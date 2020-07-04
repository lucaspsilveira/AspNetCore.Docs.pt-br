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
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946816"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Ferramentas para ASP.NET CoreBlazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

::: zone pivot="os-windows"

1. Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

1. Criar um novo projeto.

1. Selecione ** Blazor aplicativo**. Selecione **Avançar**.

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Para obter uma Blazor WebAssembly experiência, escolha o modelo de ** Blazor WebAssembly aplicativo** . Para obter uma Blazor Server experiência, escolha o modelo de ** Blazor Server aplicativo** . Selecione **Criar**.

   Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .

1. Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.

::: zone-end

::: zone pivot="os-linux"

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

::: zone-end

::: zone pivot="os-macos"

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

::: zone-end
