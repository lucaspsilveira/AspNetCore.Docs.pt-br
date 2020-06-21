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
ms.openlocfilehash: 3f8aeec1e0471bab5034d1dcc8a42023f6b13c0d
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122094"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="2a5ca-103">Usar ASP.NET Core SignalR com Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="2a5ca-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="2a5ca-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2a5ca-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="2a5ca-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a5ca-107">Criar um Blazor projeto de aplicativo hospedado pelo Webassembly</span><span class="sxs-lookup"><span data-stu-id="2a5ca-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2a5ca-108">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="2a5ca-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="2a5ca-109">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2a5ca-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="2a5ca-110">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2a5ca-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2a5ca-111">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="2a5ca-111">Add Razor component code for chat</span></span>

<span data-ttu-id="2a5ca-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="2a5ca-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a5ca-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2a5ca-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="2a5ca-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a5ca-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a5ca-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a5ca-116">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="2a5ca-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a5ca-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a5ca-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a5ca-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a5ca-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="2a5ca-119">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="2a5ca-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2a5ca-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2a5ca-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="2a5ca-121">Criar um Blazor projeto de aplicativo Webassembly hospedado</span><span class="sxs-lookup"><span data-stu-id="2a5ca-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="2a5ca-122">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a5ca-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a5ca-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="2a5ca-124">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="2a5ca-125">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-125">Create a new project.</span></span>

1. <span data-ttu-id="2a5ca-126">Selecione \*\* Blazor aplicativo\*\* e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="2a5ca-127">Digite `BlazorSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="2a5ca-128">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2a5ca-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-129">Select **Create**.</span></span>

1. <span data-ttu-id="2a5ca-130">Escolha o modelo de \*\* Blazor aplicativo Webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="2a5ca-131">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="2a5ca-132">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a5ca-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a5ca-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2a5ca-134">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="2a5ca-135">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="2a5ca-136">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="2a5ca-137">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a5ca-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a5ca-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2a5ca-139">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="2a5ca-140">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="2a5ca-141">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="2a5ca-142">Escolha o modelo de \*\* Blazor aplicativo Webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2a5ca-143">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-143">Select **Next**.</span></span>

   <span data-ttu-id="2a5ca-144">Confirme as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="2a5ca-145">**Estrutura de destino** definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="2a5ca-146">**Autenticação** definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="2a5ca-147">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="2a5ca-148">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-148">Select **Next**.</span></span>

1. <span data-ttu-id="2a5ca-149">No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="2a5ca-150">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-150">Select **Create**.</span></span>

   <span data-ttu-id="2a5ca-151">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="2a5ca-152">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="2a5ca-153">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="2a5ca-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2a5ca-154">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2a5ca-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2a5ca-155">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="2a5ca-156">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="2a5ca-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a5ca-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a5ca-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="2a5ca-158">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2a5ca-159">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="2a5ca-160">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="2a5ca-161">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="2a5ca-162">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="2a5ca-163">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a5ca-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a5ca-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="2a5ca-165">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a5ca-166">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a5ca-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2a5ca-167">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2a5ca-168">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="2a5ca-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="2a5ca-169">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="2a5ca-170">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="2a5ca-171">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2a5ca-172">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2a5ca-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2a5ca-173">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="2a5ca-174">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2a5ca-174">Add a SignalR hub</span></span>

<span data-ttu-id="2a5ca-175">No `BlazorSignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="2a5ca-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="2a5ca-176">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2a5ca-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="2a5ca-177">No projeto `BlazorSignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="2a5ca-178">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="2a5ca-179">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2a5ca-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="2a5ca-180">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="2a5ca-181">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="2a5ca-182">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="2a5ca-183">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="2a5ca-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="2a5ca-184">No projeto `BlazorSignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="2a5ca-185">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="2a5ca-186">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="2a5ca-186">Run the app</span></span>

1. <span data-ttu-id="2a5ca-187">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a5ca-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a5ca-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2a5ca-189">Em **Gerenciador de soluções**, selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="2a5ca-190">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2a5ca-191">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2a5ca-192">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2a5ca-193">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2a5ca-194">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2a5ca-195">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a5ca-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a5ca-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2a5ca-197">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="2a5ca-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="2a5ca-198">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2a5ca-199">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2a5ca-200">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2a5ca-201">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2a5ca-202">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2a5ca-203">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a5ca-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a5ca-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2a5ca-205">Na barra lateral da **solução** , selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="2a5ca-206">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2a5ca-207">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2a5ca-208">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2a5ca-209">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2a5ca-210">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2a5ca-211">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2a5ca-212">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2a5ca-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2a5ca-213">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="2a5ca-214">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2a5ca-215">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="2a5ca-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2a5ca-216">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2a5ca-217">![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2a5ca-218">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="2a5ca-219">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2a5ca-219">Next steps</span></span>

<span data-ttu-id="2a5ca-220">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a5ca-221">Criar um Blazor projeto de aplicativo hospedado pelo Webassembly</span><span class="sxs-lookup"><span data-stu-id="2a5ca-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2a5ca-222">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="2a5ca-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="2a5ca-223">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2a5ca-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="2a5ca-224">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2a5ca-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2a5ca-225">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="2a5ca-225">Add Razor component code for chat</span></span>

<span data-ttu-id="2a5ca-226">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="2a5ca-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="2a5ca-227">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2a5ca-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="2a5ca-228">[SignalRnegociação entre origens para autenticação](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="2a5ca-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
