---
title: Introdução ao ASP.NET CoreBlazor
author: guardrex
description: Comece com Blazor a criação de um Blazor aplicativo com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 6cf1de6e68d04505ce4ba5d18f2a7d0bbe5be333
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727652"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="3bd5e-103">Introdução ao ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="3bd5e-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="3bd5e-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3bd5e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3bd5e-105">Para começar com o mais claro, siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3bd5e-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bd5e-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3bd5e-107">Para criar aplicativos de servidor mais novos, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento na Web** .</span><span class="sxs-lookup"><span data-stu-id="3bd5e-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="3bd5e-108">Para criar aplicativos de Webassembly mais novos e de servidor de última edição, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) com a **ASP.net e** a carga de trabalho de desenvolvimento para a Web.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="3bd5e-109">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*de mais incrivelmente, <xref:blazor/hosting-models>consulte.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3bd5e-110">Instale o modelo de visualização do Webassembly mais incrivelmente executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="3bd5e-111">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-111">Create a new project.</span></span>

1. <span data-ttu-id="3bd5e-112">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-112">Select **Blazor App**.</span></span> <span data-ttu-id="3bd5e-113">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-113">Select **Next**.</span></span>

1. <span data-ttu-id="3bd5e-114">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3bd5e-115">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3bd5e-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-116">Select **Create**.</span></span>

1. <span data-ttu-id="3bd5e-117">Para obter uma experiência de Webassembly mais experiente (Visual Studio 16,6 Preview 2 ou posterior), escolha o modelo de **aplicativo Webassembly** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3bd5e-118">Para uma experiência de servidor mais incrivelmente (Visual Studio 16,4 ou posterior), escolha o modelo de **aplicativo de servidor** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3bd5e-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-119">Select **Create**.</span></span>

1. <span data-ttu-id="3bd5e-120">Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3bd5e-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bd5e-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3bd5e-122">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3bd5e-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="3bd5e-123">Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="3bd5e-124">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*de mais incrivelmente, <xref:blazor/hosting-models>consulte.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3bd5e-125">O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview mais recente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="3bd5e-126">Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="3bd5e-127">Instale o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="3bd5e-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="3bd5e-128">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true`.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="3bd5e-129">Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="3bd5e-130">Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="3bd5e-131">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="3bd5e-132">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3bd5e-133">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-133">Select **Yes**.</span></span>

1. <span data-ttu-id="3bd5e-134">Com o servidor de mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="3bd5e-135">Com o Webassembly de mais potentes, inicie o aplicativo usando a configuração de inicialização do **.NET Core (mais ecostandalone)** e inicie o navegador usando o **assembly da Web de depuração do .NET Core na configuração de** inicialização do Chrome (requer o Chrome).</span><span class="sxs-lookup"><span data-stu-id="3bd5e-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="3bd5e-136">Para obter mais informações, consulte <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="3bd5e-137">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3bd5e-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3bd5e-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3bd5e-139">Há suporte para o servidor mais incrivelmente no Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="3bd5e-140">Não há suporte para Webassembly mais incrivelmente no momento.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="3bd5e-141">Para criar aplicativos Webassembly mais incrivelmente no macOS, siga as orientações na guia **CLI do .NET Core** . Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*de mais incrivelmente, <xref:blazor/hosting-models>consulte.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3bd5e-142">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3bd5e-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="3bd5e-143">Selecione **arquivo** > **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3bd5e-144">Na barra lateral, selecione**aplicativo** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="3bd5e-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="3bd5e-145">Selecione o modelo de **aplicativo de servidor mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="3bd5e-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="3bd5e-146">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-146">Select **Next**.</span></span>

1. <span data-ttu-id="3bd5e-147">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="3bd5e-148">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="3bd5e-149">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="3bd5e-150">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-150">Select **Next**.</span></span>

1. <span data-ttu-id="3bd5e-151">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3bd5e-152">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-152">Select **Create**.</span></span>

1. <span data-ttu-id="3bd5e-153">Selecione **executar** > **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3bd5e-154">Não há suporte para a depuração no momento.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="3bd5e-155">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3bd5e-156">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3bd5e-157">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="3bd5e-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3bd5e-158">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3bd5e-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="3bd5e-159">Opcionalmente, instale o modelo de visualização do Webassembly mais incrivelmente executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="3bd5e-160">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*de mais incrivelmente, <xref:blazor/hosting-models>consulte.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3bd5e-161">O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview mais recente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="3bd5e-162">Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="3bd5e-163">Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3bd5e-164">Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="3bd5e-165">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="3bd5e-166">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="3bd5e-167">Início</span><span class="sxs-lookup"><span data-stu-id="3bd5e-167">Home</span></span>
* <span data-ttu-id="3bd5e-168">Contador</span><span class="sxs-lookup"><span data-stu-id="3bd5e-168">Counter</span></span>
* <span data-ttu-id="3bd5e-169">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="3bd5e-169">Fetch data</span></span>

<span data-ttu-id="3bd5e-170">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="3bd5e-171">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript Blazor , mas com você pode usar C#.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="3bd5e-172">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="3bd5e-173">Uma solicitação para `/counter` no navegador, conforme especificado pela `@page` diretiva na parte superior, faz com que o `Counter` componente processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="3bd5e-174">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="3bd5e-175">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="3bd5e-176">O `onclick` evento é acionado.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="3bd5e-177">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="3bd5e-178">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="3bd5e-179">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-179">The component is rendered again.</span></span>

<span data-ttu-id="3bd5e-180">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="3bd5e-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="3bd5e-181">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="3bd5e-182">Por exemplo, adicione o `Counter` componente à página inicial do aplicativo adicionando um `<Counter />` elemento ao `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="3bd5e-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="3bd5e-184">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-184">Run the app.</span></span> <span data-ttu-id="3bd5e-185">A Home Page tem seu próprio contador fornecido pelo `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="3bd5e-186">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="3bd5e-187">Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do `@code` componente:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="3bd5e-188">Adicione uma propriedade pública para `IncrementAmount` com um `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="3bd5e-189">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="3bd5e-190">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="3bd5e-191">Especifique o `IncrementAmount` no elemento `Index` do `<Counter>` componente usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="3bd5e-192">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="3bd5e-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="3bd5e-193">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-193">Run the app.</span></span> <span data-ttu-id="3bd5e-194">O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="3bd5e-195">O `Counter` componente (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="3bd5e-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3bd5e-196">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3bd5e-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="3bd5e-197">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3bd5e-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
