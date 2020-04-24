---
title: Introdução ao ASP.NET CoreBlazor
author: guardrex
description: Comece com Blazor a criação de um Blazor aplicativo com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111065"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introdução ao ASP.NET Core mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Para começar com o mais claro, siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Para criar aplicativos de servidor mais novos, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento na Web** .

   Para criar aplicativos de Webassembly mais novos e de servidor de última edição, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) com a **ASP.net e** a carga de trabalho de desenvolvimento para a Web.

   Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*de mais incrivelmente, <xref:blazor/hosting-models>consulte.

1. Instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Criar um novo projeto.

1. Selecione **aplicativo mais incrivelmente**. Selecione **Avançar**.

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Para obter uma experiência de Webassembly mais experiente (Visual Studio 16,6 Preview 2 ou posterior), escolha o modelo de **aplicativo Webassembly** mais experiente. Para uma experiência de servidor mais incrivelmente (Visual Studio 16,4 ou posterior), escolha o modelo de **aplicativo de servidor** mais experiente. Selecione **Criar**.

1. Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 4 mais recente. Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.

1. Instale o [Visual Studio Code](https://code.visualstudio.com/).

1. Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true`.

1. Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

1. Abra a pasta *WebApplication1* em Visual Studio Code.

1. O IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim** na barra superior.

1. Com o servidor de mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code.

   Com o Webassembly de mais potentes, inicie o aplicativo usando a configuração de inicialização do **.NET Core (mais ecostandalone)** e inicie o navegador usando o **assembly da Web de depuração do .NET Core na configuração de** inicialização do Chrome (requer o Chrome). Para obter mais informações, consulte <xref:blazor/debug#visual-studio-code>.

1. Em um navegador, navegue até `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Há suporte para o servidor mais incrivelmente no Visual Studio para Mac. Não há suporte para Webassembly mais incrivelmente no momento. Para criar aplicativos Webassembly mais elaborados no macOS, siga as orientações na guia **CLI do .NET Core** .

1. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.

1. Na barra lateral, selecione**aplicativo** **.NET Core** > .

1. Selecione o modelo de **aplicativo de servidor mais incrivelmente** . Selecione **Criar**.

   Para obter informações sobre o modelo de Hospedagem de servidor mais <xref:blazor/hosting-models>incrivelmente, consulte.

1. Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`. Selecione **Criar**.

1. Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.

Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 4 mais recente. Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.

1. Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

1. Em um navegador, navegue até `https://localhost:5001`.

---

Várias páginas estão disponíveis em guias na barra lateral:

* Home
* Contador
* Buscar dados

Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript Blazor , mas com você pode usar C#.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Uma solicitação para `/counter` no navegador, conforme especificado pela `@page` diretiva na parte superior, faz com que o `Counter` componente processe seu conteúdo. Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.

Sempre que o botão **clicar em mim** estiver selecionado:

* O `onclick` evento é acionado.
* O método `IncrementCount` é chamado.
* O `currentCount` é incrementado.
* O componente é renderizado novamente.

O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).

Adicione um componente a outro componente usando a sintaxe HTML. Por exemplo, adicione o `Counter` componente à página inicial do aplicativo adicionando um `<Counter />` elemento ao `Index` componente.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Execute o aplicativo. A Home Page tem seu próprio contador fornecido pelo `Counter` componente.

Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho. Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do `@code` componente:

* Adicione uma propriedade pública para `IncrementAmount` com um `[Parameter]` atributo.
* Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique o `IncrementAmount` no elemento `Index` do `<Counter>` componente usando um atributo.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Execute o aplicativo. O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado. O `Counter` componente (*Counter. Razor*) em `/counter` continua a incrementar um.

## <a name="next-steps"></a>Próximas etapas

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/templates>
* <xref:signalr/introduction>
