---
title: Adicionar um novo campo a uma Razor página no ASP.NET Core
author: rick-anderson
description: Mostra como adicionar um novo campo a uma Razor página com Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 683d6718f4dcdb73c45cbcf94f6ac4f477b71bcd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769728"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="e3375-103">Adicionar um novo campo em uma página Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3375-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="e3375-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e3375-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="e3375-105">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="e3375-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="e3375-106">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-106">Add a new field to the model.</span></span>
* <span data-ttu-id="e3375-107">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="e3375-108">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="e3375-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="e3375-109">Adiciona uma `__EFMigrationsHistory` tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="e3375-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="e3375-110">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="e3375-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="e3375-111">Verificação automática de esquema/modelo em sincronia torna mais fácil encontrar problemas de código/banco de dados inconsistente.</span><span class="sxs-lookup"><span data-stu-id="e3375-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="e3375-112">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="e3375-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="e3375-113">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="e3375-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="e3375-114">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3375-114">Build the app.</span></span>

<span data-ttu-id="e3375-115">Edite *Pages/Movies/Index.cshtml* e adicione um campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="e3375-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="e3375-116">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="e3375-116">Update the following pages:</span></span>

* <span data-ttu-id="e3375-117">Adicione o campo `Rating` às páginas Excluir e Detalhes.</span><span class="sxs-lookup"><span data-stu-id="e3375-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="e3375-118">Atualize [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="e3375-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="e3375-119">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="e3375-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="e3375-120">O aplicativo não funcionará até que o BD seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="e3375-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="e3375-121">Executar o aplicativo sem Atualizar o banco de dados `SqlException`gera um:</span><span class="sxs-lookup"><span data-stu-id="e3375-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="e3375-122">A `SqlException` exceção é causada pela classe de modelo de filme atualizada que é diferente do esquema da tabela de filmes do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="e3375-123">(Não há nenhuma coluna `Rating` na tabela de banco de dados.)</span><span class="sxs-lookup"><span data-stu-id="e3375-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="e3375-124">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="e3375-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="e3375-125">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="e3375-126">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="e3375-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="e3375-127">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="e3375-128">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="e3375-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="e3375-129">A remoção do BD em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com os dados de teste é muitas vezes uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3375-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="e3375-130">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="e3375-131">A vantagem dessa abordagem é que você mantém os dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="e3375-132">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="e3375-133">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="e3375-134">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="e3375-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="e3375-135">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="e3375-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="e3375-136">Uma alteração de amostra é mostrada abaixo, mas é recomendável fazer essa alteração em cada bloco `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="e3375-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="e3375-137">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="e3375-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="e3375-138">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="e3375-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e3375-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3375-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="e3375-140">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="e3375-140">Add a migration for the rating field</span></span>

<span data-ttu-id="e3375-141"> No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="e3375-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="e3375-142">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e3375-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="e3375-143">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="e3375-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="e3375-144">Compare o modelo `Movie` com o esquema de BD `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e3375-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="e3375-145">Crie um código para migrar o esquema de BD para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="e3375-146">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="e3375-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="e3375-147">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="e3375-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="e3375-148">O `Update-Database` comando informa à estrutura para aplicar as alterações de esquema ao banco de dados e para preservar os existentes.</span><span class="sxs-lookup"><span data-stu-id="e3375-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="e3375-149">Se você excluir todos os registros no BD, o inicializador propagará o BD e incluirá o campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="e3375-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="e3375-150">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="e3375-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="e3375-151">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="e3375-152">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="e3375-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="e3375-153">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="e3375-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="e3375-154">Clique com o botão direito do mouse no banco de dados e selecione *Excluir*.</span><span class="sxs-lookup"><span data-stu-id="e3375-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="e3375-155">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="e3375-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="e3375-156">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e3375-156">Select **OK**.</span></span>
* <span data-ttu-id="e3375-157">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e3375-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e3375-158">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e3375-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="e3375-159">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e3375-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="e3375-160">Exclua a pasta de migração.</span><span class="sxs-lookup"><span data-stu-id="e3375-160">Delete the migration folder.</span></span>  <span data-ttu-id="e3375-161">Use os comandos a seguir para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="e3375-162">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="e3375-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="e3375-163">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="e3375-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e3375-164">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e3375-164">Additional resources</span></span>

* [<span data-ttu-id="e3375-165">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="e3375-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="e3375-166">[Anterior: adicionando pesquisa](xref:tutorials/razor-pages/search)
> [seguinte: adicionando validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="e3375-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="e3375-167">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="e3375-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="e3375-168">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-168">Add a new field to the model.</span></span>
* <span data-ttu-id="e3375-169">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="e3375-170">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="e3375-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="e3375-171">Adiciona uma tabela ao banco de dados para acompanhar se o esquema do banco de dados está sincronizado com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="e3375-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="e3375-172">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="e3375-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="e3375-173">Verificação automática de esquema/modelo em sincronia torna mais fácil encontrar problemas de código/banco de dados inconsistente.</span><span class="sxs-lookup"><span data-stu-id="e3375-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="e3375-174">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="e3375-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="e3375-175">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="e3375-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="e3375-176">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3375-176">Build the app.</span></span>

<span data-ttu-id="e3375-177">Edite *Pages/Movies/Index.cshtml* e adicione um campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="e3375-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="e3375-178">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="e3375-178">Update the following pages:</span></span>

* <span data-ttu-id="e3375-179">Adicione o campo `Rating` às páginas Excluir e Detalhes.</span><span class="sxs-lookup"><span data-stu-id="e3375-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="e3375-180">Atualize [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="e3375-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="e3375-181">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="e3375-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="e3375-182">O aplicativo não funcionará até que o BD seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="e3375-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="e3375-183">Se for executado agora, o aplicativo gerará uma `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="e3375-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="e3375-184">Esse erro é causado devido à classe de modelo Movie atualizada ser diferente do esquema da tabela Movie do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="e3375-185">(Não há nenhuma coluna `Rating` na tabela de banco de dados.)</span><span class="sxs-lookup"><span data-stu-id="e3375-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="e3375-186">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="e3375-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="e3375-187">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="e3375-188">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="e3375-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="e3375-189">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="e3375-190">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="e3375-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="e3375-191">A remoção do BD em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com os dados de teste é muitas vezes uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3375-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="e3375-192">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="e3375-193">A vantagem dessa abordagem é que você mantém os dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="e3375-194">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="e3375-195">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="e3375-196">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="e3375-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="e3375-197">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="e3375-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="e3375-198">Uma alteração de amostra é mostrada abaixo, mas é recomendável fazer essa alteração em cada bloco `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="e3375-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="e3375-199">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="e3375-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="e3375-200">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="e3375-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e3375-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3375-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="e3375-202">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="e3375-202">Add a migration for the rating field</span></span>

<span data-ttu-id="e3375-203"> No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="e3375-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="e3375-204">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e3375-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="e3375-205">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="e3375-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="e3375-206">Compare o modelo `Movie` com o esquema de BD `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e3375-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="e3375-207">Crie um código para migrar o esquema de BD para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="e3375-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="e3375-208">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="e3375-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="e3375-209">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="e3375-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="e3375-210">O comando `Update-Database` informa à estrutura para aplicar as alterações de esquema no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="e3375-211">Se você excluir todos os registros no BD, o inicializador propagará o BD e incluirá o campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="e3375-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="e3375-212">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="e3375-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="e3375-213">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="e3375-214">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="e3375-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="e3375-215">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="e3375-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="e3375-216">Clique com o botão direito do mouse no banco de dados e selecione *Excluir*.</span><span class="sxs-lookup"><span data-stu-id="e3375-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="e3375-217">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="e3375-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="e3375-218">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e3375-218">Select **OK**.</span></span>
* <span data-ttu-id="e3375-219">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e3375-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e3375-220">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e3375-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="e3375-221">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e3375-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="e3375-222">Excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e3375-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="e3375-223">Para excluir o banco de dados, exclua o arquivo de banco de dados (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="e3375-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="e3375-224">Em seguida, execute o comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="e3375-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="e3375-225">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="e3375-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="e3375-226">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="e3375-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e3375-227">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e3375-227">Additional resources</span></span>

* [<span data-ttu-id="e3375-228">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="e3375-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="e3375-229">[Anterior: adicionando pesquisa](xref:tutorials/razor-pages/search)
> [seguinte: adicionando validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="e3375-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
