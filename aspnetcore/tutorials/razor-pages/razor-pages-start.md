---
title: 'Tutorial: introdução às Razor páginas no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor páginas no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor páginas, usar Entity Framework Core e SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405296"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="a6bfc-104">Tutorial: introdução às Razor páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6bfc-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a6bfc-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6bfc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="a6bfc-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a6bfc-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a6bfc-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a6bfc-109">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a6bfc-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-110">Run the app.</span></span>
> * <span data-ttu-id="a6bfc-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-111">Examine the project files.</span></span>

<span data-ttu-id="a6bfc-112">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a6bfc-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a6bfc-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6bfc-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bfc-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6bfc-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6bfc-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6bfc-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a6bfc-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="a6bfc-118">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="a6bfc-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6bfc-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bfc-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6bfc-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a6bfc-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="a6bfc-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="a6bfc-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="a6bfc-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a6bfc-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="a6bfc-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="a6bfc-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="a6bfc-126">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web**e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="a6bfc-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6bfc-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6bfc-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a6bfc-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a6bfc-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a6bfc-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a6bfc-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a6bfc-134">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="a6bfc-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a6bfc-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a6bfc-136">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="a6bfc-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="a6bfc-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-137">Select **Yes**.</span></span>

  <span data-ttu-id="a6bfc-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6bfc-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a6bfc-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6bfc-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-140">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a6bfc-142">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a6bfc-143">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="a6bfc-145">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="a6bfc-146">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="a6bfc-147">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="a6bfc-148">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-148">Select **Next**.</span></span>

  ![seleção do macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="a6bfc-150">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a6bfc-152">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a6bfc-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="a6bfc-153">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="a6bfc-153">Examine the project files</span></span>

<span data-ttu-id="a6bfc-154">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a6bfc-155">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="a6bfc-155">Pages folder</span></span>

<span data-ttu-id="a6bfc-156">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a6bfc-157">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a6bfc-158">Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a6bfc-159">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a6bfc-160">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a6bfc-161">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a6bfc-162">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a6bfc-163">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a6bfc-164">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="a6bfc-164">wwwroot folder</span></span>

<span data-ttu-id="a6bfc-165">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a6bfc-166">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a6bfc-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="a6bfc-167">appSettings.json</span></span>

<span data-ttu-id="a6bfc-168">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a6bfc-169">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a6bfc-170">Module.vb</span><span class="sxs-lookup"><span data-stu-id="a6bfc-170">Program.cs</span></span>

<span data-ttu-id="a6bfc-171">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-171">Contains the entry point for the program.</span></span> <span data-ttu-id="a6bfc-172">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a6bfc-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a6bfc-173">Startup.cs</span></span>

<span data-ttu-id="a6bfc-174">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="a6bfc-175">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a6bfc-176">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a6bfc-176">Next steps</span></span>

<span data-ttu-id="a6bfc-177">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a6bfc-178">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="a6bfc-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6bfc-179">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="a6bfc-180">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas da criação de um Razor aplicativo Web de ASP.NET Core páginas.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a6bfc-181">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a6bfc-182">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a6bfc-183">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a6bfc-184">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-184">Run the app.</span></span>
> * <span data-ttu-id="a6bfc-185">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-185">Examine the project files.</span></span>

<span data-ttu-id="a6bfc-186">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a6bfc-188">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a6bfc-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6bfc-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bfc-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6bfc-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6bfc-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6bfc-191">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a6bfc-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="a6bfc-192">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="a6bfc-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6bfc-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bfc-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6bfc-194">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="a6bfc-195">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="a6bfc-197">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a6bfc-198">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="a6bfc-200">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="a6bfc-202">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-202">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6bfc-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6bfc-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a6bfc-205">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a6bfc-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a6bfc-206">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a6bfc-207">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a6bfc-208">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="a6bfc-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a6bfc-209">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a6bfc-210">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="a6bfc-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="a6bfc-211">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-211">Select **Yes**.</span></span>

  <span data-ttu-id="a6bfc-212">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6bfc-213">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a6bfc-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6bfc-214">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-214">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a6bfc-216">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a6bfc-217">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="a6bfc-218">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="a6bfc-220">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a6bfc-222">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a6bfc-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6bfc-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bfc-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6bfc-224">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a6bfc-225">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a6bfc-226">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a6bfc-227">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="a6bfc-228">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a6bfc-229">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="a6bfc-230">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a6bfc-231">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a6bfc-233">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="a6bfc-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6bfc-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="a6bfc-236">Pressione **Ctrl-F5** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a6bfc-237">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="a6bfc-238">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a6bfc-239">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a6bfc-240">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="a6bfc-241">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a6bfc-242">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a6bfc-244">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6bfc-246">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a6bfc-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a6bfc-247">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a6bfc-248">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="a6bfc-249">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a6bfc-250">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="a6bfc-252">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="a6bfc-254">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="a6bfc-254">Examine the project files</span></span>

<span data-ttu-id="a6bfc-255">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a6bfc-256">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="a6bfc-256">Pages folder</span></span>

<span data-ttu-id="a6bfc-257">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a6bfc-258">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a6bfc-259">Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a6bfc-260">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a6bfc-261">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a6bfc-262">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a6bfc-263">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a6bfc-264">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a6bfc-265">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="a6bfc-265">wwwroot folder</span></span>

<span data-ttu-id="a6bfc-266">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a6bfc-267">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a6bfc-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="a6bfc-268">appSettings.json</span></span>

<span data-ttu-id="a6bfc-269">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a6bfc-270">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a6bfc-271">Module.vb</span><span class="sxs-lookup"><span data-stu-id="a6bfc-271">Program.cs</span></span>

<span data-ttu-id="a6bfc-272">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-272">Contains the entry point for the program.</span></span> <span data-ttu-id="a6bfc-273">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a6bfc-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a6bfc-274">Startup.cs</span></span>

<span data-ttu-id="a6bfc-275">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="a6bfc-276">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a6bfc-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6bfc-277">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a6bfc-277">Additional resources</span></span>

* [<span data-ttu-id="a6bfc-278">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="a6bfc-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="a6bfc-279">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a6bfc-279">Next steps</span></span>

<span data-ttu-id="a6bfc-280">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="a6bfc-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a6bfc-281">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="a6bfc-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
