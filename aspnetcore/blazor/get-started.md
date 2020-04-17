---
title: Comece com ASP.NET CoreBlazor
author: guardrex
description: Comece construindo Blazor um Blazor aplicativo com a ferramenta de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 7fe4fbb082f08d4f71684c836a826d8b6dd888f6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488722"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="b8e45-103">Comece com ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b8e45-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="b8e45-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8e45-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b8e45-105">Para começar com Blazor, siga as orientações para a sua escolha de ferramentaria:</span><span class="sxs-lookup"><span data-stu-id="b8e45-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8e45-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8e45-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b8e45-107">Para criar aplicativos Blazor Server, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a carga de trabalho **de ASP.NET e desenvolvimento web.**</span><span class="sxs-lookup"><span data-stu-id="b8e45-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b8e45-108">Para criar aplicativos Blazor Server e Blazor WebAssembly, instale a última pré-visualização do [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) com a **carga de trabalho de desenvolvimento ASP.NET e web.**</span><span class="sxs-lookup"><span data-stu-id="b8e45-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b8e45-109">Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor WebAssembly* e *Blazor Server*, consulte .</span><span class="sxs-lookup"><span data-stu-id="b8e45-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b8e45-110">Instale o modelo de visualização [do Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b8e45-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

1. <span data-ttu-id="b8e45-111">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="b8e45-111">Create a new project.</span></span>

1. <span data-ttu-id="b8e45-112">Selecione **o aplicativo Blazor**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-112">Select **Blazor App**.</span></span> <span data-ttu-id="b8e45-113">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-113">Select **Next**.</span></span>

1. <span data-ttu-id="b8e45-114">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="b8e45-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b8e45-115">Confirme se a entrada **do local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="b8e45-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b8e45-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-116">Select **Create**.</span></span>

1. <span data-ttu-id="b8e45-117">Para uma experiência blazor WebAssembly (Visual Studio 16.6 Preview 2 ou posterior), escolha o modelo **do Aplicativo Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="b8e45-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b8e45-118">Para uma experiência de Servidor Blazor (Visual Studio 16.4 ou posterior), escolha o modelo **do Aplicativo Blazor Server.**</span><span class="sxs-lookup"><span data-stu-id="b8e45-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b8e45-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-119">Select **Create**.</span></span>

1. <span data-ttu-id="b8e45-120">Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b8e45-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8e45-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8e45-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b8e45-122">Instale o [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b8e45-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b8e45-123">Instale opcionalmente o modelo de visualização do [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b8e45-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="b8e45-124">A [versão 3.NET Core SDK 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessária** para usar o modelo 3.2 Preview 4 Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b8e45-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="b8e45-125">Confirme a versão instalada do .NET `dotnet --version` Core SDK executando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="b8e45-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b8e45-126">Instale [o Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="b8e45-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="b8e45-127">Instale a mais recente [extensão C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` set para `true`.</span><span class="sxs-lookup"><span data-stu-id="b8e45-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="b8e45-128">Para uma experiência de Servidor Blazor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b8e45-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="b8e45-129">Para uma experiência blazor WebAssembly, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b8e45-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="b8e45-130">Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor Server* e *Blazor WebAssembly*, consulte .</span><span class="sxs-lookup"><span data-stu-id="b8e45-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b8e45-131">Abra a pasta *WebApplication1* no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b8e45-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="b8e45-132">O IDE solicita que você adicione ativos para construir e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="b8e45-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b8e45-133">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="b8e45-133">Select **Yes**.</span></span>

1. <span data-ttu-id="b8e45-134">Com o Blazor Server, execute o aplicativo usando o depurador Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b8e45-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="b8e45-135">Com o Blazor WebAssembly, inicie o aplicativo usando a configuração de lançamento **do .NET Core Launch (Blazor Standalone)** e, em seguida, inicie o navegador usando o **.NET Core Debug Blazor Web Assembly na** configuração de lançamento do Chrome (requer Chrome).</span><span class="sxs-lookup"><span data-stu-id="b8e45-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="b8e45-136">Para obter mais informações, consulte <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="b8e45-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="b8e45-137">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b8e45-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b8e45-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b8e45-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b8e45-139">Blazor Server é suportado no Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="b8e45-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="b8e45-140">Blazor WebAssembly não é suportado no momento.</span><span class="sxs-lookup"><span data-stu-id="b8e45-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="b8e45-141">Para criar aplicativos Blazor WebAssembly no macOS, siga as orientações na guia **.NET Core CLI.**</span><span class="sxs-lookup"><span data-stu-id="b8e45-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="b8e45-142">Instale [o Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="b8e45-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="b8e45-143">Selecione **'Arquivo** > **nova solução'** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="b8e45-144">Na barra lateral, selecione **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="b8e45-145">Selecione o modelo **do Aplicativo Do Servidor Blazor.**</span><span class="sxs-lookup"><span data-stu-id="b8e45-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="b8e45-146">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-146">Select **Create**.</span></span>

   <span data-ttu-id="b8e45-147">Para obter informações sobre o modelo <xref:blazor/hosting-models>de hospedagem do Blazor Server, consulte .</span><span class="sxs-lookup"><span data-stu-id="b8e45-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b8e45-148">Defina o **Framework de destino** para **.NET Core 3.1** e selecione **Next**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="b8e45-149">No campo Nome do **projeto,** nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="b8e45-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="b8e45-150">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b8e45-150">Select **Create**.</span></span>

1. <span data-ttu-id="b8e45-151">Selecione **Executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="b8e45-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="b8e45-152">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="b8e45-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="b8e45-153">Se um prompt aparecer para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="b8e45-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b8e45-154">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b8e45-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b8e45-155">Instale o [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b8e45-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b8e45-156">Instale opcionalmente o modelo de visualização do [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b8e45-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="b8e45-157">A [versão 3.NET Core SDK 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessária** para usar o modelo 3.2 Preview 4 Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b8e45-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="b8e45-158">Confirme a versão instalada do .NET `dotnet --version` Core SDK executando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="b8e45-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b8e45-159">Para uma experiência de Servidor Blazor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b8e45-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b8e45-160">Para uma experiência blazor WebAssembly, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b8e45-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b8e45-161">Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor Server* e *Blazor WebAssembly*, consulte .</span><span class="sxs-lookup"><span data-stu-id="b8e45-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b8e45-162">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b8e45-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="b8e45-163">Várias páginas estão disponíveis nas guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="b8e45-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="b8e45-164">Home</span><span class="sxs-lookup"><span data-stu-id="b8e45-164">Home</span></span>
* <span data-ttu-id="b8e45-165">Contador</span><span class="sxs-lookup"><span data-stu-id="b8e45-165">Counter</span></span>
* <span data-ttu-id="b8e45-166">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="b8e45-166">Fetch data</span></span>

<span data-ttu-id="b8e45-167">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="b8e45-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b8e45-168">Incrementar um contador em uma página da Web Blazor normalmente requer escrever JavaScript, mas com você pode usar C#.</span><span class="sxs-lookup"><span data-stu-id="b8e45-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="b8e45-169">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="b8e45-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="b8e45-170">Uma solicitação `/counter` no navegador, conforme especificado pela `@page` diretiva na `Counter` parte superior, faz com que o componente torne seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="b8e45-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="b8e45-171">Os componentes se transformam em uma representação na memória da árvore de renderização que pode ser usada para atualizar a iu de forma flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="b8e45-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="b8e45-172">Cada vez que o botão **Clique em mim** é selecionado:</span><span class="sxs-lookup"><span data-stu-id="b8e45-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="b8e45-173">O `onclick` evento foi disparado.</span><span class="sxs-lookup"><span data-stu-id="b8e45-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="b8e45-174">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="b8e45-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="b8e45-175">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="b8e45-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="b8e45-176">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="b8e45-176">The component is rendered again.</span></span>

<span data-ttu-id="b8e45-177">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado ao DoM (Document Object Model).</span><span class="sxs-lookup"><span data-stu-id="b8e45-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="b8e45-178">Adicione um componente a outro componente usando sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="b8e45-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="b8e45-179">Por exemplo, `Counter` adicione o componente à página inicial `<Counter />` do `Index` aplicativo adicionando um elemento ao componente.</span><span class="sxs-lookup"><span data-stu-id="b8e45-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="b8e45-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="b8e45-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="b8e45-181">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b8e45-181">Run the app.</span></span> <span data-ttu-id="b8e45-182">A página inicial tem seu próprio `Counter` contador fornecido pelo componente.</span><span class="sxs-lookup"><span data-stu-id="b8e45-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="b8e45-183">Os parâmetros dos componentes são especificados usando atributos ou [conteúdo infantil,](xref:blazor/components#child-content)que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="b8e45-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="b8e45-184">Para adicionar um parâmetro `Counter` ao componente, atualize o bloco do `@code` componente:</span><span class="sxs-lookup"><span data-stu-id="b8e45-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="b8e45-185">Adicione uma propriedade `IncrementAmount` pública `[Parameter]` para com um atributo.</span><span class="sxs-lookup"><span data-stu-id="b8e45-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="b8e45-186">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b8e45-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="b8e45-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="b8e45-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="b8e45-188">Especifique o `IncrementAmount` `<Counter>` elemento no `Index` componente usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="b8e45-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="b8e45-189">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="b8e45-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="b8e45-190">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b8e45-190">Run the app.</span></span> <span data-ttu-id="b8e45-191">O `Index` componente tem seu próprio contador que incrementa por dez cada vez que o botão **Clique-me** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="b8e45-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="b8e45-192">O `Counter` componente *(Counter.razor)* continua `/counter` a incrementar por um.</span><span class="sxs-lookup"><span data-stu-id="b8e45-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b8e45-193">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b8e45-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="b8e45-194">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b8e45-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
