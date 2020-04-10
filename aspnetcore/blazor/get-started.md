---
title: Comece com ASP.NET CoreBlazor
author: guardrex
description: Comece construindo Blazor um Blazor aplicativo com a ferramenta de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: e9e6eeeb1d29aa529e43d75f5d3951d2c4384d7e
ms.sourcegitcommit: 4506a8f71ece921010ad6b7edebc8b200618f40d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "81002906"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="89d6b-103">Comece com ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="89d6b-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="89d6b-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89d6b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="89d6b-105">Para começar com Blazor, siga as orientações para a sua escolha de ferramentaria:</span><span class="sxs-lookup"><span data-stu-id="89d6b-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89d6b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d6b-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="89d6b-107">Para criar aplicativos Blazor Server, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a carga de trabalho **de ASP.NET e desenvolvimento web.**</span><span class="sxs-lookup"><span data-stu-id="89d6b-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="89d6b-108">Para criar aplicativos Blazor Server e Blazor WebAssembly, instale a última pré-visualização do [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) com a **carga de trabalho de desenvolvimento ASP.NET e web.**</span><span class="sxs-lookup"><span data-stu-id="89d6b-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="89d6b-109">Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor WebAssembly* e *Blazor Server*, consulte .</span><span class="sxs-lookup"><span data-stu-id="89d6b-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="89d6b-110">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="89d6b-110">Create a new project.</span></span>

1. <span data-ttu-id="89d6b-111">Selecione **o aplicativo Blazor**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-111">Select **Blazor App**.</span></span> <span data-ttu-id="89d6b-112">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-112">Select **Next**.</span></span>

1. <span data-ttu-id="89d6b-113">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="89d6b-113">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="89d6b-114">Confirme se a entrada **do local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="89d6b-114">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="89d6b-115">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-115">Select **Create**.</span></span>

1. <span data-ttu-id="89d6b-116">Para uma experiência blazor WebAssembly (Visual Studio 16.6 Preview 2 ou posterior), escolha o modelo **do Aplicativo Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="89d6b-116">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="89d6b-117">Para uma experiência de Servidor Blazor (Visual Studio 16.4 ou posterior), escolha o modelo **do Aplicativo Blazor Server.**</span><span class="sxs-lookup"><span data-stu-id="89d6b-117">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="89d6b-118">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-118">Select **Create**.</span></span>

1. <span data-ttu-id="89d6b-119">Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89d6b-119">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89d6b-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89d6b-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="89d6b-121">Instale o [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="89d6b-121">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="89d6b-122">Instale opcionalmente o modelo de visualização do [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="89d6b-122">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="89d6b-123">A [versão 3.NET Core SDK 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessária** para usar o modelo 3.2 Preview 3 Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="89d6b-123">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="89d6b-124">Confirme a versão instalada do .NET `dotnet --version` Core SDK executando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="89d6b-124">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="89d6b-125">Instale [o Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="89d6b-125">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="89d6b-126">Instale a mais recente [extensão C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` set para `true`.</span><span class="sxs-lookup"><span data-stu-id="89d6b-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="89d6b-127">Para uma experiência de Servidor Blazor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="89d6b-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="89d6b-128">Para uma experiência blazor WebAssembly, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="89d6b-128">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="89d6b-129">Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor Server* e *Blazor WebAssembly*, consulte .</span><span class="sxs-lookup"><span data-stu-id="89d6b-129">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="89d6b-130">Abra a pasta *WebApplication1* no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="89d6b-130">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="89d6b-131">O IDE solicita que você adicione ativos para construir e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="89d6b-131">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="89d6b-132">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="89d6b-132">Select **Yes**.</span></span>

1. <span data-ttu-id="89d6b-133">Com o Blazor Server, execute o aplicativo usando o depurador Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="89d6b-133">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="89d6b-134">Com o Blazor WebAssembly, inicie o aplicativo usando a configuração de lançamento **do .NET Core Launch (Blazor Standalone)** e, em seguida, inicie o navegador usando o **.NET Core Debug Blazor Web Assembly na** configuração de lançamento do Chrome (requer Chrome).</span><span class="sxs-lookup"><span data-stu-id="89d6b-134">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="89d6b-135">Para obter mais informações, consulte <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="89d6b-135">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="89d6b-136">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="89d6b-136">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89d6b-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="89d6b-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="89d6b-138">Blazor Server é suportado no Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="89d6b-138">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="89d6b-139">Blazor WebAssembly não é suportado no momento.</span><span class="sxs-lookup"><span data-stu-id="89d6b-139">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="89d6b-140">Para criar aplicativos Blazor WebAssembly no macOS, siga as orientações na guia **.NET Core CLI.**</span><span class="sxs-lookup"><span data-stu-id="89d6b-140">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="89d6b-141">Instale [o Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="89d6b-141">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="89d6b-142">Selecione **'Arquivo** > **nova solução'** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-142">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="89d6b-143">Na barra lateral, selecione **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-143">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="89d6b-144">Selecione o modelo **do Aplicativo Do Servidor Blazor.**</span><span class="sxs-lookup"><span data-stu-id="89d6b-144">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="89d6b-145">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-145">Select **Create**.</span></span>

   <span data-ttu-id="89d6b-146">Para obter informações sobre o modelo <xref:blazor/hosting-models>de hospedagem do Blazor Server, consulte .</span><span class="sxs-lookup"><span data-stu-id="89d6b-146">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="89d6b-147">Defina o **Framework de destino** para **.NET Core 3.1** e selecione **Next**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-147">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="89d6b-148">No campo Nome do **projeto,** nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="89d6b-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="89d6b-149">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="89d6b-149">Select **Create**.</span></span>

1. <span data-ttu-id="89d6b-150">Selecione **Executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="89d6b-150">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="89d6b-151">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="89d6b-151">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="89d6b-152">Se um prompt aparecer para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="89d6b-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="89d6b-153">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="89d6b-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="89d6b-154">Instale o [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="89d6b-154">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="89d6b-155">Instale opcionalmente o modelo de visualização do [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="89d6b-155">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="89d6b-156">A [versão 3.NET Core SDK 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessária** para usar o modelo 3.2 Preview 3 Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="89d6b-156">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="89d6b-157">Confirme a versão instalada do .NET `dotnet --version` Core SDK executando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="89d6b-157">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="89d6b-158">Para uma experiência de Servidor Blazor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="89d6b-158">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="89d6b-159">Para uma experiência blazor WebAssembly, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="89d6b-159">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="89d6b-160">Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor Server* e *Blazor WebAssembly*, consulte .</span><span class="sxs-lookup"><span data-stu-id="89d6b-160">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="89d6b-161">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="89d6b-161">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="89d6b-162">Várias páginas estão disponíveis nas guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="89d6b-162">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="89d6b-163">Home</span><span class="sxs-lookup"><span data-stu-id="89d6b-163">Home</span></span>
* <span data-ttu-id="89d6b-164">Contador</span><span class="sxs-lookup"><span data-stu-id="89d6b-164">Counter</span></span>
* <span data-ttu-id="89d6b-165">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="89d6b-165">Fetch data</span></span>

<span data-ttu-id="89d6b-166">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="89d6b-166">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="89d6b-167">Incrementar um contador em uma página da Web Blazor normalmente requer escrever JavaScript, mas com você pode usar C#.</span><span class="sxs-lookup"><span data-stu-id="89d6b-167">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="89d6b-168">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="89d6b-168">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="89d6b-169">Uma solicitação `/counter` no navegador, conforme especificado pela `@page` diretiva na `Counter` parte superior, faz com que o componente torne seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="89d6b-169">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="89d6b-170">Os componentes se transformam em uma representação na memória da árvore de renderização que pode ser usada para atualizar a iu de forma flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="89d6b-170">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="89d6b-171">Cada vez que o botão **Clique em mim** é selecionado:</span><span class="sxs-lookup"><span data-stu-id="89d6b-171">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="89d6b-172">O `onclick` evento foi disparado.</span><span class="sxs-lookup"><span data-stu-id="89d6b-172">The `onclick` event is fired.</span></span>
* <span data-ttu-id="89d6b-173">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="89d6b-173">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="89d6b-174">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="89d6b-174">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="89d6b-175">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="89d6b-175">The component is rendered again.</span></span>

<span data-ttu-id="89d6b-176">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado ao DoM (Document Object Model).</span><span class="sxs-lookup"><span data-stu-id="89d6b-176">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="89d6b-177">Adicione um componente a outro componente usando sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="89d6b-177">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="89d6b-178">Por exemplo, `Counter` adicione o componente à página inicial `<Counter />` do `Index` aplicativo adicionando um elemento ao componente.</span><span class="sxs-lookup"><span data-stu-id="89d6b-178">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="89d6b-179">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="89d6b-179">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="89d6b-180">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89d6b-180">Run the app.</span></span> <span data-ttu-id="89d6b-181">A página inicial tem seu próprio `Counter` contador fornecido pelo componente.</span><span class="sxs-lookup"><span data-stu-id="89d6b-181">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="89d6b-182">Os parâmetros dos componentes são especificados usando atributos ou [conteúdo infantil,](xref:blazor/components#child-content)que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="89d6b-182">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="89d6b-183">Para adicionar um parâmetro `Counter` ao componente, atualize o bloco do `@code` componente:</span><span class="sxs-lookup"><span data-stu-id="89d6b-183">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="89d6b-184">Adicione uma propriedade `IncrementAmount` pública `[Parameter]` para com um atributo.</span><span class="sxs-lookup"><span data-stu-id="89d6b-184">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="89d6b-185">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="89d6b-185">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="89d6b-186">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="89d6b-186">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="89d6b-187">Especifique o `IncrementAmount` `<Counter>` elemento no `Index` componente usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="89d6b-187">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="89d6b-188">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="89d6b-188">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="89d6b-189">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89d6b-189">Run the app.</span></span> <span data-ttu-id="89d6b-190">O `Index` componente tem seu próprio contador que incrementa por dez cada vez que o botão **Clique-me** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="89d6b-190">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="89d6b-191">O `Counter` componente *(Counter.razor)* continua `/counter` a incrementar por um.</span><span class="sxs-lookup"><span data-stu-id="89d6b-191">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="89d6b-192">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="89d6b-192">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="89d6b-193">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="89d6b-193">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
