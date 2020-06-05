::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="126f7-101">Execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="126f7-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="126f7-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="126f7-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="126f7-103">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="126f7-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="126f7-104">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="126f7-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="126f7-105">Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="126f7-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="126f7-106">Selecione a página de layout existente para que o arquivo de layout não seja substituído por marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="126f7-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="126f7-107">Quando um arquivo \* \_ layout. cshtml\* existente é selecionado, ele **não** é substituído.</span><span class="sxs-lookup"><span data-stu-id="126f7-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="126f7-108">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="126f7-108">For example:</span></span>
    * <span data-ttu-id="126f7-109">`~/Pages/Shared/_Layout.cshtml`para projetos de servidor Razor Pages ou mais incrivelmente com a infraestrutura de Razor Pages existente</span><span class="sxs-lookup"><span data-stu-id="126f7-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="126f7-110">`~/Views/Shared/_Layout.cshtml`para projetos do MVC ou projetos de servidor mais incrivelmente com a infraestrutura MVC existente</span><span class="sxs-lookup"><span data-stu-id="126f7-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="126f7-111">Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="126f7-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="126f7-112">Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="126f7-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="126f7-113">Selecione sua classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="126f7-113">Select your data context class.</span></span>
  * <span data-ttu-id="126f7-114">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="126f7-114">Select **Add**.</span></span>
* <span data-ttu-id="126f7-115">Para criar um novo contexto de usuário e possivelmente criar uma classe de usuário personalizada para identidade:</span><span class="sxs-lookup"><span data-stu-id="126f7-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="126f7-116">Selecione o **+** botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="126f7-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="126f7-117">Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="126f7-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="126f7-118">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="126f7-118">Select **Add**.</span></span>

<span data-ttu-id="126f7-119">Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="126f7-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="126f7-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="126f7-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="126f7-121">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="126f7-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="126f7-122">Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="126f7-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="126f7-123">Execute os seguintes comandos no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="126f7-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="126f7-124">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="126f7-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="126f7-125">Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="126f7-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="126f7-126">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="126f7-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="126f7-127">Use o nome totalmente qualificado correto para seu contexto de BD:</span><span class="sxs-lookup"><span data-stu-id="126f7-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="126f7-128">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="126f7-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="126f7-129">Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas.</span><span class="sxs-lookup"><span data-stu-id="126f7-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="126f7-130">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="126f7-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="126f7-131">Se você executar o scaffolder de identidade sem especificar o `--files` sinalizador ou o `--useDefaultUI` sinalizador, todas as páginas de interface do usuário de identidade disponíveis serão criadas em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="126f7-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="126f7-132">Execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="126f7-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="126f7-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="126f7-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="126f7-134">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="126f7-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="126f7-135">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="126f7-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="126f7-136">Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="126f7-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="126f7-137">Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="126f7-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="126f7-138">Quando um arquivo \* \_ layout. cshtml\* existente é selecionado, ele **não** é substituído.</span><span class="sxs-lookup"><span data-stu-id="126f7-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="126f7-139">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="126f7-139">For example:</span></span>
    * <span data-ttu-id="126f7-140">`~/Pages/Shared/_Layout.cshtml`para Razor Pages</span><span class="sxs-lookup"><span data-stu-id="126f7-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="126f7-141">`~/Views/Shared/_Layout.cshtml`para projetos do MVC</span><span class="sxs-lookup"><span data-stu-id="126f7-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="126f7-142">Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="126f7-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="126f7-143">Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="126f7-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="126f7-144">Selecione sua classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="126f7-144">Select your data context class.</span></span>
  * <span data-ttu-id="126f7-145">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="126f7-145">Select **Add**.</span></span>
* <span data-ttu-id="126f7-146">Para criar um novo contexto de usuário e possivelmente criar uma classe de usuário personalizada para identidade:</span><span class="sxs-lookup"><span data-stu-id="126f7-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="126f7-147">Selecione o **+** botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="126f7-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="126f7-148">Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="126f7-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="126f7-149">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="126f7-149">Select **Add**.</span></span>

<span data-ttu-id="126f7-150">Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="126f7-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="126f7-151">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="126f7-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="126f7-152">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="126f7-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="126f7-153">Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="126f7-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="126f7-154">Execute os seguintes comandos no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="126f7-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="126f7-155">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="126f7-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="126f7-156">Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="126f7-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="126f7-157">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="126f7-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="126f7-158">Use o nome totalmente qualificado correto para seu contexto de BD:</span><span class="sxs-lookup"><span data-stu-id="126f7-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="126f7-159">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="126f7-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="126f7-160">Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas.</span><span class="sxs-lookup"><span data-stu-id="126f7-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="126f7-161">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="126f7-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="126f7-162">Se você executar o scaffolder de identidade sem especificar o `--files` sinalizador ou o `--useDefaultUI` sinalizador, todas as páginas de interface do usuário de identidade disponíveis serão criadas em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="126f7-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
