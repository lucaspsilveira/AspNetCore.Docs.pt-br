---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-web-api
ms.openlocfilehash: 79f36168d0430ceee3794cfb5a4e29f3671ac73f
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86212627"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="35cb2-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35cb2-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="35cb2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="35cb2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="35cb2-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="35cb2-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="35cb2-106">Neste tutorial, você aprende a:</span><span class="sxs-lookup"><span data-stu-id="35cb2-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="35cb2-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-107">Create a web API project.</span></span>
> * <span data-ttu-id="35cb2-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="35cb2-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="35cb2-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="35cb2-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="35cb2-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="35cb2-111">Call the web API with Postman.</span></span>

<span data-ttu-id="35cb2-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="35cb2-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="35cb2-113">Overview</span></span>

<span data-ttu-id="35cb2-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="35cb2-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="35cb2-115">API</span><span class="sxs-lookup"><span data-stu-id="35cb2-115">API</span></span> | <span data-ttu-id="35cb2-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="35cb2-116">Description</span></span> | <span data-ttu-id="35cb2-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="35cb2-117">Request body</span></span> | <span data-ttu-id="35cb2-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="35cb2-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="35cb2-119">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-119">Get all to-do items</span></span> | <span data-ttu-id="35cb2-120">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-120">None</span></span> | <span data-ttu-id="35cb2-121">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="35cb2-122">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="35cb2-122">Get an item by ID</span></span> | <span data-ttu-id="35cb2-123">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-123">None</span></span> | <span data-ttu-id="35cb2-124">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="35cb2-125">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="35cb2-125">Add a new item</span></span> | <span data-ttu-id="35cb2-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-126">To-do item</span></span> | <span data-ttu-id="35cb2-127">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="35cb2-128">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="35cb2-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="35cb2-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-129">To-do item</span></span> | <span data-ttu-id="35cb2-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-130">None</span></span> |
|<span data-ttu-id="35cb2-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="35cb2-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="35cb2-132">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="35cb2-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="35cb2-133">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-133">None</span></span> | <span data-ttu-id="35cb2-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-134">None</span></span>|

<span data-ttu-id="35cb2-135">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-135">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="35cb2-141">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="35cb2-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="35cb2-145">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="35cb2-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-147">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="35cb2-148">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="35cb2-149">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="35cb2-150">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="35cb2-151">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-151">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="35cb2-154">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="35cb2-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="35cb2-155">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="35cb2-156">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="35cb2-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="35cb2-157">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="35cb2-158">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="35cb2-158">The preceding commands:</span></span>

  * <span data-ttu-id="35cb2-159">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="35cb2-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="35cb2-160">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="35cb2-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="35cb2-162">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-162">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="35cb2-164">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="35cb2-165">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="35cb2-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="35cb2-167">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="35cb2-168">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-168">Select **Next**.</span></span>

* <span data-ttu-id="35cb2-169">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="35cb2-171">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="35cb2-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="35cb2-172">Testar a API</span><span class="sxs-lookup"><span data-stu-id="35cb2-172">Test the API</span></span>

<span data-ttu-id="35cb2-173">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="35cb2-174">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="35cb2-176">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="35cb2-177">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="35cb2-178">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="35cb2-179">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="35cb2-181">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="35cb2-182">Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-183">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="35cb2-184">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="35cb2-185">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="35cb2-186">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="35cb2-187">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="35cb2-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="35cb2-188">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="35cb2-188">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="35cb2-189">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="35cb2-189">Add a model class</span></span>

<span data-ttu-id="35cb2-190">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="35cb2-191">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-193">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="35cb2-194">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="35cb2-195">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="35cb2-196">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="35cb2-197">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="35cb2-198">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="35cb2-200">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="35cb2-201">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="35cb2-203">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-203">Right-click the project.</span></span> <span data-ttu-id="35cb2-204">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="35cb2-205">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-205">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="35cb2-207">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="35cb2-208">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="35cb2-209">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="35cb2-210">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="35cb2-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="35cb2-211">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="35cb2-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="35cb2-212">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="35cb2-212">Add a database context</span></span>

<span data-ttu-id="35cb2-213">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="35cb2-214">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="35cb2-216">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="35cb2-216">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="35cb2-217">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="35cb2-218">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="35cb2-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="35cb2-219">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="35cb2-220">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="35cb2-221">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="35cb2-223">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="35cb2-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="35cb2-224">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="35cb2-225">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-226">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="35cb2-227">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="35cb2-228">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="35cb2-229">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="35cb2-229">Register the database context</span></span>

<span data-ttu-id="35cb2-230">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="35cb2-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="35cb2-231">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="35cb2-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="35cb2-232">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="35cb2-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="35cb2-233">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="35cb2-233">The preceding code:</span></span>

* <span data-ttu-id="35cb2-234">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="35cb2-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="35cb2-235">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="35cb2-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="35cb2-236">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="35cb2-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="35cb2-237">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="35cb2-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-239">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="35cb2-240">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="35cb2-241">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="35cb2-242">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="35cb2-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="35cb2-243">Selecione **TodoItem (TodoApi. Models)** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="35cb2-244">Selecione **TodoContext (TodoApi. Models)** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="35cb2-245">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-246">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="35cb2-247">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="35cb2-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="35cb2-248">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="35cb2-248">The preceding commands:</span></span>

* <span data-ttu-id="35cb2-249">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="35cb2-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="35cb2-250">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="35cb2-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="35cb2-251">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="35cb2-252">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="35cb2-252">The generated code:</span></span>

* <span data-ttu-id="35cb2-253">Marca a classe com o [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="35cb2-254">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="35cb2-255">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="35cb2-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="35cb2-256">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="35cb2-257">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="35cb2-258">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="35cb2-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="35cb2-259">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="35cb2-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="35cb2-260">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="35cb2-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="35cb2-261">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="35cb2-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="35cb2-262">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="35cb2-263">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="35cb2-264">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="35cb2-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="35cb2-265">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="35cb2-266">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="35cb2-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="35cb2-267">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="35cb2-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="35cb2-268">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="35cb2-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="35cb2-269">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="35cb2-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="35cb2-270">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="35cb2-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="35cb2-271">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="35cb2-272">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="35cb2-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="35cb2-273">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="35cb2-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="35cb2-274">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="35cb2-275">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="35cb2-275">Install Postman</span></span>

<span data-ttu-id="35cb2-276">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="35cb2-277">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="35cb2-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="35cb2-278">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-278">Start the web app.</span></span>
* <span data-ttu-id="35cb2-279">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="35cb2-279">Start Postman.</span></span>
* <span data-ttu-id="35cb2-280">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="35cb2-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="35cb2-281">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="35cb2-282">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="35cb2-283">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="35cb2-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="35cb2-284">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="35cb2-284">Create a new request.</span></span>
* <span data-ttu-id="35cb2-285">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="35cb2-286">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="35cb2-287">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="35cb2-288">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="35cb2-289">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="35cb2-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="35cb2-290">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-290">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="35cb2-292">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="35cb2-292">Test the location header URI</span></span>

* <span data-ttu-id="35cb2-293">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="35cb2-294">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="35cb2-294">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="35cb2-296">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="35cb2-296">Set the method to GET.</span></span>
* <span data-ttu-id="35cb2-297">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="35cb2-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="35cb2-298">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="35cb2-299">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="35cb2-299">Examine the GET methods</span></span>

<span data-ttu-id="35cb2-300">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="35cb2-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="35cb2-301">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="35cb2-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="35cb2-302">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="35cb2-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="35cb2-303">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="35cb2-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="35cb2-304">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="35cb2-304">Test Get with Postman</span></span>

* <span data-ttu-id="35cb2-305">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="35cb2-305">Create a new request.</span></span>
* <span data-ttu-id="35cb2-306">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="35cb2-307">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="35cb2-308">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="35cb2-309">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="35cb2-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="35cb2-310">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-310">Select **Send**.</span></span>

<span data-ttu-id="35cb2-311">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="35cb2-311">This app uses an in-memory database.</span></span> <span data-ttu-id="35cb2-312">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="35cb2-313">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="35cb2-314">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="35cb2-314">Routing and URL paths</span></span>

<span data-ttu-id="35cb2-315">O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="35cb2-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="35cb2-316">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="35cb2-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="35cb2-317">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="35cb2-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="35cb2-318">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="35cb2-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="35cb2-319">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="35cb2-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="35cb2-320">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="35cb2-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="35cb2-321">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="35cb2-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="35cb2-322">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-322">This sample doesn't use a template.</span></span> <span data-ttu-id="35cb2-323">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="35cb2-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="35cb2-324">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="35cb2-325">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="35cb2-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="35cb2-326">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="35cb2-326">Return values</span></span>

<span data-ttu-id="35cb2-327">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="35cb2-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="35cb2-328">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="35cb2-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="35cb2-329">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="35cb2-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="35cb2-330">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="35cb2-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="35cb2-331">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="35cb2-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="35cb2-332">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="35cb2-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="35cb2-333">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="35cb2-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="35cb2-334">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="35cb2-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="35cb2-335">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="35cb2-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="35cb2-336">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-336">The PutTodoItem method</span></span>

<span data-ttu-id="35cb2-337">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="35cb2-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="35cb2-338">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="35cb2-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="35cb2-339">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="35cb2-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="35cb2-340">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="35cb2-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="35cb2-341">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="35cb2-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="35cb2-342">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="35cb2-343">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="35cb2-344">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="35cb2-345">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="35cb2-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="35cb2-346">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="35cb2-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="35cb2-347">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="35cb2-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="35cb2-348">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="35cb2-348">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="35cb2-350">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="35cb2-351">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="35cb2-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="35cb2-352">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="35cb2-353">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="35cb2-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="35cb2-354">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="35cb2-355">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="35cb2-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="35cb2-356">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="35cb2-357">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="35cb2-357">Prevent over-posting</span></span>

<span data-ttu-id="35cb2-358">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="35cb2-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="35cb2-359">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="35cb2-360">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="35cb2-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="35cb2-361">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="35cb2-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="35cb2-362">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="35cb2-363">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="35cb2-363">A DTO may be used to:</span></span>

* <span data-ttu-id="35cb2-364">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="35cb2-364">Prevent over-posting.</span></span>
* <span data-ttu-id="35cb2-365">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="35cb2-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="35cb2-366">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="35cb2-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="35cb2-367">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="35cb2-368">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="35cb2-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="35cb2-369">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="35cb2-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="35cb2-370">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="35cb2-371">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="35cb2-372">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="35cb2-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="35cb2-373">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="35cb2-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="35cb2-374">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="35cb2-375">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="35cb2-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="35cb2-376">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="35cb2-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="35cb2-377">Neste tutorial, você aprende a:</span><span class="sxs-lookup"><span data-stu-id="35cb2-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="35cb2-378">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-378">Create a web API project.</span></span>
> * <span data-ttu-id="35cb2-379">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="35cb2-380">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-380">Add a controller.</span></span>
> * <span data-ttu-id="35cb2-381">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="35cb2-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="35cb2-382">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="35cb2-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="35cb2-383">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-383">Specify return values.</span></span>
> * <span data-ttu-id="35cb2-384">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="35cb2-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="35cb2-385">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35cb2-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="35cb2-386">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="35cb2-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="35cb2-387">Visão geral</span><span class="sxs-lookup"><span data-stu-id="35cb2-387">Overview</span></span>

<span data-ttu-id="35cb2-388">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="35cb2-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="35cb2-389">API</span><span class="sxs-lookup"><span data-stu-id="35cb2-389">API</span></span> | <span data-ttu-id="35cb2-390">Descrição</span><span class="sxs-lookup"><span data-stu-id="35cb2-390">Description</span></span> | <span data-ttu-id="35cb2-391">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="35cb2-391">Request body</span></span> | <span data-ttu-id="35cb2-392">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="35cb2-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="35cb2-393">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="35cb2-393">GET /api/TodoItems</span></span> | <span data-ttu-id="35cb2-394">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-394">Get all to-do items</span></span> | <span data-ttu-id="35cb2-395">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-395">None</span></span> | <span data-ttu-id="35cb2-396">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-396">Array of to-do items</span></span>|
|<span data-ttu-id="35cb2-397">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="35cb2-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="35cb2-398">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="35cb2-398">Get an item by ID</span></span> | <span data-ttu-id="35cb2-399">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-399">None</span></span> | <span data-ttu-id="35cb2-400">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-400">To-do item</span></span>|
|<span data-ttu-id="35cb2-401">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="35cb2-401">POST /api/TodoItems</span></span> | <span data-ttu-id="35cb2-402">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="35cb2-402">Add a new item</span></span> | <span data-ttu-id="35cb2-403">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-403">To-do item</span></span> | <span data-ttu-id="35cb2-404">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-404">To-do item</span></span> |
|<span data-ttu-id="35cb2-405">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="35cb2-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="35cb2-406">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="35cb2-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="35cb2-407">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-407">To-do item</span></span> | <span data-ttu-id="35cb2-408">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-408">None</span></span> |
|<span data-ttu-id="35cb2-409">EXCLUIR/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="35cb2-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="35cb2-410">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="35cb2-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="35cb2-411">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-411">None</span></span> | <span data-ttu-id="35cb2-412">Nenhum</span><span class="sxs-lookup"><span data-stu-id="35cb2-412">None</span></span>|

<span data-ttu-id="35cb2-413">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-413">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="35cb2-419">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="35cb2-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-422">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="35cb2-423">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="35cb2-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-425">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="35cb2-426">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="35cb2-427">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="35cb2-428">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="35cb2-429">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="35cb2-430">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-430">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="35cb2-433">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="35cb2-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="35cb2-434">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="35cb2-435">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="35cb2-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="35cb2-436">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="35cb2-437">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-438">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="35cb2-439">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-439">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="35cb2-441">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="35cb2-442">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="35cb2-443">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="35cb2-444">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-444">Select **Next**.</span></span>

* <span data-ttu-id="35cb2-445">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="35cb2-447">Testar a API</span><span class="sxs-lookup"><span data-stu-id="35cb2-447">Test the API</span></span>

<span data-ttu-id="35cb2-448">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-448">The project template creates a `values` API.</span></span> <span data-ttu-id="35cb2-449">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="35cb2-451">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="35cb2-452">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="35cb2-453">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="35cb2-454">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="35cb2-456">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="35cb2-457">Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-458">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="35cb2-459">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="35cb2-460">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="35cb2-461">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="35cb2-462">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="35cb2-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="35cb2-463">O JSON a seguir será retornado:</span><span class="sxs-lookup"><span data-stu-id="35cb2-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="35cb2-464">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="35cb2-464">Add a model class</span></span>

<span data-ttu-id="35cb2-465">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="35cb2-466">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-468">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="35cb2-469">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="35cb2-470">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="35cb2-471">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="35cb2-472">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="35cb2-473">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="35cb2-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="35cb2-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="35cb2-475">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="35cb2-476">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-477">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="35cb2-478">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-478">Right-click the project.</span></span> <span data-ttu-id="35cb2-479">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="35cb2-480">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-480">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="35cb2-482">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="35cb2-483">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="35cb2-484">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="35cb2-485">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="35cb2-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="35cb2-486">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="35cb2-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="35cb2-487">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="35cb2-487">Add a database context</span></span>

<span data-ttu-id="35cb2-488">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="35cb2-489">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-491">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="35cb2-492">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-493">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="35cb2-494">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="35cb2-495">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="35cb2-496">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="35cb2-496">Register the database context</span></span>

<span data-ttu-id="35cb2-497">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="35cb2-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="35cb2-498">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="35cb2-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="35cb2-499">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="35cb2-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="35cb2-500">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="35cb2-500">The preceding code:</span></span>

* <span data-ttu-id="35cb2-501">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="35cb2-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="35cb2-502">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="35cb2-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="35cb2-503">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="35cb2-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="35cb2-504">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="35cb2-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-506">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="35cb2-507">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="35cb2-508">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="35cb2-509">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-511">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="35cb2-512">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="35cb2-513">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="35cb2-514">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="35cb2-514">The preceding code:</span></span>

* <span data-ttu-id="35cb2-515">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="35cb2-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="35cb2-516">Marca a classe com o [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="35cb2-517">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="35cb2-518">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="35cb2-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="35cb2-519">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="35cb2-520">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="35cb2-521">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="35cb2-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="35cb2-522">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="35cb2-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="35cb2-523">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="35cb2-524">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="35cb2-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="35cb2-525">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="35cb2-525">Add Get methods</span></span>

<span data-ttu-id="35cb2-526">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="35cb2-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="35cb2-527">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="35cb2-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="35cb2-528">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="35cb2-528">Stop the app if it's still running.</span></span> <span data-ttu-id="35cb2-529">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="35cb2-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="35cb2-530">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="35cb2-531">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="35cb2-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="35cb2-532">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="35cb2-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="35cb2-533">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="35cb2-533">Routing and URL paths</span></span>

<span data-ttu-id="35cb2-534">O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="35cb2-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="35cb2-535">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="35cb2-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="35cb2-536">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="35cb2-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="35cb2-537">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="35cb2-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="35cb2-538">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="35cb2-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="35cb2-539">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="35cb2-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="35cb2-540">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="35cb2-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="35cb2-541">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-541">This sample doesn't use a template.</span></span> <span data-ttu-id="35cb2-542">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="35cb2-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="35cb2-543">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="35cb2-544">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="35cb2-545">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="35cb2-545">Return values</span></span>

<span data-ttu-id="35cb2-546">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="35cb2-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="35cb2-547">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="35cb2-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="35cb2-548">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="35cb2-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="35cb2-549">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="35cb2-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="35cb2-550">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="35cb2-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="35cb2-551">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="35cb2-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="35cb2-552">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="35cb2-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="35cb2-553">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="35cb2-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="35cb2-554">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="35cb2-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="35cb2-555">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="35cb2-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="35cb2-556">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="35cb2-557">Instalar o [postmaster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="35cb2-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="35cb2-558">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-558">Start the web app.</span></span>
* <span data-ttu-id="35cb2-559">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="35cb2-559">Start Postman.</span></span>
* <span data-ttu-id="35cb2-560">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35cb2-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35cb2-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="35cb2-562">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="35cb2-563">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="35cb2-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="35cb2-564">De **Postman**  >  **preferências** do postmaster (guia**geral** ), desabilite a **verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="35cb2-565">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="35cb2-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="35cb2-566">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="35cb2-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="35cb2-567">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="35cb2-567">Create a new request.</span></span>
  * <span data-ttu-id="35cb2-568">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="35cb2-569">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="35cb2-570">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="35cb2-571">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="35cb2-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="35cb2-572">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-572">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="35cb2-574">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="35cb2-574">Add a Create method</span></span>

<span data-ttu-id="35cb2-575">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="35cb2-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="35cb2-576">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="35cb2-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="35cb2-577">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="35cb2-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="35cb2-578">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="35cb2-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="35cb2-579">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="35cb2-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="35cb2-580">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="35cb2-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="35cb2-581">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="35cb2-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="35cb2-582">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="35cb2-583">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="35cb2-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="35cb2-584">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="35cb2-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="35cb2-585">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="35cb2-586">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="35cb2-587">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-587">Build the project.</span></span>
* <span data-ttu-id="35cb2-588">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="35cb2-589">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="35cb2-590">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="35cb2-591">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="35cb2-592">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="35cb2-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="35cb2-593">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-593">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="35cb2-595">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="35cb2-596">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="35cb2-596">Test the location header URI</span></span>

* <span data-ttu-id="35cb2-597">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="35cb2-598">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="35cb2-598">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="35cb2-600">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="35cb2-600">Set the method to GET.</span></span>
* <span data-ttu-id="35cb2-601">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="35cb2-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="35cb2-602">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="35cb2-603">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="35cb2-604">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="35cb2-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="35cb2-605">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="35cb2-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="35cb2-606">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="35cb2-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="35cb2-607">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="35cb2-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="35cb2-608">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="35cb2-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="35cb2-609">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb2-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="35cb2-610">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="35cb2-611">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="35cb2-612">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="35cb2-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="35cb2-613">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="35cb2-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="35cb2-614">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="35cb2-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="35cb2-615">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="35cb2-615">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="35cb2-617">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="35cb2-618">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="35cb2-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="35cb2-619">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="35cb2-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="35cb2-620">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="35cb2-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="35cb2-621">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="35cb2-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="35cb2-622">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="35cb2-623">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="35cb2-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="35cb2-624">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="35cb2-624">Select **Send**.</span></span>

<span data-ttu-id="35cb2-625">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="35cb2-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="35cb2-626">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="35cb2-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="35cb2-627">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="35cb2-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="35cb2-628">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="35cb2-629">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="35cb2-629">jQuery initiates the request.</span></span> <span data-ttu-id="35cb2-630">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="35cb2-631">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="35cb2-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="35cb2-632">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="35cb2-633">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="35cb2-634">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="35cb2-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="35cb2-635">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="35cb2-636">Substitua o conteúdo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="35cb2-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="35cb2-637">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="35cb2-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="35cb2-638">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="35cb2-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="35cb2-639">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index.html* &mdash; o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="35cb2-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="35cb2-640">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="35cb2-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="35cb2-641">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="35cb2-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="35cb2-642">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="35cb2-642">Get a list of to-do items</span></span>

<span data-ttu-id="35cb2-643">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="35cb2-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="35cb2-644">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="35cb2-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="35cb2-645">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="35cb2-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="35cb2-646">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="35cb2-646">Add a to-do item</span></span>

<span data-ttu-id="35cb2-647">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="35cb2-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="35cb2-648">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="35cb2-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="35cb2-649">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="35cb2-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="35cb2-650">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="35cb2-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="35cb2-651">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="35cb2-651">Update a to-do item</span></span>

<span data-ttu-id="35cb2-652">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="35cb2-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="35cb2-653">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="35cb2-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="35cb2-654">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="35cb2-654">Delete a to-do item</span></span>

<span data-ttu-id="35cb2-655">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="35cb2-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="35cb2-656">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="35cb2-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources"></a><span data-ttu-id="35cb2-657">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="35cb2-657">Additional resources</span></span>

<span data-ttu-id="35cb2-658">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="35cb2-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="35cb2-659">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="35cb2-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="35cb2-660">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="35cb2-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="35cb2-661">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="35cb2-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
