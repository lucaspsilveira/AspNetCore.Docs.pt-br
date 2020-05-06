---
title: 'Tutorial: introdução às Razor páginas no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor páginas no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor páginas, usar Entity Framework Core e SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774867"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="ffac1-104">Tutorial: introdução ao Razor Pages no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffac1-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="ffac1-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ffac1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="ffac1-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ffac1-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="ffac1-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="ffac1-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="ffac1-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="ffac1-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ffac1-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="ffac1-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="ffac1-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffac1-110">Run the app.</span></span>
> * <span data-ttu-id="ffac1-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="ffac1-111">Examine the project files.</span></span>

<span data-ttu-id="ffac1-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="ffac1-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="ffac1-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ffac1-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffac1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffac1-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffac1-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffac1-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffac1-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffac1-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="ffac1-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="ffac1-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffac1-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffac1-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffac1-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ffac1-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="ffac1-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="ffac1-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="ffac1-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="ffac1-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="ffac1-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="ffac1-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="ffac1-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="ffac1-126">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web**e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="ffac1-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="ffac1-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffac1-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffac1-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffac1-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ffac1-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="ffac1-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="ffac1-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="ffac1-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ffac1-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="ffac1-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="ffac1-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="ffac1-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ffac1-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="ffac1-136">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="ffac1-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="ffac1-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="ffac1-137">Select **Yes**.</span></span>

  <span data-ttu-id="ffac1-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffac1-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffac1-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffac1-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffac1-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-140">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ffac1-142">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="ffac1-144">Na caixa de diálogo **configurar seu novo aplicativo Web** , defina a **estrutura de destino** como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![seleção do macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="ffac1-146">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="ffac1-148">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ffac1-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="ffac1-149">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="ffac1-149">Examine the project files</span></span>

<span data-ttu-id="ffac1-150">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="ffac1-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="ffac1-151">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="ffac1-151">Pages folder</span></span>

<span data-ttu-id="ffac1-152">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="ffac1-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="ffac1-153">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="ffac1-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="ffac1-154">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="ffac1-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="ffac1-155">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="ffac1-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="ffac1-156">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="ffac1-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="ffac1-157">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="ffac1-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="ffac1-158">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="ffac1-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="ffac1-159">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="ffac1-160">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="ffac1-160">wwwroot folder</span></span>

<span data-ttu-id="ffac1-161">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="ffac1-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="ffac1-162">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="ffac1-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="ffac1-163">appSettings.json</span></span>

<span data-ttu-id="ffac1-164">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="ffac1-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="ffac1-165">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="ffac1-166">Module.vb</span><span class="sxs-lookup"><span data-stu-id="ffac1-166">Program.cs</span></span>

<span data-ttu-id="ffac1-167">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="ffac1-167">Contains the entry point for the program.</span></span> <span data-ttu-id="ffac1-168">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="ffac1-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ffac1-169">Startup.cs</span></span>

<span data-ttu-id="ffac1-170">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffac1-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="ffac1-171">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ffac1-172">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ffac1-172">Next steps</span></span>

<span data-ttu-id="ffac1-173">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="ffac1-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ffac1-174">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="ffac1-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ffac1-175">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="ffac1-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="ffac1-176">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ffac1-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="ffac1-177">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="ffac1-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="ffac1-178">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="ffac1-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ffac1-179">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="ffac1-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="ffac1-180">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffac1-180">Run the app.</span></span>
> * <span data-ttu-id="ffac1-181">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="ffac1-181">Examine the project files.</span></span>

<span data-ttu-id="ffac1-182">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="ffac1-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="ffac1-184">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ffac1-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffac1-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffac1-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffac1-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffac1-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffac1-187">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffac1-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="ffac1-188">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="ffac1-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffac1-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffac1-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffac1-190">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="ffac1-191">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="ffac1-193">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="ffac1-194">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="ffac1-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="ffac1-196">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="ffac1-198">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="ffac1-198">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffac1-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffac1-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffac1-201">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ffac1-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="ffac1-202">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="ffac1-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="ffac1-203">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ffac1-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="ffac1-204">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="ffac1-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="ffac1-205">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ffac1-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="ffac1-206">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="ffac1-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="ffac1-207">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="ffac1-207">Select **Yes**.</span></span>

  <span data-ttu-id="ffac1-208">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffac1-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffac1-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffac1-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffac1-210">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-210">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ffac1-212">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="ffac1-214">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="ffac1-216">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffac1-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="ffac1-218">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ffac1-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffac1-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffac1-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffac1-220">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="ffac1-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="ffac1-221">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffac1-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ffac1-222">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ffac1-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ffac1-223">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ffac1-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="ffac1-224">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="ffac1-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="ffac1-225">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="ffac1-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="ffac1-226">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="ffac1-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ffac1-227">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="ffac1-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="ffac1-229">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="ffac1-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="ffac1-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffac1-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="ffac1-232">Pressione **Ctrl-F5** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="ffac1-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="ffac1-233">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ffac1-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="ffac1-234">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ffac1-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ffac1-235">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="ffac1-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ffac1-236">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="ffac1-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="ffac1-237">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="ffac1-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ffac1-238">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="ffac1-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="ffac1-240">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="ffac1-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffac1-242">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffac1-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ffac1-243">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="ffac1-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="ffac1-244">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ffac1-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="ffac1-245">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="ffac1-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ffac1-246">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="ffac1-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="ffac1-248">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="ffac1-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="ffac1-250">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="ffac1-250">Examine the project files</span></span>

<span data-ttu-id="ffac1-251">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="ffac1-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="ffac1-252">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="ffac1-252">Pages folder</span></span>

<span data-ttu-id="ffac1-253">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="ffac1-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="ffac1-254">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="ffac1-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="ffac1-255">Um arquivo *. cshtml* que contém marcação HTML com código C# usando Razor a sintaxe.</span><span class="sxs-lookup"><span data-stu-id="ffac1-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="ffac1-256">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="ffac1-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="ffac1-257">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="ffac1-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="ffac1-258">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="ffac1-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="ffac1-259">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="ffac1-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="ffac1-260">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="ffac1-261">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="ffac1-261">wwwroot folder</span></span>

<span data-ttu-id="ffac1-262">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="ffac1-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="ffac1-263">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="ffac1-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="ffac1-264">appSettings.json</span></span>

<span data-ttu-id="ffac1-265">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="ffac1-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="ffac1-266">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="ffac1-267">Module.vb</span><span class="sxs-lookup"><span data-stu-id="ffac1-267">Program.cs</span></span>

<span data-ttu-id="ffac1-268">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="ffac1-268">Contains the entry point for the program.</span></span> <span data-ttu-id="ffac1-269">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="ffac1-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ffac1-270">Startup.cs</span></span>

<span data-ttu-id="ffac1-271">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="ffac1-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="ffac1-272">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ffac1-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ffac1-273">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ffac1-273">Additional resources</span></span>

* [<span data-ttu-id="ffac1-274">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="ffac1-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="ffac1-275">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ffac1-275">Next steps</span></span>

<span data-ttu-id="ffac1-276">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="ffac1-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ffac1-277">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="ffac1-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
