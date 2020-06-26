---
title: Usar ASP.NET Core SignalR comBlazor WebAssembly
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 5a58e7ae28842e2e8a0f3bae8f47e252839903fe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408871"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="b7347-103">Usar ASP.NET Core SignalR comBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b7347-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="b7347-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b7347-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b7347-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b7347-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="b7347-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="b7347-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b7347-107">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="b7347-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="b7347-108">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="b7347-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="b7347-109">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="b7347-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="b7347-110">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="b7347-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="b7347-111">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="b7347-111">Add Razor component code for chat</span></span>

<span data-ttu-id="b7347-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="b7347-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="b7347-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7347-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7347-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b7347-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7347-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7347-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7347-116">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="b7347-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7347-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7347-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7347-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7347-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="b7347-119">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="b7347-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="b7347-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7347-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="b7347-121">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="b7347-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="b7347-122">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="b7347-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7347-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7347-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="b7347-124">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="b7347-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="b7347-125">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="b7347-125">Create a new project.</span></span>

1. <span data-ttu-id="b7347-126">Selecione \*\* Blazor aplicativo\*\* e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="b7347-127">Digite `BlazorSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="b7347-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="b7347-128">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="b7347-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b7347-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-129">Select **Create**.</span></span>

1. <span data-ttu-id="b7347-130">Escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="b7347-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="b7347-131">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="b7347-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="b7347-132">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7347-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7347-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b7347-134">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b7347-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="b7347-135">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7347-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="b7347-136">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="b7347-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="b7347-137">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="b7347-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7347-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7347-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b7347-139">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="b7347-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="b7347-140">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="b7347-141">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="b7347-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="b7347-142">Escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="b7347-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b7347-143">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-143">Select **Next**.</span></span>

   <span data-ttu-id="b7347-144">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="b7347-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="b7347-145">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="b7347-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="b7347-146">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="b7347-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="b7347-147">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="b7347-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="b7347-148">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-148">Select **Next**.</span></span>

1. <span data-ttu-id="b7347-149">No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="b7347-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="b7347-150">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-150">Select **Create**.</span></span>

   <span data-ttu-id="b7347-151">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="b7347-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="b7347-152">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="b7347-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="b7347-153">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="b7347-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b7347-154">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7347-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b7347-155">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b7347-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="b7347-156">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="b7347-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7347-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7347-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="b7347-158">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b7347-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="b7347-159">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="b7347-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="b7347-160">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="b7347-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="b7347-161">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="b7347-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="b7347-162">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="b7347-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="b7347-163">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="b7347-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7347-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7347-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="b7347-165">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="b7347-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7347-166">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7347-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b7347-167">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b7347-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="b7347-168">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="b7347-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="b7347-169">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="b7347-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="b7347-170">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="b7347-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="b7347-171">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="b7347-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b7347-172">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7347-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b7347-173">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="b7347-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="b7347-174">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="b7347-174">Add a SignalR hub</span></span>

<span data-ttu-id="b7347-175">No `BlazorSignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="b7347-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="b7347-176">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="b7347-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="b7347-177">No projeto `BlazorSignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="b7347-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="b7347-178">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="b7347-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="b7347-179">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b7347-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="b7347-180">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b7347-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="b7347-181">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="b7347-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="b7347-182">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="b7347-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="b7347-183">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="b7347-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="b7347-184">No projeto `BlazorSignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="b7347-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="b7347-185">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="b7347-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="b7347-186">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="b7347-186">Run the app</span></span>

1. <span data-ttu-id="b7347-187">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="b7347-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7347-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7347-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b7347-189">Em **Gerenciador de soluções**, selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="b7347-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="b7347-190">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="b7347-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="b7347-191">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="b7347-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b7347-192">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="b7347-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b7347-193">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="b7347-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="b7347-194">![SignalRBlazor WebAssemblyaplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="b7347-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="b7347-195">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b7347-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7347-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7347-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b7347-197">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="b7347-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="b7347-198">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="b7347-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="b7347-199">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="b7347-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b7347-200">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="b7347-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b7347-201">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="b7347-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="b7347-202">![SignalRBlazor WebAssemblyaplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="b7347-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="b7347-203">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b7347-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7347-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7347-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b7347-205">Na barra lateral da **solução** , selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="b7347-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="b7347-206">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="b7347-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="b7347-207">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="b7347-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b7347-208">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="b7347-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b7347-209">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="b7347-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="b7347-210">![SignalRBlazor WebAssemblyaplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="b7347-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="b7347-211">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b7347-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b7347-212">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7347-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b7347-213">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="b7347-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="b7347-214">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="b7347-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="b7347-215">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="b7347-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="b7347-216">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="b7347-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="b7347-217">![SignalRBlazor WebAssemblyaplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="b7347-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="b7347-218">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="b7347-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="b7347-219">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b7347-219">Next steps</span></span>

<span data-ttu-id="b7347-220">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="b7347-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b7347-221">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="b7347-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="b7347-222">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="b7347-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="b7347-223">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="b7347-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="b7347-224">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="b7347-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="b7347-225">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="b7347-225">Add Razor component code for chat</span></span>

<span data-ttu-id="b7347-226">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="b7347-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="b7347-227">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b7347-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="b7347-228">[SignalRnegociação entre origens para autenticação](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="b7347-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
