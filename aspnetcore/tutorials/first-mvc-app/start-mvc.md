---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 0b28e99d9c56d92e157e9010880e58ec5165196a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774979"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="ecf76-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="ecf76-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="ecf76-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ecf76-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="ecf76-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ecf76-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="ecf76-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="ecf76-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="ecf76-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="ecf76-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ecf76-108">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ecf76-108">Create a web app.</span></span>
> * <span data-ttu-id="ecf76-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="ecf76-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="ecf76-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ecf76-110">Work with a database.</span></span>
> * <span data-ttu-id="ecf76-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="ecf76-111">Add search and validation.</span></span>

<span data-ttu-id="ecf76-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="ecf76-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="ecf76-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ecf76-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ecf76-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecf76-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ecf76-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecf76-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ecf76-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecf76-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="ecf76-117">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ecf76-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ecf76-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecf76-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecf76-119">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="ecf76-120">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="ecf76-122">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="ecf76-123">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="ecf76-125">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="ecf76-126">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecf76-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="ecf76-127">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="ecf76-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="ecf76-128">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="ecf76-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="ecf76-129">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="ecf76-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ecf76-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecf76-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ecf76-131">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="ecf76-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="ecf76-132">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="ecf76-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="ecf76-133">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ecf76-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ecf76-134">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="ecf76-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ecf76-135">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="ecf76-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="ecf76-136">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="ecf76-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="ecf76-137">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-137">Select **Yes**</span></span>

  * <span data-ttu-id="ecf76-138">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="ecf76-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="ecf76-139">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ecf76-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ecf76-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecf76-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ecf76-141">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-141">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ecf76-143">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web (Model-View-Controller)** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="ecf76-145">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** do **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![seleção do macOS .NET Core 3,1](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="ecf76-147">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="ecf76-148">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ecf76-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ecf76-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecf76-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ecf76-150">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ecf76-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="ecf76-151">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ecf76-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ecf76-152">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ecf76-153">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ecf76-154">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="ecf76-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="ecf76-155">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ecf76-156">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="ecf76-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="ecf76-157">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="ecf76-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="ecf76-159">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ecf76-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="ecf76-161">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ecf76-161">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ecf76-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecf76-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ecf76-164">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="ecf76-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="ecf76-165">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="ecf76-166">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="ecf76-167">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ecf76-168">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="ecf76-169">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ecf76-170">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="ecf76-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ecf76-172">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecf76-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ecf76-173">Selecione **executar** > **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ecf76-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="ecf76-174">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ecf76-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ecf76-175">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ecf76-176">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ecf76-177">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="ecf76-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="ecf76-178">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="ecf76-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="ecf76-179">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="ecf76-180">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ecf76-180">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="ecf76-182">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ecf76-183">Avançar</span><span class="sxs-lookup"><span data-stu-id="ecf76-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="ecf76-184">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ecf76-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="ecf76-185">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="ecf76-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="ecf76-186">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="ecf76-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ecf76-187">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ecf76-187">Create a web app.</span></span>
> * <span data-ttu-id="ecf76-188">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="ecf76-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="ecf76-189">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ecf76-189">Work with a database.</span></span>
> * <span data-ttu-id="ecf76-190">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="ecf76-190">Add search and validation.</span></span>

<span data-ttu-id="ecf76-191">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="ecf76-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="ecf76-192">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ecf76-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ecf76-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecf76-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ecf76-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecf76-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ecf76-195">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecf76-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="ecf76-196">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ecf76-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ecf76-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecf76-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecf76-198">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="ecf76-199">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="ecf76-201">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="ecf76-202">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="ecf76-204">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="ecf76-205">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecf76-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="ecf76-206">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="ecf76-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="ecf76-207">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="ecf76-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="ecf76-208">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="ecf76-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ecf76-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecf76-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ecf76-210">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="ecf76-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="ecf76-211">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="ecf76-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="ecf76-212">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ecf76-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ecf76-213">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="ecf76-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ecf76-214">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="ecf76-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="ecf76-215">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="ecf76-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="ecf76-216">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-216">Select **Yes**</span></span>

  * <span data-ttu-id="ecf76-217">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="ecf76-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="ecf76-218">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ecf76-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ecf76-219">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecf76-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ecf76-220">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-220">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ecf76-222">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web (Model-View-Controller)** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="ecf76-224">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , aceite a **estrutura de destino** padrão do **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Seleção do .NET Core 2.2 do macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="ecf76-226">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="ecf76-227">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ecf76-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ecf76-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecf76-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ecf76-229">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ecf76-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="ecf76-230">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ecf76-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ecf76-231">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ecf76-232">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ecf76-233">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="ecf76-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="ecf76-234">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ecf76-235">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="ecf76-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="ecf76-236">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="ecf76-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="ecf76-238">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ecf76-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="ecf76-240">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="ecf76-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="ecf76-241">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="ecf76-241">This app doesn't track personal information.</span></span> <span data-ttu-id="ecf76-242">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="ecf76-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="ecf76-244">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="ecf76-244">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ecf76-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecf76-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ecf76-247">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="ecf76-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="ecf76-248">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="ecf76-249">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="ecf76-250">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ecf76-251">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="ecf76-252">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ecf76-253">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="ecf76-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="ecf76-254">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="ecf76-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="ecf76-255">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="ecf76-255">This app doesn't track personal information.</span></span> <span data-ttu-id="ecf76-256">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="ecf76-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="ecf76-258">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="ecf76-258">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ecf76-260">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ecf76-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ecf76-261">Selecione **executar** > **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ecf76-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="ecf76-262">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ecf76-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ecf76-263">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ecf76-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ecf76-264">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ecf76-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ecf76-265">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="ecf76-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="ecf76-266">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="ecf76-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="ecf76-267">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="ecf76-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="ecf76-268">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="ecf76-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="ecf76-269">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="ecf76-269">This app doesn't track personal information.</span></span> <span data-ttu-id="ecf76-270">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="ecf76-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="ecf76-272">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="ecf76-272">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="ecf76-274">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="ecf76-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ecf76-275">Avançar</span><span class="sxs-lookup"><span data-stu-id="ecf76-275">Next</span></span>](adding-controller.md)

::: moniker-end
