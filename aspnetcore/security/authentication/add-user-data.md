---
title: Adicionar, baixar e excluir dados do usuário para identidade em um projeto ASP.NET Core
author: rick-anderson
description: Aprenda a adicionar dados personalizados do usuário à Identidade em um projeto ASP.NET Core. Excluir dados por GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501223"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="1c6be-104">Adicione, baixe e exclua dados personalizados do usuário para identidade em um projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c6be-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="1c6be-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c6be-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1c6be-106">Este artigo mostra como:</span><span class="sxs-lookup"><span data-stu-id="1c6be-106">This article shows how to:</span></span>

* <span data-ttu-id="1c6be-107">Adicione dados de usuário personalizados a um aplicativo web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c6be-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="1c6be-108">Marque o modelo de <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> dados personalizados do usuário com o atributo para que ele esteja automaticamente disponível para download e exclusão.</span><span class="sxs-lookup"><span data-stu-id="1c6be-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="1c6be-109">Tornar os dados capazes de serem baixados e excluídos ajuda a atender aos requisitos [do GDPR.](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="1c6be-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="1c6be-110">A amostra do projeto é criada a partir de um aplicativo web Razor Pages, mas as instruções são semelhantes para um aplicativo web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1c6be-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="1c6be-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c6be-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c6be-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="1c6be-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="1c6be-113">Criar um aplicativo Web do Razor</span><span class="sxs-lookup"><span data-stu-id="1c6be-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c6be-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c6be-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="1c6be-115">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="1c6be-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="1c6be-116">Nomeie o projeto **WebApp1** se quiser corresponder ao namespace do código de amostra de [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="1c6be-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="1c6be-117">Selecione **ASP.NET ok do aplicativo** > web **principal**</span><span class="sxs-lookup"><span data-stu-id="1c6be-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="1c6be-118">Selecione **ASP.NET Núcleo 3.0** no dropdown</span><span class="sxs-lookup"><span data-stu-id="1c6be-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="1c6be-119">Selecione **o aplicativo da** > **Web OK**</span><span class="sxs-lookup"><span data-stu-id="1c6be-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="1c6be-120">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="1c6be-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="1c6be-121">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="1c6be-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="1c6be-122">Nomeie o projeto **WebApp1** se quiser corresponder ao namespace do código de amostra de [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="1c6be-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="1c6be-123">Selecione **ASP.NET ok do aplicativo** > web **principal**</span><span class="sxs-lookup"><span data-stu-id="1c6be-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="1c6be-124">Selecione **ASP.NET Núcleo 2.2** na estada</span><span class="sxs-lookup"><span data-stu-id="1c6be-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="1c6be-125">Selecione **o aplicativo da** > **Web OK**</span><span class="sxs-lookup"><span data-stu-id="1c6be-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="1c6be-126">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="1c6be-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="1c6be-127">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c6be-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="1c6be-128">Execute a pasta de identidade</span><span class="sxs-lookup"><span data-stu-id="1c6be-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c6be-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c6be-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c6be-130">A partir **do Solution Explorer**, clique com o botão direito do mouse no projeto > **adicionar** > **novo item de andaime**.</span><span class="sxs-lookup"><span data-stu-id="1c6be-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1c6be-131">No painel esquerdo da caixa de diálogo **Adicionar andaime,** selecione **'Adicionar''** > **Add**</span><span class="sxs-lookup"><span data-stu-id="1c6be-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="1c6be-132">Na **caixa de diálogo Adicionar identidade,** as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="1c6be-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="1c6be-133">Selecione o arquivo de layout existente *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1c6be-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="1c6be-134">Selecione os seguintes arquivos a serem supervisionados:</span><span class="sxs-lookup"><span data-stu-id="1c6be-134">Select the following files to override:</span></span>
    * <span data-ttu-id="1c6be-135">**Conta/Registro**</span><span class="sxs-lookup"><span data-stu-id="1c6be-135">**Account/Register**</span></span>
    * <span data-ttu-id="1c6be-136">**Conta/Gerenciar/Índice**</span><span class="sxs-lookup"><span data-stu-id="1c6be-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="1c6be-137">Selecione **+** o botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="1c6be-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="1c6be-138">Aceite o tipo (**WebApp1.Models.WebApp1Context** se o projeto for chamado **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="1c6be-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="1c6be-139">Selecione **+** o botão para criar uma nova **classe de usuário**.</span><span class="sxs-lookup"><span data-stu-id="1c6be-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="1c6be-140">Aceite o tipo **(WebApp1User** se o projeto for chamado **WebApp1)**> **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="1c6be-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="1c6be-141">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="1c6be-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1c6be-142">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c6be-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1c6be-143">Se você não tiver instalado anteriormente a pasta de scaffolder ASP.NET Core, instale-a agora:</span><span class="sxs-lookup"><span data-stu-id="1c6be-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="1c6be-144">Adicione uma referência de pacote ao [arquivo Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo project (.csproj).</span><span class="sxs-lookup"><span data-stu-id="1c6be-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="1c6be-145">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="1c6be-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="1c6be-146">Execute o seguinte comando para listar as opções de pasta de scaffolder identidade:</span><span class="sxs-lookup"><span data-stu-id="1c6be-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="1c6be-147">Na pasta do projeto, execute a pasta identidade:</span><span class="sxs-lookup"><span data-stu-id="1c6be-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="1c6be-148">Siga as instruções em [Migrações, UseAutenticação e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="1c6be-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="1c6be-149">Crie uma migração e atualize o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1c6be-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="1c6be-150">Adicione `UseAuthentication` a `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1c6be-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="1c6be-151">Adicione `<partial name="_LoginPartial" />` ao arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="1c6be-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="1c6be-152">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="1c6be-152">Test the app:</span></span>
  * <span data-ttu-id="1c6be-153">Registrar um usuário</span><span class="sxs-lookup"><span data-stu-id="1c6be-153">Register a user</span></span>
  * <span data-ttu-id="1c6be-154">Selecione o novo nome de usuário (ao lado do link **Logout).**</span><span class="sxs-lookup"><span data-stu-id="1c6be-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="1c6be-155">Você pode precisar expandir a janela ou selecionar o ícone da barra de navegação para mostrar o nome do usuário e outros links.</span><span class="sxs-lookup"><span data-stu-id="1c6be-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="1c6be-156">Selecione a guia **Dados Pessoais.**</span><span class="sxs-lookup"><span data-stu-id="1c6be-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="1c6be-157">Selecione o botão **Download** e examineo o arquivo *PersonalData.json.*</span><span class="sxs-lookup"><span data-stu-id="1c6be-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="1c6be-158">Teste o botão **Excluir,** que exclui o logado no usuário.</span><span class="sxs-lookup"><span data-stu-id="1c6be-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="1c6be-159">Adicionar dados de usuário personalizados ao DB de identidade</span><span class="sxs-lookup"><span data-stu-id="1c6be-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="1c6be-160">Atualize `IdentityUser` a classe derivada com propriedades personalizadas.</span><span class="sxs-lookup"><span data-stu-id="1c6be-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="1c6be-161">Se você nomeou o projeto WebApp1, o arquivo será chamado *de Áreas/Identidade/Dados/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="1c6be-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="1c6be-162">Atualize o arquivo com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1c6be-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="1c6be-163">As propriedades com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:</span><span class="sxs-lookup"><span data-stu-id="1c6be-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="1c6be-164">Excluído quando as *áreas/identidade/páginas/conta/gerenciar/excluirpersonalData.cshtml* Razor Page `UserManager.Delete`chamadas .</span><span class="sxs-lookup"><span data-stu-id="1c6be-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="1c6be-165">Incluído nos dados baixados pelas *Áreas/Identidade/Páginas/Conta/Gerenciar/DownloadPersonalData.cshtml* Razor Page.</span><span class="sxs-lookup"><span data-stu-id="1c6be-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="1c6be-166">Atualize a página Conta/Gerenciar/Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="1c6be-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="1c6be-167">Atualize `InputModel` o in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* com o seguinte código destacado:</span><span class="sxs-lookup"><span data-stu-id="1c6be-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="1c6be-168">Atualize as *áreas/identidade/páginas/conta/gerenciar/índice.cshtml* com a seguinte marcação destacada:</span><span class="sxs-lookup"><span data-stu-id="1c6be-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="1c6be-169">Atualize as *áreas/identidade/páginas/conta/gerenciar/índice.cshtml* com a seguinte marcação destacada:</span><span class="sxs-lookup"><span data-stu-id="1c6be-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="1c6be-170">Atualize a página Conta/Register.cshtml</span><span class="sxs-lookup"><span data-stu-id="1c6be-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="1c6be-171">Atualize `InputModel` o in *Areas/Identity/Pages/Account/Register.cshtml.cs* com o seguinte código destacado:</span><span class="sxs-lookup"><span data-stu-id="1c6be-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="1c6be-172">Atualize as *áreas/identidade/páginas/conta/register.cshtml* com a seguinte marcação destacada:</span><span class="sxs-lookup"><span data-stu-id="1c6be-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="1c6be-173">Atualize as *áreas/identidade/páginas/conta/register.cshtml* com a seguinte marcação destacada:</span><span class="sxs-lookup"><span data-stu-id="1c6be-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="1c6be-174">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="1c6be-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="1c6be-175">Adicionar uma migração para os dados personalizados do usuário</span><span class="sxs-lookup"><span data-stu-id="1c6be-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1c6be-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c6be-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c6be-177">No visual studio **package manager console**:</span><span class="sxs-lookup"><span data-stu-id="1c6be-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="1c6be-178">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c6be-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="1c6be-179">Teste criar, visualizar, baixar, excluir dados personalizados do usuário</span><span class="sxs-lookup"><span data-stu-id="1c6be-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="1c6be-180">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="1c6be-180">Test the app:</span></span>

* <span data-ttu-id="1c6be-181">Registre um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="1c6be-181">Register a new user.</span></span>
* <span data-ttu-id="1c6be-182">Veja os dados personalizados do usuário na `/Identity/Account/Manage` página.</span><span class="sxs-lookup"><span data-stu-id="1c6be-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="1c6be-183">Baixe e visualize os `/Identity/Account/Manage/PersonalData` dados pessoais dos usuários a partir da página.</span><span class="sxs-lookup"><span data-stu-id="1c6be-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="1c6be-184">Adicionar reivindicações à identidade usando IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="1c6be-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="1c6be-185">Reivindicações adicionais podem ser adicionadas `IUserClaimsPrincipalFactory<T>` ao ASP.NET Identidade Central usando a interface.</span><span class="sxs-lookup"><span data-stu-id="1c6be-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="1c6be-186">Esta classe pode ser adicionada `Startup.ConfigureServices` ao aplicativo no método.</span><span class="sxs-lookup"><span data-stu-id="1c6be-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1c6be-187">Adicione a implementação personalizada da classe da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="1c6be-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="1c6be-188">O código de `ApplicationUser` demonstração usa a classe.</span><span class="sxs-lookup"><span data-stu-id="1c6be-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="1c6be-189">Esta classe `IsAdmin` adiciona uma propriedade que é usada para adicionar a reivindicação adicional.</span><span class="sxs-lookup"><span data-stu-id="1c6be-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="1c6be-190">O `AdditionalUserClaimsPrincipalFactory` implemento `UserClaimsPrincipalFactory` da interface.</span><span class="sxs-lookup"><span data-stu-id="1c6be-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="1c6be-191">Uma nova reivindicação de `ClaimsPrincipal`função é adicionada ao .</span><span class="sxs-lookup"><span data-stu-id="1c6be-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="1c6be-192">A reivindicação adicional pode então ser usada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1c6be-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="1c6be-193">Em uma Página `IAuthorizationService` de Navalha, a instância pode ser usada para acessar o valor da solicitação.</span><span class="sxs-lookup"><span data-stu-id="1c6be-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
