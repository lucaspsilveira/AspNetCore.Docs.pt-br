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
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="2f1cd-103">Introdução ao ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="2f1cd-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="2f1cd-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2f1cd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2f1cd-105">Para começar com o mais claro, siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f1cd-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f1cd-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2f1cd-107">Para criar aplicativos de servidor mais novos, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento na Web** .</span><span class="sxs-lookup"><span data-stu-id="2f1cd-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="2f1cd-108">Para criar aplicativos de Webassembly mais novos e de servidor de última edição, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) com a **ASP.net e** a carga de trabalho de desenvolvimento para a Web.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="2f1cd-109">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*de mais incrivelmente, <xref:blazor/hosting-models>consulte.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2f1cd-110">Instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. <span data-ttu-id="2f1cd-111">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-111">Create a new project.</span></span>

1. <span data-ttu-id="2f1cd-112">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-112">Select **Blazor App**.</span></span> <span data-ttu-id="2f1cd-113">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-113">Select **Next**.</span></span>

1. <span data-ttu-id="2f1cd-114">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2f1cd-115">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2f1cd-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-116">Select **Create**.</span></span>

1. <span data-ttu-id="2f1cd-117">Para obter uma experiência de Webassembly mais experiente (Visual Studio 16,6 Preview 2 ou posterior), escolha o modelo de **aplicativo Webassembly** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2f1cd-118">Para uma experiência de servidor mais incrivelmente (Visual Studio 16,4 ou posterior), escolha o modelo de **aplicativo de servidor** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2f1cd-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-119">Select **Create**.</span></span>

1. <span data-ttu-id="2f1cd-120">Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f1cd-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f1cd-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2f1cd-122">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="2f1cd-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="2f1cd-123">Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="2f1cd-124">O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 4 mais recente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="2f1cd-125">Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="2f1cd-126">Instale o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="2f1cd-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="2f1cd-127">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true`.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="2f1cd-128">Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="2f1cd-129">Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="2f1cd-130">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2f1cd-131">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="2f1cd-132">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="2f1cd-133">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-133">Select **Yes**.</span></span>

1. <span data-ttu-id="2f1cd-134">Com o servidor de mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="2f1cd-135">Com o Webassembly de mais potentes, inicie o aplicativo usando a configuração de inicialização do **.NET Core (mais ecostandalone)** e inicie o navegador usando o **assembly da Web de depuração do .NET Core na configuração de** inicialização do Chrome (requer o Chrome).</span><span class="sxs-lookup"><span data-stu-id="2f1cd-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="2f1cd-136">Para obter mais informações, consulte <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="2f1cd-137">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f1cd-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2f1cd-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f1cd-139">Há suporte para o servidor mais incrivelmente no Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="2f1cd-140">Não há suporte para Webassembly mais incrivelmente no momento.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="2f1cd-141">Para criar aplicativos Webassembly mais elaborados no macOS, siga as orientações na guia **CLI do .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="2f1cd-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="2f1cd-142">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="2f1cd-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="2f1cd-143">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="2f1cd-144">Na barra lateral, selecione**aplicativo** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="2f1cd-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="2f1cd-145">Selecione o modelo de **aplicativo de servidor mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="2f1cd-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="2f1cd-146">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-146">Select **Create**.</span></span>

   <span data-ttu-id="2f1cd-147">Para obter informações sobre o modelo de Hospedagem de servidor mais <xref:blazor/hosting-models>incrivelmente, consulte.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2f1cd-148">Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="2f1cd-149">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="2f1cd-150">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-150">Select **Create**.</span></span>

1. <span data-ttu-id="2f1cd-151">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="2f1cd-152">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="2f1cd-153">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2f1cd-154">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f1cd-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2f1cd-155">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="2f1cd-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="2f1cd-156">Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="2f1cd-157">O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 4 mais recente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="2f1cd-158">Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="2f1cd-159">Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2f1cd-160">Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2f1cd-161">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2f1cd-162">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="2f1cd-163">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="2f1cd-164">Home</span><span class="sxs-lookup"><span data-stu-id="2f1cd-164">Home</span></span>
* <span data-ttu-id="2f1cd-165">Contador</span><span class="sxs-lookup"><span data-stu-id="2f1cd-165">Counter</span></span>
* <span data-ttu-id="2f1cd-166">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="2f1cd-166">Fetch data</span></span>

<span data-ttu-id="2f1cd-167">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2f1cd-168">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript Blazor , mas com você pode usar C#.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="2f1cd-169">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="2f1cd-170">Uma solicitação para `/counter` no navegador, conforme especificado pela `@page` diretiva na parte superior, faz com que o `Counter` componente processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="2f1cd-171">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="2f1cd-172">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="2f1cd-173">O `onclick` evento é acionado.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="2f1cd-174">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="2f1cd-175">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="2f1cd-176">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-176">The component is rendered again.</span></span>

<span data-ttu-id="2f1cd-177">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="2f1cd-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="2f1cd-178">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="2f1cd-179">Por exemplo, adicione o `Counter` componente à página inicial do aplicativo adicionando um `<Counter />` elemento ao `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="2f1cd-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="2f1cd-181">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-181">Run the app.</span></span> <span data-ttu-id="2f1cd-182">A Home Page tem seu próprio contador fornecido pelo `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="2f1cd-183">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="2f1cd-184">Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do `@code` componente:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="2f1cd-185">Adicione uma propriedade pública para `IncrementAmount` com um `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="2f1cd-186">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="2f1cd-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="2f1cd-188">Especifique o `IncrementAmount` no elemento `Index` do `<Counter>` componente usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="2f1cd-189">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2f1cd-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="2f1cd-190">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-190">Run the app.</span></span> <span data-ttu-id="2f1cd-191">O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="2f1cd-192">O `Counter` componente (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="2f1cd-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2f1cd-193">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2f1cd-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="2f1cd-194">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2f1cd-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
