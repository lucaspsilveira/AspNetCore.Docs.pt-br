---
title: Ferramentas para ASP.NET CoreBlazor
author: guardrex
description: Saiba mais sobre as ferramentas disponíveis para criar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
ms.openlocfilehash: 3ff610225c798624b7ead7d365924ae3d193829d
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944933"
---
<!-- zone_pivot_groups: operating-systems -->

# <a name="tooling-for-aspnet-core-blazor"></a>Ferramentas para ASP.NET CoreBlazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Selecione as ferramentas para sua plataforma:

* Windows: selecione a guia **Visual Studio** .
* Linux: selecione a guia **Visual Studio Code** .
* macOS: selecione a guia **Visual Studio para Mac** .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

1. Criar um novo projeto.

1. Selecione ** Blazor aplicativo**. Selecione **Avançar**.

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Para obter uma Blazor WebAssembly experiência, escolha o modelo de ** Blazor WebAssembly aplicativo** . Para obter uma Blazor Server experiência, escolha o modelo de ** Blazor Server aplicativo** . Selecione **Criar**.

   Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet --version
   ```

1. Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/).

1. Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .

   Para definir `debug.javascript.usePreview` como `true` usar a interface do usuário do **File**vs Code, abra  >  **Preferences**  >  **configurações** de preferências de arquivo e pesquise por `debug javascript use preview` . Marque a caixa de seleção para **usar o novo depurador de JavaScript em visualização para Node.js e Chrome**.

1. Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Abra a pasta `WebApplication1` no Visual Studio Code.

1. O IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim** na barra superior.

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.

1. Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.

   Para obter uma Blazor WebAssembly experiência, escolha o modelo de ** Blazor WebAssembly aplicativo** . Para obter uma Blazor Server experiência, escolha o modelo de ** Blazor Server aplicativo** . Selecione **Avançar**.

   Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Confirme as seguintes configurações:

   * **Estrutura de destino** definida como **.NET Core 3,1**.
   * **Autenticação** definida como **sem autenticação**.
   
   Selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` . Selecione **Criar**.

1. Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.

Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.

---

<!--

::: zone pivot="os-windows"

1. Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.

1. Create a new project.

1. Select **Blazor App**. Select **Next**.

1. Provide a project name in the **Project name** field or accept the default project name. Confirm the **Location** entry is correct or provide a location for the project. Select **Create**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Create**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-linux"

1. Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:

   ```dotnetcli
   dotnet --version
   ```

1. Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).

1. Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.

   To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`. Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.

1. For a Blazor WebAssembly experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Open the `WebApplication1` folder in Visual Studio Code.

1. The IDE requests that you add assets to build and debug the project. Select **Yes**.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-macos"

1. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

   For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

If a prompt appears to trust the development certificate, trust the certificate and continue. The user and keychain passwords are required to trust the certificate.

::: zone-end

-->
