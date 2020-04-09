---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f6dbac81b4efceb30c379ab06dd715005d879228
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658931"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="fabf5-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fabf5-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="fabf5-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fabf5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="fabf5-105">Nesta seção, são adicionadas aulas para o gerenciamento de filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)multiplataforma .</span><span class="sxs-lookup"><span data-stu-id="fabf5-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="fabf5-106">Aplicativos criados a partir de um modelo ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="fabf5-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="fabf5-107">As classes de modelo do aplicativo são usadas com [o Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="fabf5-108">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="fabf5-109">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="fabf5-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="fabf5-110">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="fabf5-111">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="fabf5-111">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fabf5-113">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="fabf5-114">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-114">Name the folder *Models*.</span></span>

<span data-ttu-id="fabf5-115">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-115">Right click the *Models* folder.</span></span> <span data-ttu-id="fabf5-116">Selecione **Adicionar** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="fabf5-117">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fabf5-119">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="fabf5-120">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fabf5-122">No Solution Pad, clique com o botão direito do mouse no projeto **RazorPagesMovie** e, em seguida, **selecione Adicionar** > **nova pasta...**. Nomeie a pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="fabf5-123">Clique com o botão direito do mouse na pasta *Modelos* e, em seguida, **selecione Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="fabf5-124">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="fabf5-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="fabf5-125">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="fabf5-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="fabf5-126">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="fabf5-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="fabf5-127">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="fabf5-128">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="fabf5-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="fabf5-129">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="fabf5-129">Scaffold the movie model</span></span>

<span data-ttu-id="fabf5-130">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="fabf5-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="fabf5-131">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="fabf5-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fabf5-133">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="fabf5-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fabf5-134">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fabf5-135">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="fabf5-135">Name the folder *Movies*</span></span>

<span data-ttu-id="fabf5-136">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="fabf5-138">Na caixa de diálogo **Adicionar andaime,** selecione **Páginas de navalha usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="fabf5-140">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="fabf5-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="fabf5-141">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="fabf5-142">Na linha **Classe de contexto de dados**, selecione o sinal de adição (**+**) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="fabf5-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="fabf5-143">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="fabf5-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="fabf5-144">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="fabf5-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="fabf5-145">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-145">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="fabf5-147">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="fabf5-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="fabf5-149">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="fabf5-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="fabf5-150">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="fabf5-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="fabf5-151">**Para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="fabf5-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="fabf5-152">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="fabf5-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-153">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fabf5-154">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="fabf5-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fabf5-155">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fabf5-156">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="fabf5-156">Name the folder *Movies*</span></span>

<span data-ttu-id="fabf5-157">Clique com o botão direito do mouse na pasta *Páginas/Filmes* > **adicionar** > **novos andaimes...**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="fabf5-159">Na caixa de diálogo **Novos andaimes, selecione** **Páginas de Navalha usando O Quadro de Entidades (CRUD)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="fabf5-161">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="fabf5-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="fabf5-162">Na **classe Modelo,** queda, selecione ou digite **, Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="fabf5-163">Na linha **de classe de contexto de dados,** digite o nome para a nova classe, RazorPagesMovie. **Dados**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="fabf5-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="fabf5-164">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="fabf5-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="fabf5-165">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="fabf5-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="fabf5-166">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-166">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="fabf5-168">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="fabf5-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="fabf5-169">Adicionar ferramentas EF</span><span class="sxs-lookup"><span data-stu-id="fabf5-169">Add EF tools</span></span>

<span data-ttu-id="fabf5-170">Execute o seguinte comando .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="fabf5-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="fabf5-171">O comando anterior adiciona as Ferramentas core do framework da entidade para o .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="fabf5-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="fabf5-172">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="fabf5-172">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fabf5-174">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="fabf5-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="fabf5-175">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="fabf5-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="fabf5-176">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="fabf5-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="fabf5-177">Atualizado</span><span class="sxs-lookup"><span data-stu-id="fabf5-177">Updated</span></span>

* <span data-ttu-id="fabf5-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fabf5-178">*Startup.cs*</span></span>

<span data-ttu-id="fabf5-179">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="fabf5-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-180">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fabf5-181">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="fabf5-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="fabf5-182">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="fabf5-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="fabf5-183">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="fabf5-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="fabf5-184">Atualizado</span><span class="sxs-lookup"><span data-stu-id="fabf5-184">Updated</span></span>

* <span data-ttu-id="fabf5-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fabf5-185">*Startup.cs*</span></span>

<span data-ttu-id="fabf5-186">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="fabf5-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fabf5-188">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="fabf5-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="fabf5-189">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="fabf5-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="fabf5-190">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="fabf5-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="fabf5-191">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="fabf5-191">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fabf5-193">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="fabf5-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="fabf5-194">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="fabf5-194">Add an initial migration.</span></span>
* <span data-ttu-id="fabf5-195">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="fabf5-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="fabf5-196">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="fabf5-198">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="fabf5-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-200">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="fabf5-201">Os comandos anteriores geram o seguinte aviso: "Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="fabf5-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="fabf5-202">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="fabf5-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="fabf5-203">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="fabf5-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="fabf5-204">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="fabf5-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="fabf5-205">O comando migrations gera código para criar o esquema inicial do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="fabf5-206">O esquema é baseado no modelo `DbContext`especificado em .</span><span class="sxs-lookup"><span data-stu-id="fabf5-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="fabf5-207">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="fabf5-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="fabf5-208">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="fabf5-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="fabf5-209">O `update` comando `Up` executa o método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="fabf5-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="fabf5-210">Neste caso, `update` executa o `Up` método em *Migrações/\<carimbo de tempo>_InitialCreate.cs,* que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="fabf5-212">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="fabf5-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="fabf5-213">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fabf5-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fabf5-214">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fabf5-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="fabf5-215">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="fabf5-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="fabf5-216">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="fabf5-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="fabf5-217">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="fabf5-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="fabf5-218">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fabf5-219">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="fabf5-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="fabf5-220">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="fabf5-221">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="fabf5-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="fabf5-222">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="fabf5-223">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="fabf5-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="fabf5-224">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="fabf5-225">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="fabf5-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="fabf5-226">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="fabf5-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="fabf5-227">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fabf5-229">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-230">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fabf5-231">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="fabf5-232">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="fabf5-232">Test the app</span></span>

* <span data-ttu-id="fabf5-233">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="fabf5-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="fabf5-234">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="fabf5-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="fabf5-235">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="fabf5-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="fabf5-236">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-236">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="fabf5-238">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="fabf5-239">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="fabf5-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="fabf5-240">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="fabf5-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="fabf5-241">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="fabf5-242">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="fabf5-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fabf5-243">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fabf5-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fabf5-244">[Anterior: Comece a](xref:tutorials/razor-pages/razor-pages-start)
> [seguir: Páginas de navalha andaimes](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="fabf5-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fabf5-245">Nesta seção, são adicionadas aulas para o gerenciamento de filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)multiplataforma .</span><span class="sxs-lookup"><span data-stu-id="fabf5-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="fabf5-246">Aplicativos criados a partir de um modelo ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="fabf5-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="fabf5-247">As classes de modelo do aplicativo são usadas com [o Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="fabf5-248">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="fabf5-249">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="fabf5-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="fabf5-250">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="fabf5-251">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="fabf5-251">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fabf5-253">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="fabf5-254">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-254">Name the folder *Models*.</span></span>

<span data-ttu-id="fabf5-255">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-255">Right click the *Models* folder.</span></span> <span data-ttu-id="fabf5-256">Selecione **Adicionar** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="fabf5-257">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fabf5-259">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="fabf5-260">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-261">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fabf5-262">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="fabf5-263">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="fabf5-264">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="fabf5-265">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="fabf5-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="fabf5-266">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="fabf5-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="fabf5-267">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="fabf5-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="fabf5-268">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="fabf5-269">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="fabf5-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="fabf5-270">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="fabf5-270">Scaffold the movie model</span></span>

<span data-ttu-id="fabf5-271">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="fabf5-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="fabf5-272">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="fabf5-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-273">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fabf5-274">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="fabf5-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fabf5-275">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fabf5-276">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="fabf5-276">Name the folder *Movies*</span></span>

<span data-ttu-id="fabf5-277">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="fabf5-279">Na caixa de diálogo **Adicionar andaime,** selecione **Páginas de navalha usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="fabf5-281">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="fabf5-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="fabf5-282">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="fabf5-283">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="fabf5-284">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-284">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="fabf5-286">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="fabf5-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="fabf5-288">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="fabf5-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="fabf5-289">**Para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="fabf5-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="fabf5-290">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="fabf5-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-291">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fabf5-292">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="fabf5-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fabf5-293">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fabf5-294">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="fabf5-294">Name the folder *Movies*</span></span>

<span data-ttu-id="fabf5-295">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="fabf5-297">Na **caixa de diálogo Adicionar novos andaimes,** selecione **Páginas de navalha usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="fabf5-299">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="fabf5-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="fabf5-300">Na **classe Modelo,** queda, selecione ou digite **Filme**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="fabf5-301">Na linha **de classe de contexto de dados,** selecione o **RazorPagesMovieContext,** isso criará uma nova classe de contexto db com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="fabf5-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="fabf5-302">Neste caso, será **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="fabf5-303">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-303">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="fabf5-305">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="fabf5-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="fabf5-306">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="fabf5-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="fabf5-307">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="fabf5-307">Files created</span></span>

* <span data-ttu-id="fabf5-308">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="fabf5-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="fabf5-309">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="fabf5-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="fabf5-310">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="fabf5-310">File updated</span></span>

* <span data-ttu-id="fabf5-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fabf5-311">*Startup.cs*</span></span>

<span data-ttu-id="fabf5-312">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="fabf5-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="fabf5-313">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="fabf5-313">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fabf5-315">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="fabf5-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="fabf5-316">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="fabf5-316">Add an initial migration.</span></span>
* <span data-ttu-id="fabf5-317">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="fabf5-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="fabf5-318">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="fabf5-320">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="fabf5-320">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="fabf5-321">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="fabf5-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="fabf5-322">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="fabf5-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="fabf5-323">O `InitialCreate` argumento é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="fabf5-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="fabf5-324">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="fabf5-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="fabf5-325">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="fabf5-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="fabf5-326">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="fabf5-327">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-329">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="fabf5-330">Os comandos anteriores geram o seguinte aviso: "*Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Movie'. Isso fará com que os valores sejam silenciosamente truncados se não se encaixarem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do servidor SQL que pode acomodar todos os valores usando 'HasColumnType()'.* Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="fabf5-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fabf5-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fabf5-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="fabf5-332">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="fabf5-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="fabf5-333">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fabf5-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fabf5-334">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fabf5-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="fabf5-335">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="fabf5-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="fabf5-336">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="fabf5-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="fabf5-337">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="fabf5-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="fabf5-338">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fabf5-339">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="fabf5-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="fabf5-340">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="fabf5-341">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="fabf5-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="fabf5-342">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="fabf5-343">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="fabf5-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="fabf5-344">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fabf5-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="fabf5-345">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="fabf5-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="fabf5-346">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="fabf5-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="fabf5-347">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fabf5-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fabf5-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fabf5-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fabf5-349">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fabf5-350">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fabf5-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fabf5-351">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="fabf5-352">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="fabf5-352">Test the app</span></span>

* <span data-ttu-id="fabf5-353">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="fabf5-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="fabf5-354">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="fabf5-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="fabf5-355">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="fabf5-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="fabf5-356">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-356">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="fabf5-358">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="fabf5-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="fabf5-359">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="fabf5-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="fabf5-360">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="fabf5-360">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="fabf5-361">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="fabf5-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="fabf5-362">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="fabf5-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fabf5-363">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fabf5-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fabf5-364">[Anterior: Comece a](xref:tutorials/razor-pages/razor-pages-start)
> [seguir: Páginas de navalha andaimes](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="fabf5-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
