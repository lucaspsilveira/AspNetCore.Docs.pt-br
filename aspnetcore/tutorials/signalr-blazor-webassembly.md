---
title: Usar ASP.NET Core SignalR com Blazor Webassembly
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor Webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 720f534426cc0e2b32778e49050c7f7d75ecd60d
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679586"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="e05e4-103">Usar ASP.NET Core SignalR com Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="e05e4-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="e05e4-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e05e4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e05e4-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="e05e4-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="e05e4-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="e05e4-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e05e4-107">Criar um Blazor projeto de aplicativo hospedado pelo Webassembly</span><span class="sxs-lookup"><span data-stu-id="e05e4-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="e05e4-108">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="e05e4-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="e05e4-109">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="e05e4-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="e05e4-110">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="e05e4-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="e05e4-111">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="e05e4-111">Add Razor component code for chat</span></span>

<span data-ttu-id="e05e4-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="e05e4-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="e05e4-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e05e4-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e05e4-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e05e4-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e05e4-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e05e4-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e05e4-116">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="e05e4-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e05e4-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e05e4-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e05e4-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e05e4-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="e05e4-119">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e05e4-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="e05e4-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e05e4-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="e05e4-121">Criar um Blazor projeto de aplicativo Webassembly hospedado</span><span class="sxs-lookup"><span data-stu-id="e05e4-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="e05e4-122">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="e05e4-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e05e4-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e05e4-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="e05e4-124">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="e05e4-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="e05e4-125">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="e05e4-125">Create a new project.</span></span>

1. <span data-ttu-id="e05e4-126">Selecione \*\* Blazor aplicativo\*\* e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="e05e4-127">Digite "BlazorSignalRApp" no campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="e05e4-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="e05e4-128">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="e05e4-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="e05e4-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-129">Select **Create**.</span></span>

1. <span data-ttu-id="e05e4-130">Escolha o modelo de \*\* Blazor aplicativo Webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="e05e4-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="e05e4-131">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="e05e4-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="e05e4-132">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="e05e4-133">Se você atualizou ou instalou uma nova versão do Visual Studio e o Blazor modelo Webassembly não aparece na interface do usuário do vs, reinstale o modelo usando o `dotnet new` comando mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e05e4-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e05e4-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e05e4-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e05e4-135">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e05e4-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="e05e4-136">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e05e4-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="e05e4-137">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="e05e4-138">Visual Studio Code adiciona automaticamente a pasta *. vscode* com o *launch.jsgerado no* e *tasks.jsem* arquivos.</span><span class="sxs-lookup"><span data-stu-id="e05e4-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e05e4-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e05e4-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e05e4-140">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="e05e4-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="e05e4-141">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="e05e4-142">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="e05e4-143">Escolha o modelo de \*\* Blazor aplicativo Webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="e05e4-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="e05e4-144">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-144">Select **Next**.</span></span>

   <span data-ttu-id="e05e4-145">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="e05e4-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="e05e4-146">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="e05e4-147">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="e05e4-148">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="e05e4-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="e05e4-149">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-149">Select **Next**.</span></span>

1. <span data-ttu-id="e05e4-150">No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="e05e4-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="e05e4-151">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-151">Select **Create**.</span></span>

   <span data-ttu-id="e05e4-152">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="e05e4-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="e05e4-153">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="e05e4-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="e05e4-154">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="e05e4-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e05e4-155">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e05e4-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="e05e4-156">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e05e4-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="e05e4-157">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="e05e4-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e05e4-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e05e4-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="e05e4-159">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="e05e4-160">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="e05e4-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="e05e4-161">Com **procurar** selecionado, digite "Microsoft. AspNetCore. SignalR . Cliente "na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="e05e4-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="e05e4-162">Nos resultados da pesquisa, selecione o [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)Pacote do cliente e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="e05e4-163">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="e05e4-164">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="e05e4-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e05e4-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e05e4-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="e05e4-166">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e05e4-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e05e4-167">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e05e4-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e05e4-168">Na barra lateral da **solução** , clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="e05e4-169">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="e05e4-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="e05e4-170">Com **procurar** selecionado, digite "Microsoft. AspNetCore. SignalR . Cliente "na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="e05e4-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="e05e4-171">Nos resultados da pesquisa, marque a caixa de seleção ao lado de [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)Pacote do cliente e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="e05e4-172">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="e05e4-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e05e4-173">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e05e4-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="e05e4-174">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e05e4-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="e05e4-175">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="e05e4-175">Add a SignalR hub</span></span>

<span data-ttu-id="e05e4-176">No projeto **BlazorSignalRApp. Server** , crie uma pasta *hubs* (plural) e adicione a seguinte `ChatHub` classe (*hubs/ChatHub. cs*):</span><span class="sxs-lookup"><span data-stu-id="e05e4-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="e05e4-177">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="e05e4-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="e05e4-178">No projeto **BlazorSignalRApp. Server** , abra o arquivo *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="e05e4-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="e05e4-179">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="e05e4-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="e05e4-180">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e05e4-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="e05e4-181">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e05e4-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="e05e4-182">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="e05e4-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="e05e4-183">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="e05e4-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="e05e4-184">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="e05e4-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="e05e4-185">No projeto **BlazorSignalRApp. Client** , abra o arquivo *pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="e05e4-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="e05e4-186">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e05e4-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="e05e4-187">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e05e4-187">Run the app</span></span>

1. <span data-ttu-id="e05e4-188">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="e05e4-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e05e4-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e05e4-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e05e4-190">Em **Gerenciador de soluções**, selecione o projeto **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="e05e4-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="e05e4-191">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="e05e4-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="e05e4-192">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="e05e4-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e05e4-193">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="e05e4-194">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="e05e4-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="e05e4-195">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="e05e4-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="e05e4-196">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="e05e4-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e05e4-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e05e4-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e05e4-198">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor (*. vscode/launch.json*), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="e05e4-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="e05e4-199">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="e05e4-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="e05e4-200">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="e05e4-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e05e4-201">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="e05e4-202">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="e05e4-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="e05e4-203">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="e05e4-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="e05e4-204">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="e05e4-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e05e4-205">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e05e4-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e05e4-206">Na barra lateral da **solução** , selecione o projeto **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="e05e4-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="e05e4-207">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="e05e4-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="e05e4-208">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="e05e4-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e05e4-209">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="e05e4-210">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="e05e4-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="e05e4-211">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="e05e4-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="e05e4-212">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="e05e4-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e05e4-213">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e05e4-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="e05e4-214">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e05e4-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="e05e4-215">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="e05e4-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="e05e4-216">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e05e4-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="e05e4-217">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="e05e4-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="e05e4-218">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="e05e4-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="e05e4-219">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="e05e4-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="e05e4-220">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e05e4-220">Next steps</span></span>

<span data-ttu-id="e05e4-221">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="e05e4-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e05e4-222">Criar um Blazor projeto de aplicativo hospedado pelo Webassembly</span><span class="sxs-lookup"><span data-stu-id="e05e4-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="e05e4-223">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="e05e4-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="e05e4-224">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="e05e4-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="e05e4-225">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="e05e4-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="e05e4-226">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="e05e4-226">Add Razor component code for chat</span></span>

<span data-ttu-id="e05e4-227">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="e05e4-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="e05e4-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e05e4-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="e05e4-229">[SignalRnegociação entre origens para autenticação](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="e05e4-229">[SignalR cross-origin negotiation for authentication](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span></span>
