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
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="f600c-103">Ferramentas para ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="f600c-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="f600c-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f600c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="f600c-105">Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="f600c-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="f600c-106">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="f600c-106">Create a new project.</span></span>

1. <span data-ttu-id="f600c-107">Selecione \*\* Blazor aplicativo\*\*.</span><span class="sxs-lookup"><span data-stu-id="f600c-107">Select **Blazor App**.</span></span> <span data-ttu-id="f600c-108">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f600c-108">Select **Next**.</span></span>

1. <span data-ttu-id="f600c-109">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="f600c-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f600c-110">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="f600c-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f600c-111">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f600c-111">Select **Create**.</span></span>

1. <span data-ttu-id="f600c-112">Para obter uma Blazor WebAssembly experiência, escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="f600c-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f600c-113">Para obter uma Blazor Server experiência, escolha o modelo de \*\* Blazor Server aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="f600c-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f600c-114">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f600c-114">Select **Create**.</span></span>

   <span data-ttu-id="f600c-115">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="f600c-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f600c-116">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f600c-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="f600c-117">Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="f600c-117">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="f600c-118">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f600c-118">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="f600c-119">Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="f600c-119">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="f600c-120">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="f600c-120">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="f600c-121">Para definir `debug.javascript.usePreview` como `true` usar a interface do usuário do **File**vs Code, abra  >  **Preferences**  >  **configurações** de preferências de arquivo e pesquise por `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="f600c-121">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="f600c-122">Marque a caixa de seleção para **usar o novo depurador de JavaScript em visualização para Node.js e Chrome**.</span><span class="sxs-lookup"><span data-stu-id="f600c-122">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="f600c-123">Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f600c-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f600c-124">Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f600c-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f600c-125">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="f600c-125">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f600c-126">Abra a pasta `WebApplication1` no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f600c-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f600c-127">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="f600c-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f600c-128">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="f600c-128">Select **Yes**.</span></span>

1. <span data-ttu-id="f600c-129">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f600c-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="f600c-130">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="f600c-130">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f600c-131">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="f600c-131">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f600c-132">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="f600c-132">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="f600c-133">Para obter uma Blazor WebAssembly experiência, escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="f600c-133">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f600c-134">Para obter uma Blazor Server experiência, escolha o modelo de \*\* Blazor Server aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="f600c-134">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f600c-135">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f600c-135">Select **Next**.</span></span>

   <span data-ttu-id="f600c-136">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="f600c-136">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f600c-137">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="f600c-137">Confirm the following configurations:</span></span>

   * <span data-ttu-id="f600c-138">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="f600c-138">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="f600c-139">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="f600c-139">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="f600c-140">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f600c-140">Select **Next**.</span></span>

1. <span data-ttu-id="f600c-141">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="f600c-141">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f600c-142">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f600c-142">Select **Create**.</span></span>

1. <span data-ttu-id="f600c-143">Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="f600c-143">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f600c-144">Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="f600c-144">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="f600c-145">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="f600c-145">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f600c-146">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="f600c-146">The user and keychain passwords are required to trust the certificate.</span></span>

::: zone-end
