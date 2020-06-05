---
title: "\"Tutorial: introdução às Razor páginas no ASP.NET Core\""
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
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452326"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="10ffb-104">Tutorial: introdução às Razor páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10ffb-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="10ffb-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="10ffb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="10ffb-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="10ffb-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="10ffb-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="10ffb-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="10ffb-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="10ffb-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="10ffb-109">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="10ffb-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="10ffb-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10ffb-110">Run the app.</span></span>
> * <span data-ttu-id="10ffb-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="10ffb-111">Examine the project files.</span></span>

<span data-ttu-id="10ffb-112">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="10ffb-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="10ffb-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="10ffb-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10ffb-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10ffb-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="10ffb-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10ffb-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="10ffb-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="10ffb-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="10ffb-118">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="10ffb-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10ffb-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10ffb-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="10ffb-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="10ffb-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="10ffb-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="10ffb-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="10ffb-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="10ffb-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="10ffb-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="10ffb-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="10ffb-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="10ffb-126">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web**e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="10ffb-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="10ffb-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="10ffb-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10ffb-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="10ffb-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="10ffb-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="10ffb-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="10ffb-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="10ffb-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="10ffb-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="10ffb-134">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="10ffb-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="10ffb-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="10ffb-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="10ffb-136">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="10ffb-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="10ffb-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="10ffb-137">Select **Yes**.</span></span>

  <span data-ttu-id="10ffb-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="10ffb-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="10ffb-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="10ffb-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="10ffb-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-140">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="10ffb-142">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="10ffb-143">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="10ffb-145">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="10ffb-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="10ffb-146">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="10ffb-147">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="10ffb-148">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-148">Select **Next**.</span></span>

  ![seleção do macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="10ffb-150">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="10ffb-152">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="10ffb-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="10ffb-153">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="10ffb-153">Examine the project files</span></span>

<span data-ttu-id="10ffb-154">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="10ffb-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="10ffb-155">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="10ffb-155">Pages folder</span></span>

<span data-ttu-id="10ffb-156">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="10ffb-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="10ffb-157">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="10ffb-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="10ffb-158">Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="10ffb-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="10ffb-159">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="10ffb-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="10ffb-160">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="10ffb-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="10ffb-161">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="10ffb-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="10ffb-162">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="10ffb-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="10ffb-163">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="10ffb-164">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="10ffb-164">wwwroot folder</span></span>

<span data-ttu-id="10ffb-165">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="10ffb-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="10ffb-166">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="10ffb-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="10ffb-167">appSettings.json</span></span>

<span data-ttu-id="10ffb-168">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="10ffb-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="10ffb-169">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="10ffb-170">Module.vb</span><span class="sxs-lookup"><span data-stu-id="10ffb-170">Program.cs</span></span>

<span data-ttu-id="10ffb-171">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="10ffb-171">Contains the entry point for the program.</span></span> <span data-ttu-id="10ffb-172">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="10ffb-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="10ffb-173">Startup.cs</span></span>

<span data-ttu-id="10ffb-174">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10ffb-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="10ffb-175">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="10ffb-176">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="10ffb-176">Next steps</span></span>

<span data-ttu-id="10ffb-177">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="10ffb-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="10ffb-178">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="10ffb-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10ffb-179">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="10ffb-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="10ffb-180">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas da criação de um Razor aplicativo Web de ASP.NET Core páginas.</span><span class="sxs-lookup"><span data-stu-id="10ffb-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="10ffb-181">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="10ffb-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="10ffb-182">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="10ffb-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="10ffb-183">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="10ffb-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="10ffb-184">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10ffb-184">Run the app.</span></span>
> * <span data-ttu-id="10ffb-185">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="10ffb-185">Examine the project files.</span></span>

<span data-ttu-id="10ffb-186">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="10ffb-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="10ffb-188">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="10ffb-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10ffb-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10ffb-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="10ffb-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10ffb-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="10ffb-191">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="10ffb-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="10ffb-192">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="10ffb-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10ffb-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10ffb-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="10ffb-194">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="10ffb-195">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="10ffb-197">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="10ffb-198">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="10ffb-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="10ffb-200">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="10ffb-202">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="10ffb-202">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="10ffb-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10ffb-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="10ffb-205">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="10ffb-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="10ffb-206">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="10ffb-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="10ffb-207">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="10ffb-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="10ffb-208">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="10ffb-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="10ffb-209">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="10ffb-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="10ffb-210">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="10ffb-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="10ffb-211">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="10ffb-211">Select **Yes**.</span></span>

  <span data-ttu-id="10ffb-212">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="10ffb-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="10ffb-213">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="10ffb-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="10ffb-214">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-214">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="10ffb-216">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="10ffb-217">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="10ffb-218">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="10ffb-220">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="10ffb-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="10ffb-222">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="10ffb-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10ffb-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10ffb-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="10ffb-224">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="10ffb-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="10ffb-225">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10ffb-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="10ffb-226">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="10ffb-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="10ffb-227">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="10ffb-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="10ffb-228">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="10ffb-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="10ffb-229">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="10ffb-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="10ffb-230">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="10ffb-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="10ffb-231">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="10ffb-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="10ffb-233">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="10ffb-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="10ffb-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10ffb-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="10ffb-236">Pressione **Ctrl-F5** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="10ffb-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="10ffb-237">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="10ffb-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="10ffb-238">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="10ffb-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="10ffb-239">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="10ffb-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="10ffb-240">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="10ffb-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="10ffb-241">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="10ffb-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="10ffb-242">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="10ffb-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="10ffb-244">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="10ffb-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="10ffb-246">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="10ffb-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="10ffb-247">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="10ffb-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="10ffb-248">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="10ffb-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="10ffb-249">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="10ffb-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="10ffb-250">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="10ffb-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="10ffb-252">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="10ffb-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="10ffb-254">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="10ffb-254">Examine the project files</span></span>

<span data-ttu-id="10ffb-255">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="10ffb-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="10ffb-256">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="10ffb-256">Pages folder</span></span>

<span data-ttu-id="10ffb-257">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="10ffb-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="10ffb-258">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="10ffb-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="10ffb-259">Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="10ffb-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="10ffb-260">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="10ffb-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="10ffb-261">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="10ffb-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="10ffb-262">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="10ffb-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="10ffb-263">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="10ffb-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="10ffb-264">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="10ffb-265">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="10ffb-265">wwwroot folder</span></span>

<span data-ttu-id="10ffb-266">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="10ffb-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="10ffb-267">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="10ffb-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="10ffb-268">appSettings.json</span></span>

<span data-ttu-id="10ffb-269">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="10ffb-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="10ffb-270">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="10ffb-271">Module.vb</span><span class="sxs-lookup"><span data-stu-id="10ffb-271">Program.cs</span></span>

<span data-ttu-id="10ffb-272">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="10ffb-272">Contains the entry point for the program.</span></span> <span data-ttu-id="10ffb-273">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="10ffb-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="10ffb-274">Startup.cs</span></span>

<span data-ttu-id="10ffb-275">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="10ffb-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="10ffb-276">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="10ffb-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10ffb-277">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="10ffb-277">Additional resources</span></span>

* [<span data-ttu-id="10ffb-278">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="10ffb-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="10ffb-279">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="10ffb-279">Next steps</span></span>

<span data-ttu-id="10ffb-280">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="10ffb-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="10ffb-281">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="10ffb-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
