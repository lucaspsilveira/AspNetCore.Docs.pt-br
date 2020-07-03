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

# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="5486e-103">Ferramentas para ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="5486e-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="5486e-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5486e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5486e-105">Selecione as ferramentas para sua plataforma:</span><span class="sxs-lookup"><span data-stu-id="5486e-105">Select the tooling for your platform:</span></span>

* <span data-ttu-id="5486e-106">Windows: selecione a guia **Visual Studio** .</span><span class="sxs-lookup"><span data-stu-id="5486e-106">Windows: Select the **Visual Studio** tab.</span></span>
* <span data-ttu-id="5486e-107">Linux: selecione a guia **Visual Studio Code** .</span><span class="sxs-lookup"><span data-stu-id="5486e-107">Linux: Select the **Visual Studio Code** tab.</span></span>
* <span data-ttu-id="5486e-108">macOS: selecione a guia **Visual Studio para Mac** .</span><span class="sxs-lookup"><span data-stu-id="5486e-108">macOS: Select the **Visual Studio for Mac** tab.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5486e-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5486e-109">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5486e-110">Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="5486e-110">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="5486e-111">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="5486e-111">Create a new project.</span></span>

1. <span data-ttu-id="5486e-112">Selecione \*\* Blazor aplicativo\*\*.</span><span class="sxs-lookup"><span data-stu-id="5486e-112">Select **Blazor App**.</span></span> <span data-ttu-id="5486e-113">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="5486e-113">Select **Next**.</span></span>

1. <span data-ttu-id="5486e-114">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="5486e-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="5486e-115">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="5486e-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5486e-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="5486e-116">Select **Create**.</span></span>

1. <span data-ttu-id="5486e-117">Para obter uma Blazor WebAssembly experiência, escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="5486e-117">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="5486e-118">Para obter uma Blazor Server experiência, escolha o modelo de \*\* Blazor Server aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="5486e-118">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="5486e-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="5486e-119">Select **Create**.</span></span>

   <span data-ttu-id="5486e-120">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="5486e-120">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="5486e-121">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5486e-121">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5486e-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5486e-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5486e-123">Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="5486e-123">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="5486e-124">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="5486e-124">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="5486e-125">Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="5486e-125">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="5486e-126">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="5486e-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="5486e-127">Para definir `debug.javascript.usePreview` como `true` usar a interface do usuário do **File**vs Code, abra  >  **Preferences**  >  **configurações** de preferências de arquivo e pesquise por `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="5486e-127">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="5486e-128">Marque a caixa de seleção para **usar o novo depurador de JavaScript em visualização para Node.js e Chrome**.</span><span class="sxs-lookup"><span data-stu-id="5486e-128">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="5486e-129">Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="5486e-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="5486e-130">Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="5486e-130">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="5486e-131">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="5486e-131">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="5486e-132">Abra a pasta `WebApplication1` no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5486e-132">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="5486e-133">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="5486e-133">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="5486e-134">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="5486e-134">Select **Yes**.</span></span>

1. <span data-ttu-id="5486e-135">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5486e-135">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5486e-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5486e-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5486e-137">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="5486e-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="5486e-138">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="5486e-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="5486e-139">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="5486e-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="5486e-140">Para obter uma Blazor WebAssembly experiência, escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="5486e-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="5486e-141">Para obter uma Blazor Server experiência, escolha o modelo de \*\* Blazor Server aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="5486e-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="5486e-142">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="5486e-142">Select **Next**.</span></span>

   <span data-ttu-id="5486e-143">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="5486e-143">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="5486e-144">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="5486e-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="5486e-145">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="5486e-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="5486e-146">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="5486e-146">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="5486e-147">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="5486e-147">Select **Next**.</span></span>

1. <span data-ttu-id="5486e-148">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="5486e-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="5486e-149">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="5486e-149">Select **Create**.</span></span>

1. <span data-ttu-id="5486e-150">Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="5486e-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="5486e-151">Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="5486e-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="5486e-152">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="5486e-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="5486e-153">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="5486e-153">The user and keychain passwords are required to trust the certificate.</span></span>

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
