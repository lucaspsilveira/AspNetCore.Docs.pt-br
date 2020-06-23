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
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243597"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="b2700-103">Introdução ao ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="b2700-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="b2700-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b2700-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b2700-105">Para começar a usar Blazor o, siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="b2700-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2700-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2700-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b2700-107">Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="b2700-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="b2700-108">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="b2700-108">Create a new project.</span></span>

1. <span data-ttu-id="b2700-109">Selecione \*\* Blazor aplicativo\*\*.</span><span class="sxs-lookup"><span data-stu-id="b2700-109">Select **Blazor App**.</span></span> <span data-ttu-id="b2700-110">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="b2700-110">Select **Next**.</span></span>

1. <span data-ttu-id="b2700-111">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="b2700-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b2700-112">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="b2700-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b2700-113">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b2700-113">Select **Create**.</span></span>

1. <span data-ttu-id="b2700-114">Para uma Blazor experiência de Webassembly, escolha o modelo de \*\* Blazor aplicativo Webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="b2700-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b2700-115">Para uma Blazor experiência de servidor, escolha o modelo de \*\* Blazor aplicativo de servidor\*\* .</span><span class="sxs-lookup"><span data-stu-id="b2700-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b2700-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b2700-116">Select **Create**.</span></span>

   <span data-ttu-id="b2700-117">Para obter informações sobre os dois Blazor modelos de hospedagem, \* Blazor Webassembly\* e \* Blazor Server\*, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="b2700-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b2700-118">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2700-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2700-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2700-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b2700-120">Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b2700-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="b2700-121">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b2700-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="b2700-122">Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="b2700-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="b2700-123">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="b2700-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="b2700-124">Para definir `debug.javascript.usePreview` como `true` usar a interface do usuário do **File**vs Code, abra  >  **Preferences**  >  **configurações** de preferências de arquivo e pesquise por `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="b2700-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="b2700-125">Marque a caixa de seleção para **usar o novo depurador de JavaScript em visualização para Node.js e Chrome**.</span><span class="sxs-lookup"><span data-stu-id="b2700-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="b2700-126">Para uma Blazor experiência de Webassembly, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b2700-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="b2700-127">Para uma Blazor experiência de servidor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b2700-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="b2700-128">Para obter informações sobre os dois Blazor modelos de hospedagem, \* Blazor Webassembly\* e \* Blazor Server\*, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="b2700-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b2700-129">Abra a pasta `WebApplication1` no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b2700-129">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="b2700-130">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="b2700-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b2700-131">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="b2700-131">Select **Yes**.</span></span>

1. <span data-ttu-id="b2700-132">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2700-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2700-133">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b2700-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b2700-134">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="b2700-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="b2700-135">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="b2700-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="b2700-136">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="b2700-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="b2700-137">Para uma Blazor experiência de Webassembly, escolha o modelo de \*\* Blazor aplicativo Webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="b2700-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b2700-138">Para uma Blazor experiência de servidor, escolha o modelo de \*\* Blazor aplicativo de servidor\*\* .</span><span class="sxs-lookup"><span data-stu-id="b2700-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b2700-139">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="b2700-139">Select **Next**.</span></span>

   <span data-ttu-id="b2700-140">Para obter informações sobre os dois Blazor modelos de hospedagem, \* Blazor Webassembly\* e \* Blazor Server\*, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="b2700-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b2700-141">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="b2700-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="b2700-142">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="b2700-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="b2700-143">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="b2700-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="b2700-144">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="b2700-144">Select **Next**.</span></span>

1. <span data-ttu-id="b2700-145">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="b2700-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="b2700-146">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b2700-146">Select **Create**.</span></span>

1. <span data-ttu-id="b2700-147">Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="b2700-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="b2700-148">Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="b2700-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="b2700-149">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="b2700-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="b2700-150">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="b2700-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b2700-151">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b2700-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b2700-152">Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b2700-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="b2700-153">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b2700-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="b2700-154">Para uma Blazor experiência de Webassembly, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b2700-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b2700-155">Para uma Blazor experiência de servidor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b2700-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b2700-156">Para obter informações sobre os dois Blazor modelos de hospedagem, \* Blazor Webassembly\* e \* Blazor Server\*, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="b2700-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b2700-157">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b2700-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="b2700-158">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="b2700-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="b2700-159">Página Inicial</span><span class="sxs-lookup"><span data-stu-id="b2700-159">Home</span></span>
* <span data-ttu-id="b2700-160">Contador</span><span class="sxs-lookup"><span data-stu-id="b2700-160">Counter</span></span>
* <span data-ttu-id="b2700-161">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="b2700-161">Fetch data</span></span>

<span data-ttu-id="b2700-162">Na página contador, selecione o botão para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="b2700-162">On the Counter page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b2700-163">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor você pode usar C#.</span><span class="sxs-lookup"><span data-stu-id="b2700-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="b2700-164">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b2700-164">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="b2700-165">Uma solicitação para `/counter` no navegador, conforme especificado pela `@page` diretiva na parte superior, faz com que o `Counter` componente processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="b2700-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="b2700-166">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="b2700-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="b2700-167">Cada vez que o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="b2700-167">Each time the button is selected:</span></span>

* <span data-ttu-id="b2700-168">O `onclick` evento é acionado.</span><span class="sxs-lookup"><span data-stu-id="b2700-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="b2700-169">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="b2700-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="b2700-170">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="b2700-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="b2700-171">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="b2700-171">The component is rendered again.</span></span>

<span data-ttu-id="b2700-172">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="b2700-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="b2700-173">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="b2700-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="b2700-174">Por exemplo, adicione o `Counter` componente à página inicial do aplicativo adicionando um `<Counter />` elemento ao `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="b2700-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="b2700-175">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="b2700-175">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="b2700-176">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2700-176">Run the app.</span></span> <span data-ttu-id="b2700-177">A Home Page tem seu próprio contador fornecido pelo `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="b2700-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="b2700-178">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components/index#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="b2700-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="b2700-179">Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do componente `@code` :</span><span class="sxs-lookup"><span data-stu-id="b2700-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="b2700-180">Adicione uma propriedade pública para `IncrementAmount` com um [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="b2700-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="b2700-181">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b2700-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="b2700-182">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b2700-182">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="b2700-183">Especifique o `IncrementAmount` no `Index` elemento do componente `<Counter>` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="b2700-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="b2700-184">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="b2700-184">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="b2700-185">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2700-185">Run the app.</span></span> <span data-ttu-id="b2700-186">O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="b2700-186">The `Index` component has its own counter that increments by ten each time the button is selected.</span></span> <span data-ttu-id="b2700-187">O `Counter` componente ( `Pages/Counter.razor` ) em `/counter` continua a incrementar em um.</span><span class="sxs-lookup"><span data-stu-id="b2700-187">The `Counter` component (`Pages/Counter.razor`) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b2700-188">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b2700-188">Next steps</span></span>

<span data-ttu-id="b2700-189">Compilar um Blazor aplicativo passo a passo:</span><span class="sxs-lookup"><span data-stu-id="b2700-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="b2700-190">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b2700-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
