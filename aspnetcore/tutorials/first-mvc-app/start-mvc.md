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
ms.openlocfilehash: c2b76b59ae775b9268fa77019bf8420e5e4108b6
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452262"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="21ec1-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="21ec1-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="21ec1-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="21ec1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="21ec1-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="21ec1-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="21ec1-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="21ec1-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="21ec1-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="21ec1-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="21ec1-108">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="21ec1-108">Create a web app.</span></span>
> * <span data-ttu-id="21ec1-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="21ec1-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="21ec1-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="21ec1-110">Work with a database.</span></span>
> * <span data-ttu-id="21ec1-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="21ec1-111">Add search and validation.</span></span>

<span data-ttu-id="21ec1-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="21ec1-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="21ec1-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="21ec1-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21ec1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21ec1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="21ec1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21ec1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21ec1-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="21ec1-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="21ec1-117">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="21ec1-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21ec1-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21ec1-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="21ec1-119">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="21ec1-120">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="21ec1-122">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="21ec1-123">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="21ec1-125">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="21ec1-126">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21ec1-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="21ec1-127">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="21ec1-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="21ec1-128">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="21ec1-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="21ec1-129">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="21ec1-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21ec1-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21ec1-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="21ec1-131">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="21ec1-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="21ec1-132">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="21ec1-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="21ec1-133">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="21ec1-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="21ec1-134">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="21ec1-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="21ec1-135">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="21ec1-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="21ec1-136">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="21ec1-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="21ec1-137">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-137">Select **Yes**</span></span>

  * <span data-ttu-id="21ec1-138">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="21ec1-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="21ec1-139">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="21ec1-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21ec1-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="21ec1-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="21ec1-141">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-141">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="21ec1-143">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="21ec1-144">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="21ec1-146">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="21ec1-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="21ec1-147">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="21ec1-148">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="21ec1-149">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-149">Select **Next**.</span></span>

  ![seleção do macOS .NET Core 3,1](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="21ec1-151">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="21ec1-153">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="21ec1-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21ec1-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21ec1-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="21ec1-155">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="21ec1-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="21ec1-156">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="21ec1-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="21ec1-157">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="21ec1-158">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="21ec1-159">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="21ec1-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="21ec1-160">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="21ec1-161">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="21ec1-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="21ec1-162">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="21ec1-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="21ec1-164">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="21ec1-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="21ec1-166">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="21ec1-166">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="21ec1-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21ec1-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="21ec1-169">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="21ec1-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="21ec1-170">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="21ec1-171">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="21ec1-172">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="21ec1-173">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="21ec1-174">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="21ec1-175">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="21ec1-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21ec1-177">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="21ec1-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="21ec1-178">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="21ec1-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="21ec1-179">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="21ec1-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="21ec1-180">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="21ec1-181">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="21ec1-182">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="21ec1-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="21ec1-183">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="21ec1-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="21ec1-184">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="21ec1-185">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="21ec1-185">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="21ec1-187">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="21ec1-188">Avançar</span><span class="sxs-lookup"><span data-stu-id="21ec1-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="21ec1-189">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="21ec1-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="21ec1-190">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="21ec1-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="21ec1-191">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="21ec1-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="21ec1-192">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="21ec1-192">Create a web app.</span></span>
> * <span data-ttu-id="21ec1-193">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="21ec1-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="21ec1-194">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="21ec1-194">Work with a database.</span></span>
> * <span data-ttu-id="21ec1-195">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="21ec1-195">Add search and validation.</span></span>

<span data-ttu-id="21ec1-196">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="21ec1-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="21ec1-197">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="21ec1-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21ec1-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21ec1-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="21ec1-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21ec1-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21ec1-200">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="21ec1-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="21ec1-201">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="21ec1-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21ec1-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21ec1-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="21ec1-203">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="21ec1-204">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="21ec1-206">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="21ec1-207">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="21ec1-209">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="21ec1-210">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21ec1-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="21ec1-211">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="21ec1-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="21ec1-212">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="21ec1-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="21ec1-213">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="21ec1-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="21ec1-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21ec1-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="21ec1-215">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="21ec1-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="21ec1-216">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="21ec1-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="21ec1-217">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="21ec1-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="21ec1-218">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="21ec1-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="21ec1-219">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="21ec1-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="21ec1-220">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="21ec1-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="21ec1-221">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-221">Select **Yes**</span></span>

  * <span data-ttu-id="21ec1-222">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="21ec1-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="21ec1-223">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="21ec1-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21ec1-224">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="21ec1-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="21ec1-225">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-225">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="21ec1-227">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="21ec1-228">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="21ec1-229">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , aceite a **estrutura de destino** padrão do **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Seleção do .NET Core 2.2 do macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="21ec1-231">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="21ec1-232">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="21ec1-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="21ec1-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21ec1-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="21ec1-234">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="21ec1-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="21ec1-235">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="21ec1-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="21ec1-236">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="21ec1-237">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="21ec1-238">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="21ec1-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="21ec1-239">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="21ec1-240">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="21ec1-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="21ec1-241">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="21ec1-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="21ec1-243">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="21ec1-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="21ec1-245">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="21ec1-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="21ec1-246">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="21ec1-246">This app doesn't track personal information.</span></span> <span data-ttu-id="21ec1-247">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="21ec1-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="21ec1-249">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="21ec1-249">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="21ec1-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21ec1-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="21ec1-252">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="21ec1-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="21ec1-253">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="21ec1-254">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="21ec1-255">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="21ec1-256">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="21ec1-257">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="21ec1-258">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="21ec1-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="21ec1-259">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="21ec1-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="21ec1-260">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="21ec1-260">This app doesn't track personal information.</span></span> <span data-ttu-id="21ec1-261">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="21ec1-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="21ec1-263">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="21ec1-263">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="21ec1-265">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="21ec1-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="21ec1-266">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="21ec1-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="21ec1-267">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="21ec1-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="21ec1-268">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="21ec1-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="21ec1-269">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="21ec1-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="21ec1-270">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="21ec1-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="21ec1-271">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="21ec1-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="21ec1-272">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="21ec1-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="21ec1-273">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="21ec1-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="21ec1-274">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="21ec1-274">This app doesn't track personal information.</span></span> <span data-ttu-id="21ec1-275">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="21ec1-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="21ec1-277">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="21ec1-277">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="21ec1-279">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="21ec1-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="21ec1-280">Avançar</span><span class="sxs-lookup"><span data-stu-id="21ec1-280">Next</span></span>](adding-controller.md)

::: moniker-end
