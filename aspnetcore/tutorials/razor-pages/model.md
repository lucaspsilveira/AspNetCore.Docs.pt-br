---
title: Adicionar um modelo a um Razor aplicativo de páginas no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: d093d7c4dc1b355c0042f300f70a830653b168c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769826"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="53e27-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53e27-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="53e27-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53e27-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="53e27-105">Nesta seção, as classes são adicionadas para o gerenciamento de filmes.</span><span class="sxs-lookup"><span data-stu-id="53e27-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="53e27-106">As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="53e27-107">EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="53e27-108">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="53e27-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="53e27-109">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="53e27-110">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="53e27-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53e27-112">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="53e27-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="53e27-113">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-113">Name the folder *Models*.</span></span>

<span data-ttu-id="53e27-114">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-114">Right click the *Models* folder.</span></span> <span data-ttu-id="53e27-115">Selecione **Adicionar** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="53e27-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="53e27-116">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="53e27-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="53e27-118">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="53e27-119">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="53e27-121">Em painel de soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos*de pasta.</span><span class="sxs-lookup"><span data-stu-id="53e27-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="53e27-122">Clique com o botão direito do mouse na pasta *modelos* e selecione **Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="53e27-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="53e27-123">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="53e27-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="53e27-124">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="53e27-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="53e27-125">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="53e27-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="53e27-126">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="53e27-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="53e27-127">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="53e27-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="53e27-128">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="53e27-128">Scaffold the movie model</span></span>

<span data-ttu-id="53e27-129">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="53e27-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="53e27-130">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="53e27-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53e27-132">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="53e27-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="53e27-133">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="53e27-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="53e27-134">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="53e27-134">Name the folder *Movies*</span></span>

<span data-ttu-id="53e27-135">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="53e27-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="53e27-137">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor Pages usando a adição de Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="53e27-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="53e27-139">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="53e27-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="53e27-140">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="53e27-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="53e27-141">Na linha **Classe de contexto de dados**, selecione o sinal de adição (**+**) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="53e27-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="53e27-142">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="53e27-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="53e27-143">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="53e27-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="53e27-144">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="53e27-144">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="53e27-146">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="53e27-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="53e27-148">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="53e27-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="53e27-149">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="53e27-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="53e27-150">**Para Windows**: execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="53e27-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="53e27-151">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="53e27-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="53e27-153">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="53e27-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="53e27-154">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="53e27-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="53e27-155">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="53e27-155">Name the folder *Movies*</span></span>

<span data-ttu-id="53e27-156">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="53e27-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="53e27-158">Na caixa de diálogo **novo scaffolding** , selecione **Razor Pages usando Entity Framework (CRUD)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="53e27-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="53e27-160">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="53e27-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="53e27-161">Na lista suspensa **classe de modelo** , selecione ou digite **filme (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="53e27-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="53e27-162">Na linha **classe de contexto de dados** , digite o nome da nova classe, RazorPagesMovie. **Dados**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="53e27-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="53e27-163">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="53e27-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="53e27-164">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="53e27-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="53e27-165">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="53e27-165">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="53e27-167">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="53e27-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="53e27-168">Adicionar ferramentas do EF</span><span class="sxs-lookup"><span data-stu-id="53e27-168">Add EF tools</span></span>

<span data-ttu-id="53e27-169">Execute o seguinte comando de CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="53e27-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="53e27-170">O comando anterior adiciona as ferramentas de Entity Framework Core para o CLI do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="53e27-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="53e27-171">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="53e27-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53e27-173">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="53e27-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="53e27-174">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="53e27-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="53e27-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="53e27-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="53e27-176">Atualizado</span><span class="sxs-lookup"><span data-stu-id="53e27-176">Updated</span></span>

* <span data-ttu-id="53e27-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="53e27-177">*Startup.cs*</span></span>

<span data-ttu-id="53e27-178">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="53e27-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-179">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="53e27-180">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="53e27-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="53e27-181">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="53e27-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="53e27-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="53e27-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="53e27-183">Atualizado</span><span class="sxs-lookup"><span data-stu-id="53e27-183">Updated</span></span>

* <span data-ttu-id="53e27-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="53e27-184">*Startup.cs*</span></span>

<span data-ttu-id="53e27-185">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="53e27-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="53e27-187">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="53e27-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="53e27-188">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="53e27-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="53e27-189">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="53e27-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="53e27-190">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="53e27-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53e27-192">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="53e27-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="53e27-193">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="53e27-193">Add an initial migration.</span></span>
* <span data-ttu-id="53e27-194">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="53e27-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="53e27-195">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="53e27-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="53e27-197">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="53e27-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="53e27-200">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="53e27-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="53e27-201">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="53e27-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="53e27-202">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="53e27-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="53e27-203">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="53e27-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="53e27-204">O comando migrações gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="53e27-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="53e27-205">O esquema é baseado no modelo especificado em `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="53e27-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="53e27-206">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="53e27-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="53e27-207">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="53e27-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="53e27-208">O `update` comando executa o `Up` método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="53e27-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="53e27-209">Nesse caso, `update` o executa o `Up` método em *migrações/\<carimbo de data/hora>_InitialCreate arquivo. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="53e27-211">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="53e27-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="53e27-212">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="53e27-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="53e27-213">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="53e27-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="53e27-214">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="53e27-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="53e27-215">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="53e27-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="53e27-216">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="53e27-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="53e27-217">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="53e27-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="53e27-218">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="53e27-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="53e27-219">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="53e27-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="53e27-220">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="53e27-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="53e27-221">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="53e27-222">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="53e27-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="53e27-223">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="53e27-224">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="53e27-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="53e27-225">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="53e27-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="53e27-226">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="53e27-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="53e27-228">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="53e27-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-229">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="53e27-230">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="53e27-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="53e27-231">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="53e27-231">Test the app</span></span>

* <span data-ttu-id="53e27-232">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="53e27-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="53e27-233">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="53e27-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="53e27-234">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="53e27-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="53e27-235">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="53e27-235">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="53e27-237">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="53e27-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="53e27-238">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="53e27-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="53e27-239">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="53e27-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="53e27-240">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="53e27-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="53e27-241">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="53e27-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53e27-242">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="53e27-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="53e27-243">[Anterior:](xref:tutorials/razor-pages/razor-pages-start)
> introdução a[seguir: com Scaffold Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="53e27-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53e27-244">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="53e27-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="53e27-245">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="53e27-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="53e27-246">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="53e27-247">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="53e27-248">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="53e27-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="53e27-249">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="53e27-250">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="53e27-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53e27-252">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="53e27-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="53e27-253">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-253">Name the folder *Models*.</span></span>

<span data-ttu-id="53e27-254">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-254">Right click the *Models* folder.</span></span> <span data-ttu-id="53e27-255">Selecione **Adicionar** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="53e27-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="53e27-256">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="53e27-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="53e27-258">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="53e27-259">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-260">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="53e27-261">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="53e27-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="53e27-262">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="53e27-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="53e27-263">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="53e27-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="53e27-264">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="53e27-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="53e27-265">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="53e27-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="53e27-266">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="53e27-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="53e27-267">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="53e27-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="53e27-268">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="53e27-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="53e27-269">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="53e27-269">Scaffold the movie model</span></span>

<span data-ttu-id="53e27-270">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="53e27-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="53e27-271">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="53e27-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53e27-273">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="53e27-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="53e27-274">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="53e27-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="53e27-275">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="53e27-275">Name the folder *Movies*</span></span>

<span data-ttu-id="53e27-276">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="53e27-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="53e27-278">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor Pages usando a adição de Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="53e27-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="53e27-280">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="53e27-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="53e27-281">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="53e27-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="53e27-282">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="53e27-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="53e27-283">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="53e27-283">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="53e27-285">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="53e27-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="53e27-287">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="53e27-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="53e27-288">**Para Windows**: execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="53e27-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="53e27-289">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="53e27-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-290">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="53e27-291">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="53e27-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="53e27-292">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="53e27-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="53e27-293">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="53e27-293">Name the folder *Movies*</span></span>

<span data-ttu-id="53e27-294">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="53e27-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="53e27-296">Na caixa de diálogo **Adicionar novo scaffolding** , selecione **Razor Pages usando a adição de Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="53e27-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="53e27-298">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="53e27-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="53e27-299">Na lista suspensa **classe de modelo** , selecione ou digite **filme**.</span><span class="sxs-lookup"><span data-stu-id="53e27-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="53e27-300">Na linha **classe de contexto de dados** , digite Select **RazorPagesMovieContext** isso criará uma nova classe de contexto DB com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="53e27-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="53e27-301">Nesse caso, será **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="53e27-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="53e27-302">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="53e27-302">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="53e27-304">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="53e27-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="53e27-305">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="53e27-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="53e27-306">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="53e27-306">Files created</span></span>

* <span data-ttu-id="53e27-307">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="53e27-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="53e27-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="53e27-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="53e27-309">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="53e27-309">File updated</span></span>

* <span data-ttu-id="53e27-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="53e27-310">*Startup.cs*</span></span>

<span data-ttu-id="53e27-311">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="53e27-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="53e27-312">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="53e27-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53e27-314">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="53e27-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="53e27-315">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="53e27-315">Add an initial migration.</span></span>
* <span data-ttu-id="53e27-316">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="53e27-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="53e27-317">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="53e27-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="53e27-319">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="53e27-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="53e27-320">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="53e27-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="53e27-321">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="53e27-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="53e27-322">O `InitialCreate` argumento é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="53e27-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="53e27-323">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="53e27-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="53e27-324">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="53e27-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="53e27-325">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="53e27-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="53e27-326">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-328">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="53e27-329">Os comandos anteriores geram o seguinte aviso: "*nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '. Isso fará com que os valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando ' HasColumnType () '.*" Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="53e27-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53e27-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53e27-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="53e27-331">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="53e27-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="53e27-332">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="53e27-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="53e27-333">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="53e27-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="53e27-334">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="53e27-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="53e27-335">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="53e27-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="53e27-336">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="53e27-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="53e27-337">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="53e27-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="53e27-338">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="53e27-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="53e27-339">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="53e27-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="53e27-340">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="53e27-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="53e27-341">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="53e27-342">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="53e27-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="53e27-343">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="53e27-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="53e27-344">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="53e27-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="53e27-345">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="53e27-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="53e27-346">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="53e27-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53e27-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53e27-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="53e27-348">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="53e27-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53e27-349">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="53e27-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="53e27-350">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="53e27-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="53e27-351">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="53e27-351">Test the app</span></span>

* <span data-ttu-id="53e27-352">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="53e27-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="53e27-353">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="53e27-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="53e27-354">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="53e27-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="53e27-355">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="53e27-355">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="53e27-357">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="53e27-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="53e27-358">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="53e27-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="53e27-359">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="53e27-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="53e27-360">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="53e27-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="53e27-361">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="53e27-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53e27-362">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="53e27-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="53e27-363">[Anterior: comece](xref:tutorials/razor-pages/razor-pages-start)
> a[seguir: páginas Razor com Scaffold](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="53e27-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
