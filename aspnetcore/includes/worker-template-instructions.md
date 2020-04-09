# <a name="visual-studio"></a>[<span data-ttu-id="7d368-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d368-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d368-102">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="7d368-102">Create a new project.</span></span>
1. <span data-ttu-id="7d368-103">Selecione **serviço do trabalhador**.</span><span class="sxs-lookup"><span data-stu-id="7d368-103">Select **Worker Service**.</span></span> <span data-ttu-id="7d368-104">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7d368-104">Select **Next**.</span></span>
1. <span data-ttu-id="7d368-105">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="7d368-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7d368-106">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7d368-106">Select **Create**.</span></span>
1. <span data-ttu-id="7d368-107">No Criar uma nova caixa de diálogo **de serviço trabalhador,** selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7d368-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d368-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7d368-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7d368-109">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="7d368-109">Create a new project.</span></span>
1. <span data-ttu-id="7d368-110">Selecione **App** em **.NET Core** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="7d368-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="7d368-111">Selecione **Trabalhador** em **ASP.NET Núcleo**.</span><span class="sxs-lookup"><span data-stu-id="7d368-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="7d368-112">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7d368-112">Select **Next**.</span></span>
1. <span data-ttu-id="7d368-113">Selecione **.NET Core 3.0** ou posterior para o **Quadro de destino**.</span><span class="sxs-lookup"><span data-stu-id="7d368-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="7d368-114">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7d368-114">Select **Next**.</span></span>
1. <span data-ttu-id="7d368-115">Forneça um nome no campo Nome do **projeto.**</span><span class="sxs-lookup"><span data-stu-id="7d368-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="7d368-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7d368-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7d368-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="7d368-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7d368-118">Use o modelo de Serviço de Trabalho (`worker`) com o comando [dotnet novo](/dotnet/core/tools/dotnet-new) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="7d368-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="7d368-119">No exemplo a seguir, um aplicativo de Serviço de Trabalho é criado com o nome `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="7d368-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="7d368-120">Uma pasta para o aplicativo `ContosoWorker` é criada automaticamente quando o comando é executado.</span><span class="sxs-lookup"><span data-stu-id="7d368-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
