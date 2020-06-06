<span data-ttu-id="0367c-101">Execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="0367c-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0367c-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0367c-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0367c-103">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar**  >  **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0367c-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0367c-104">No painel esquerdo da caixa de diálogo **Adicionar novo item do com Scaffold** , selecione **identidade**  >  **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0367c-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="0367c-105">Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="0367c-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="0367c-106">Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta:</span><span class="sxs-lookup"><span data-stu-id="0367c-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="0367c-107">`~/Pages/Shared/_Layout.cshtml`para Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0367c-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="0367c-108">`~/Views/Shared/_Layout.cshtml`para projetos do MVC</span><span class="sxs-lookup"><span data-stu-id="0367c-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="0367c-109">Os aplicativos de servidor mais amseriais criados a partir do modelo de servidor mais incrivelmente ( `blazorserver` ) não são configurados para Razor pages ou MVC por padrão.</span><span class="sxs-lookup"><span data-stu-id="0367c-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="0367c-110">Deixe a entrada da página de layout em branco.</span><span class="sxs-lookup"><span data-stu-id="0367c-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="0367c-111">Selecione o **+** botão para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="0367c-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="0367c-112">Aceite o valor padrão ou especifique uma classe (por exemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="0367c-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="0367c-113">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0367c-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0367c-114">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0367c-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0367c-115">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="0367c-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="0367c-116">Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="0367c-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="0367c-117">Execute os seguintes comandos no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="0367c-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="0367c-118">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="0367c-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="0367c-119">Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="0367c-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="0367c-120">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0367c-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
