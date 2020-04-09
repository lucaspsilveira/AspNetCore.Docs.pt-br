---
title: Use ASP.NET SignalR Blazor Core com WebAssembly
author: guardrex
description: Crie um aplicativo de SignalR bate-papo que use ASP.NET Core com Blazor webAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306568"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="0407c-103">Use ASP.NET Core SignalR com Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0407c-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="0407c-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0407c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0407c-105">Este tutorial ensina o básico da construção de um aplicativo em tempo real usando signalR com Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="0407c-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="0407c-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="0407c-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0407c-107">Crie um projeto de aplicativo hospedado no WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="0407c-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0407c-108">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="0407c-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="0407c-109">Adicione um hub SignalR</span><span class="sxs-lookup"><span data-stu-id="0407c-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="0407c-110">Adicionar serviços SignalR e um ponto final para o hub SignalR</span><span class="sxs-lookup"><span data-stu-id="0407c-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0407c-111">Adicionar código de componente razor para bate-papo</span><span class="sxs-lookup"><span data-stu-id="0407c-111">Add Razor component code for chat</span></span>

<span data-ttu-id="0407c-112">No final deste tutorial, você terá um aplicativo de bate-papo funcionando.</span><span class="sxs-lookup"><span data-stu-id="0407c-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="0407c-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0407c-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0407c-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0407c-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0407c-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0407c-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0407c-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0407c-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0407c-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0407c-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0407c-118">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0407c-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="0407c-119">Crie um projeto de aplicativo Blazor WebAssembly hospedado</span><span class="sxs-lookup"><span data-stu-id="0407c-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="0407c-120">Ao não usar a versão 16.6 Preview 2 ou posterior do Visual Studio, instale o modelo [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="0407c-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="0407c-121">O pacote [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) tem uma versão de pré-visualização enquanto o Blazor WebAssembly está em pré-visualização.</span><span class="sxs-lookup"><span data-stu-id="0407c-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="0407c-122">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0407c-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

<span data-ttu-id="0407c-123">Siga as orientações para a sua escolha de ferramenta:</span><span class="sxs-lookup"><span data-stu-id="0407c-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0407c-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0407c-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0407c-125">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="0407c-125">Create a new project.</span></span>

1. <span data-ttu-id="0407c-126">Selecione **o aplicativo Blazor** e selecione **Next**.</span><span class="sxs-lookup"><span data-stu-id="0407c-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="0407c-127">Digite "BlazorSignalRApp" no campo Nome do **Projeto.**</span><span class="sxs-lookup"><span data-stu-id="0407c-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="0407c-128">Confirme se a entrada **do local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="0407c-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0407c-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0407c-129">Select **Create**.</span></span>

1. <span data-ttu-id="0407c-130">Escolha o modelo **do aplicativo Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="0407c-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="0407c-131">Em **Avançado,** selecione a caixa de seleção **hosted ASP.NET Core.**</span><span class="sxs-lookup"><span data-stu-id="0407c-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="0407c-132">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0407c-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="0407c-133">Se você atualizou ou instalou uma nova versão do Visual Studio e o modelo Blazor WebAssembly `dotnet new` não aparecerá na UI VS, reinstale o modelo usando o comando mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="0407c-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0407c-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0407c-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0407c-135">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0407c-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0407c-136">No Visual Studio Code, abra a pasta de projetos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0407c-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="0407c-137">Quando a caixa de diálogo aparecer para adicionar ativos para criar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="0407c-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="0407c-138">O Visual Studio Code adiciona automaticamente a pasta *.vscode* com arquivos *launch.json* e *tasks.json gerados.*</span><span class="sxs-lookup"><span data-stu-id="0407c-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0407c-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0407c-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0407c-140">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0407c-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0407c-141">No Visual Studio for Mac, abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto *( .sln*).</span><span class="sxs-lookup"><span data-stu-id="0407c-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0407c-142">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0407c-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0407c-143">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0407c-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="0407c-144">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="0407c-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0407c-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0407c-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="0407c-146">No **Solution Explorer,** clique com o botão direito do mouse no projeto **BlazorSignalRApp.Client** e **selecione Gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0407c-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0407c-147">Na caixa de diálogo **Gerenciar pacotes NuGet,** confirme se a fonte do **pacote** está definida para *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="0407c-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="0407c-148">Com **procurar** selecionado, digite "Microsoft.AspNetCore.SignalR.Client" na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="0407c-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="0407c-149">Nos resultados da pesquisa, selecione o `Microsoft.AspNetCore.SignalR.Client` pacote e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="0407c-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="0407c-150">Se a **caixa de diálogo 'Inversaver'' 'Inverter''** aparecer, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="0407c-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="0407c-151">Se a caixa de diálogo **Aceitação** de Licença for exibida, selecione **Aceito Se** você concordar com os termos da licença.</span><span class="sxs-lookup"><span data-stu-id="0407c-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0407c-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0407c-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="0407c-153">No **Terminal Integrado** (**Exibir** > **Terminal** a partir da barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0407c-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0407c-154">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0407c-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0407c-155">Na barra lateral **solução,** clique com o botão direito do mouse no projeto **BlazorSignalRApp.Client** e **selecione Gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0407c-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0407c-156">Na caixa de diálogo **Gerenciar pacotes NuGet,** confirme se a lista de informações está definida para *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="0407c-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="0407c-157">Com **procurar** selecionado, digite "Microsoft.AspNetCore.SignalR.Client" na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="0407c-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="0407c-158">Nos resultados da pesquisa, selecione `Microsoft.AspNetCore.SignalR.Client` a caixa de seleção ao lado do pacote e **selecione Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="0407c-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="0407c-159">Se a caixa de diálogo **Aceitação** de Licença for exibida, **selecione Aceitar** se você concordar com os termos da licença.</span><span class="sxs-lookup"><span data-stu-id="0407c-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0407c-160">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0407c-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0407c-161">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0407c-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="0407c-162">Adicione um hub SignalR</span><span class="sxs-lookup"><span data-stu-id="0407c-162">Add a SignalR hub</span></span>

<span data-ttu-id="0407c-163">No projeto **BlazorSignalRApp.Server,** crie uma pasta *Hubs* (plural) e adicione a seguinte `ChatHub` classe *(Hubs/ChatHub.cs*):</span><span class="sxs-lookup"><span data-stu-id="0407c-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="0407c-164">Adicionar serviços SignalR e um ponto final para o hub SignalR</span><span class="sxs-lookup"><span data-stu-id="0407c-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="0407c-165">No projeto **BlazorSignalRApp.Server,** abra o arquivo *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="0407c-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="0407c-166">Adicione o namespace `ChatHub` para a classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="0407c-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="0407c-167">Adicione os serviços `Startup.ConfigureServices`SignalR a:</span><span class="sxs-lookup"><span data-stu-id="0407c-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="0407c-168">Entre `Startup.Configure` os pontos finais da rota do controlador padrão e o recuo do lado do cliente, adicione um ponto final para o hub:</span><span class="sxs-lookup"><span data-stu-id="0407c-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="0407c-169">Adicionar código de componente razor para bate-papo</span><span class="sxs-lookup"><span data-stu-id="0407c-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="0407c-170">No projeto **BlazorSignalRApp.Client,** abra o arquivo *Páginas/Index.razor.*</span><span class="sxs-lookup"><span data-stu-id="0407c-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="0407c-171">Substitua a marcação pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0407c-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="0407c-172">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0407c-172">Run the app</span></span>

1. <span data-ttu-id="0407c-173">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="0407c-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0407c-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0407c-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0407c-175">No **Solution Explorer,** selecione o projeto **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="0407c-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="0407c-176">Pressione **Ctrl+F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="0407c-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="0407c-177">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="0407c-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0407c-178">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0407c-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0407c-179">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="0407c-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0407c-181">Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0407c-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0407c-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0407c-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0407c-183">Selecione **Depurar** > **Executar Sem Depuração** na barra de ferramentas.</span><span class="sxs-lookup"><span data-stu-id="0407c-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="0407c-184">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="0407c-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0407c-185">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0407c-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0407c-186">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="0407c-186">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0407c-188">Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0407c-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0407c-189">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0407c-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0407c-190">Na barra lateral **solução,** selecione o projeto **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="0407c-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="0407c-191">No menu, **selecione Executar iniciar** > **sem depuração**.</span><span class="sxs-lookup"><span data-stu-id="0407c-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="0407c-192">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="0407c-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0407c-193">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0407c-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0407c-194">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="0407c-194">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0407c-196">Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0407c-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0407c-197">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0407c-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="0407c-198">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0407c-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="0407c-199">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="0407c-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0407c-200">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0407c-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0407c-201">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="0407c-201">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0407c-203">Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0407c-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0407c-204">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0407c-204">Next steps</span></span>

<span data-ttu-id="0407c-205">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="0407c-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0407c-206">Crie Blazor um projeto de aplicativo hospedado no WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0407c-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0407c-207">Adicione SignalR a biblioteca do cliente</span><span class="sxs-lookup"><span data-stu-id="0407c-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="0407c-208">Adicionar SignalR um hub</span><span class="sxs-lookup"><span data-stu-id="0407c-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="0407c-209">Adicionar SignalR serviços e um SignalR ponto final para o hub</span><span class="sxs-lookup"><span data-stu-id="0407c-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0407c-210">Adicionar código de componente razor para bate-papo</span><span class="sxs-lookup"><span data-stu-id="0407c-210">Add Razor component code for chat</span></span>

<span data-ttu-id="0407c-211">Para saber mais Blazor sobre a Blazor construção de aplicativos, consulte a documentação:</span><span class="sxs-lookup"><span data-stu-id="0407c-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="0407c-212">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0407c-212">Additional resources</span></span>

* <xref:signalr/introduction>
