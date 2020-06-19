---
title: Introdução ao ASP.NET CoreBlazor
author: guardrex
description: Comece com Blazor a criação de um Blazor aplicativo com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: c90c3bf7ccef420101c66fe04d579920209b066c
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102333"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introdução ao ASP.NET CoreBlazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Para começar a usar Blazor o, siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

1. Criar um novo projeto.

1. Selecione ** Blazor aplicativo**. Selecione **Avançar**.

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Para uma Blazor experiência de Webassembly, escolha o modelo de ** Blazor aplicativo Webassembly** . Para uma Blazor experiência de servidor, escolha o modelo de ** Blazor aplicativo de servidor** . Selecione **Criar**.

   Para obter informações sobre os dois Blazor modelos de hospedagem, * Blazor Webassembly* e * Blazor Server*, consulte <xref:blazor/hosting-models> .

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet --version
   ```

1. Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/).

1. Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .

  Para definir `debug.javascript.usePreview` como `true` usar a interface do usuário do **File**vs Code, abra  >  **Preferences**  >  **configurações** de preferências de arquivo e pesquise por `debug javascript use preview` . Marque a caixa de seleção para **usar o novo depurador de JavaScript em visualização para Node.js e Chrome**.

1. Para uma Blazor experiência de Webassembly, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para uma Blazor experiência de servidor, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para obter informações sobre os dois Blazor modelos de hospedagem, * Blazor Webassembly* e * Blazor Server*, consulte <xref:blazor/hosting-models> .

1. Abra a pasta *WebApplication1* em Visual Studio Code.

1. O IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim** na barra superior.

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.

1. Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.

   Para uma Blazor experiência de Webassembly, escolha o modelo de ** Blazor aplicativo Webassembly** . Para uma Blazor experiência de servidor, escolha o modelo de ** Blazor aplicativo de servidor** . Selecione **Avançar**.

   Para obter informações sobre os dois Blazor modelos de hospedagem, * Blazor Webassembly* e * Blazor Server*, consulte <xref:blazor/hosting-models> .

1. Confirme as seguintes configurações:

   * **Estrutura de destino** definida como **.NET Core 3,1**.
   * **Autenticação** definida como **sem autenticação**.
   
   Selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` . Selecione **Criar**.

1. Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.

Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet --version
   ```

1. Para uma Blazor experiência de Webassembly, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para uma Blazor experiência de servidor, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter informações sobre os dois Blazor modelos de hospedagem, * Blazor Webassembly* e * Blazor Server*, consulte <xref:blazor/hosting-models> .

1. Em um navegador, navegue até `https://localhost:5001`.

---

Várias páginas estão disponíveis em guias na barra lateral:

* Página Inicial
* Contador
* Buscar dados

Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor você pode usar C#.

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

Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components/index#child-content), que permitem definir propriedades no componente filho. Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do componente `@code` :

* Adicione uma propriedade pública para `IncrementAmount` com um [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo.
* Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique o `IncrementAmount` no `Index` elemento do componente `<Counter>` usando um atributo.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Execute o aplicativo. O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado. O `Counter` componente (*Counter. Razor*) em `/counter` continua a incrementar um.

## <a name="next-steps"></a>Próximas etapas

Compilar um Blazor aplicativo passo a passo:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/templates>
* <xref:signalr/introduction>
