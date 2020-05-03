---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 4e205c737f606579590854b679e669cbdd0cd5ab
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727791"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4aab4-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4aab4-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4aab4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4aab4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4aab4-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4aab4-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4aab4-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="4aab4-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4aab4-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-107">Create a web API project.</span></span>
> * <span data-ttu-id="4aab4-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4aab4-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="4aab4-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4aab4-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4aab4-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="4aab4-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4aab4-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4aab4-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="4aab4-113">Overview</span></span>

<span data-ttu-id="4aab4-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="4aab4-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4aab4-115">API</span><span class="sxs-lookup"><span data-stu-id="4aab4-115">API</span></span> | <span data-ttu-id="4aab4-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="4aab4-116">Description</span></span> | <span data-ttu-id="4aab4-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="4aab4-117">Request body</span></span> | <span data-ttu-id="4aab4-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="4aab4-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="4aab4-119">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-119">Get all to-do items</span></span> | <span data-ttu-id="4aab4-120">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-120">None</span></span> | <span data-ttu-id="4aab4-121">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="4aab4-122">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="4aab4-122">Get an item by ID</span></span> | <span data-ttu-id="4aab4-123">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-123">None</span></span> | <span data-ttu-id="4aab4-124">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="4aab4-125">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="4aab4-125">Add a new item</span></span> | <span data-ttu-id="4aab4-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-126">To-do item</span></span> | <span data-ttu-id="4aab4-127">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="4aab4-128">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="4aab4-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4aab4-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-129">To-do item</span></span> | <span data-ttu-id="4aab4-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-130">None</span></span> |
|<span data-ttu-id="4aab4-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4aab4-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="4aab4-132">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="4aab4-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4aab4-133">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-133">None</span></span> | <span data-ttu-id="4aab4-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-134">None</span></span>|

<span data-ttu-id="4aab4-135">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-135">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4aab4-141">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4aab4-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4aab4-145">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="4aab4-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-147">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4aab4-148">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4aab4-149">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4aab4-150">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="4aab4-151">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-151">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4aab4-154">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4aab4-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4aab4-155">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4aab4-156">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="4aab4-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="4aab4-157">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4aab4-158">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="4aab4-158">The preceding commands:</span></span>

  * <span data-ttu-id="4aab4-159">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4aab4-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4aab4-160">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="4aab4-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4aab4-162">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-162">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4aab4-164">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4aab4-166">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , selecione **estrutura de destino** do \**.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="4aab4-167">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="4aab4-169">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="4aab4-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="4aab4-170">Testar a API</span><span class="sxs-lookup"><span data-stu-id="4aab4-170">Test the API</span></span>

<span data-ttu-id="4aab4-171">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4aab4-172">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4aab4-174">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4aab4-175">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4aab4-176">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4aab4-177">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4aab4-179">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4aab4-180">Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-181">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4aab4-182">Selecione **executar** > **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4aab4-183">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4aab4-184">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4aab4-185">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="4aab4-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4aab4-186">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="4aab4-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="4aab4-187">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="4aab4-187">Add a model class</span></span>

<span data-ttu-id="4aab4-188">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4aab4-189">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-191">No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4aab4-192">Selecione **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4aab4-193">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="4aab4-194">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4aab4-195">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4aab4-196">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4aab4-198">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4aab4-199">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-200">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4aab4-201">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-201">Right-click the project.</span></span> <span data-ttu-id="4aab4-202">Selecione **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4aab4-203">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-203">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4aab4-205">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4aab4-206">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4aab4-207">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="4aab4-208">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="4aab4-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4aab4-209">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="4aab4-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4aab4-210">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="4aab4-210">Add a database context</span></span>

<span data-ttu-id="4aab4-211">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4aab4-212">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="4aab4-214">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="4aab4-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="4aab4-215">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4aab4-216">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="4aab4-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="4aab4-217">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="4aab4-218">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="4aab4-219">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4aab4-221">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="4aab4-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="4aab4-222">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4aab4-223">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-224">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4aab4-225">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4aab4-226">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4aab4-227">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="4aab4-227">Register the database context</span></span>

<span data-ttu-id="4aab4-228">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4aab4-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4aab4-229">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="4aab4-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="4aab4-230">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="4aab4-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4aab4-231">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="4aab4-231">The preceding code:</span></span>

* <span data-ttu-id="4aab4-232">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="4aab4-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4aab4-233">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="4aab4-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4aab4-234">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="4aab4-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4aab4-235">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="4aab4-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-237">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4aab4-238">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4aab4-239">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4aab4-240">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="4aab4-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4aab4-241">Selecione **TodoItem (TodoApi. Models)** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4aab4-242">Selecione **TodoContext (TodoApi. Models)** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4aab4-243">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-244">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4aab4-245">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="4aab4-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="4aab4-246">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="4aab4-246">The preceding commands:</span></span>

* <span data-ttu-id="4aab4-247">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="4aab4-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4aab4-248">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="4aab4-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4aab4-249">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4aab4-250">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="4aab4-250">The generated code:</span></span>

* <span data-ttu-id="4aab4-251">Marca a classe com o [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4aab4-252">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4aab4-253">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4aab4-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4aab4-254">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4aab4-255">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="4aab4-256">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="4aab4-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="4aab4-257">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="4aab4-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="4aab4-258">Os controladores de API `[action]` não incluem no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="4aab4-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="4aab4-259">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="4aab4-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="4aab4-260">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4aab4-261">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4aab4-262">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="4aab4-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4aab4-263">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4aab4-264">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="4aab4-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4aab4-265">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="4aab4-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4aab4-266">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="4aab4-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4aab4-267">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="4aab4-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4aab4-268">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="4aab4-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4aab4-269">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4aab4-270">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4aab4-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4aab4-271">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="4aab4-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4aab4-272">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4aab4-273">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="4aab4-273">Install Postman</span></span>

<span data-ttu-id="4aab4-274">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4aab4-275">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="4aab4-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4aab4-276">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-276">Start the web app.</span></span>
* <span data-ttu-id="4aab4-277">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="4aab4-277">Start Postman.</span></span>
* <span data-ttu-id="4aab4-278">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="4aab4-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="4aab4-279">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4aab4-280">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4aab4-281">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="4aab4-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4aab4-282">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="4aab4-282">Create a new request.</span></span>
* <span data-ttu-id="4aab4-283">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4aab4-284">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="4aab4-285">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4aab4-286">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4aab4-287">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="4aab4-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4aab4-288">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-288">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4aab4-290">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="4aab4-290">Test the location header URI</span></span>

* <span data-ttu-id="4aab4-291">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4aab4-292">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="4aab4-292">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="4aab4-294">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="4aab4-294">Set the method to GET.</span></span>
* <span data-ttu-id="4aab4-295">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="4aab4-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4aab4-296">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4aab4-297">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="4aab4-297">Examine the GET methods</span></span>

<span data-ttu-id="4aab4-298">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="4aab4-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4aab4-299">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="4aab4-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4aab4-300">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="4aab4-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="4aab4-301">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="4aab4-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4aab4-302">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="4aab4-302">Test Get with Postman</span></span>

* <span data-ttu-id="4aab4-303">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="4aab4-303">Create a new request.</span></span>
* <span data-ttu-id="4aab4-304">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4aab4-305">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4aab4-306">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4aab4-307">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="4aab4-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4aab4-308">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-308">Select **Send**.</span></span>

<span data-ttu-id="4aab4-309">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="4aab4-309">This app uses an in-memory database.</span></span> <span data-ttu-id="4aab4-310">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4aab4-311">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4aab4-312">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="4aab4-312">Routing and URL paths</span></span>

<span data-ttu-id="4aab4-313">O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="4aab4-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4aab4-314">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="4aab4-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4aab4-315">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="4aab4-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4aab4-316">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="4aab4-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4aab4-317">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="4aab4-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4aab4-318">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4aab4-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4aab4-319">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="4aab4-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4aab4-320">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-320">This sample doesn't use a template.</span></span> <span data-ttu-id="4aab4-321">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4aab4-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4aab4-322">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4aab4-323">Quando `GetTodoItem` é invocado, o valor `"{id}"` de na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="4aab4-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4aab4-324">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="4aab4-324">Return values</span></span>

<span data-ttu-id="4aab4-325">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4aab4-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4aab4-326">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="4aab4-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4aab4-327">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="4aab4-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4aab4-328">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="4aab4-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4aab4-329">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="4aab4-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4aab4-330">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="4aab4-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4aab4-331">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="4aab4-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4aab4-332">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="4aab4-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4aab4-333">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4aab4-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4aab4-334">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-334">The PutTodoItem method</span></span>

<span data-ttu-id="4aab4-335">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4aab4-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4aab4-336">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4aab4-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4aab4-337">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4aab4-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4aab4-338">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="4aab4-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4aab4-339">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="4aab4-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4aab4-340">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4aab4-341">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="4aab4-342">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4aab4-343">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4aab4-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4aab4-344">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4aab4-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4aab4-345">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="4aab4-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4aab4-346">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="4aab4-346">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4aab4-348">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="4aab4-349">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4aab4-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4aab4-350">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4aab4-351">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="4aab4-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4aab4-352">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4aab4-353">Defina o URI do objeto a ser excluído (por exemplo `https://localhost:5001/api/TodoItems/1`,).</span><span class="sxs-lookup"><span data-stu-id="4aab4-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4aab4-354">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="4aab4-355">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="4aab4-355">Prevent over-posting</span></span>

<span data-ttu-id="4aab4-356">Atualmente, o aplicativo de exemplo expõe `TodoItem` o objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="4aab4-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="4aab4-357">Os aplicativos de produções normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="4aab4-358">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="4aab4-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="4aab4-359">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="4aab4-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="4aab4-360">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="4aab4-361">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="4aab4-361">A DTO may be used to:</span></span>

* <span data-ttu-id="4aab4-362">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="4aab4-362">Prevent over-posting.</span></span>
* <span data-ttu-id="4aab4-363">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="4aab4-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="4aab4-364">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="4aab4-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="4aab4-365">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="4aab4-366">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="4aab4-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="4aab4-367">Para demonstrar a abordagem do DTO, atualize `TodoItem` a classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="4aab4-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="4aab4-368">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="4aab4-369">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="4aab4-370">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="4aab4-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="4aab4-371">Atualize o `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="4aab4-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="4aab4-372">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4aab4-373">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="4aab4-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="4aab4-374">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="4aab4-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4aab4-375">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="4aab4-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4aab4-376">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-376">Create a web API project.</span></span>
> * <span data-ttu-id="4aab4-377">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4aab4-378">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-378">Add a controller.</span></span>
> * <span data-ttu-id="4aab4-379">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="4aab4-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="4aab4-380">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="4aab4-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4aab4-381">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-381">Specify return values.</span></span>
> * <span data-ttu-id="4aab4-382">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="4aab4-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4aab4-383">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4aab4-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="4aab4-384">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="4aab4-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="4aab4-385">Visão geral</span><span class="sxs-lookup"><span data-stu-id="4aab4-385">Overview</span></span>

<span data-ttu-id="4aab4-386">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="4aab4-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4aab4-387">API</span><span class="sxs-lookup"><span data-stu-id="4aab4-387">API</span></span> | <span data-ttu-id="4aab4-388">Descrição</span><span class="sxs-lookup"><span data-stu-id="4aab4-388">Description</span></span> | <span data-ttu-id="4aab4-389">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="4aab4-389">Request body</span></span> | <span data-ttu-id="4aab4-390">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="4aab4-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4aab4-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4aab4-391">GET /api/TodoItems</span></span> | <span data-ttu-id="4aab4-392">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-392">Get all to-do items</span></span> | <span data-ttu-id="4aab4-393">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-393">None</span></span> | <span data-ttu-id="4aab4-394">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-394">Array of to-do items</span></span>|
|<span data-ttu-id="4aab4-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4aab4-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4aab4-396">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="4aab4-396">Get an item by ID</span></span> | <span data-ttu-id="4aab4-397">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-397">None</span></span> | <span data-ttu-id="4aab4-398">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-398">To-do item</span></span>|
|<span data-ttu-id="4aab4-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4aab4-399">POST /api/TodoItems</span></span> | <span data-ttu-id="4aab4-400">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="4aab4-400">Add a new item</span></span> | <span data-ttu-id="4aab4-401">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-401">To-do item</span></span> | <span data-ttu-id="4aab4-402">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-402">To-do item</span></span> |
|<span data-ttu-id="4aab4-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4aab4-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4aab4-404">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="4aab4-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4aab4-405">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-405">To-do item</span></span> | <span data-ttu-id="4aab4-406">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-406">None</span></span> |
|<span data-ttu-id="4aab4-407">EXCLUIR/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="4aab4-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4aab4-408">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="4aab4-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4aab4-409">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-409">None</span></span> | <span data-ttu-id="4aab4-410">Nenhum</span><span class="sxs-lookup"><span data-stu-id="4aab4-410">None</span></span>|

<span data-ttu-id="4aab4-411">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-411">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4aab4-417">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4aab4-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-420">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4aab4-421">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="4aab4-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-423">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4aab4-424">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4aab4-425">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4aab4-426">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4aab4-427">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4aab4-428">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-428">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4aab4-431">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4aab4-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4aab4-432">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4aab4-433">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="4aab4-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4aab4-434">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4aab4-435">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-436">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4aab4-437">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-437">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4aab4-439">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4aab4-441">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4aab4-442">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4aab4-444">Testar a API</span><span class="sxs-lookup"><span data-stu-id="4aab4-444">Test the API</span></span>

<span data-ttu-id="4aab4-445">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-445">The project template creates a `values` API.</span></span> <span data-ttu-id="4aab4-446">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4aab4-448">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4aab4-449">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4aab4-450">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4aab4-451">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4aab4-453">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4aab4-454">Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-455">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4aab4-456">Selecione **executar** > **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4aab4-457">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4aab4-458">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4aab4-459">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="4aab4-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4aab4-460">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="4aab4-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4aab4-461">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="4aab4-461">Add a model class</span></span>

<span data-ttu-id="4aab4-462">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4aab4-463">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-465">No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4aab4-466">Selecione **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4aab4-467">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="4aab4-468">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4aab4-469">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4aab4-470">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4aab4-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4aab4-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4aab4-472">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4aab4-473">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-474">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4aab4-475">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-475">Right-click the project.</span></span> <span data-ttu-id="4aab4-476">Selecione **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4aab4-477">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-477">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4aab4-479">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4aab4-480">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4aab4-481">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4aab4-482">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="4aab4-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4aab4-483">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="4aab4-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4aab4-484">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="4aab4-484">Add a database context</span></span>

<span data-ttu-id="4aab4-485">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4aab4-486">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-488">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4aab4-489">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-490">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4aab4-491">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4aab4-492">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4aab4-493">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="4aab4-493">Register the database context</span></span>

<span data-ttu-id="4aab4-494">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4aab4-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4aab4-495">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="4aab4-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="4aab4-496">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="4aab4-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4aab4-497">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="4aab4-497">The preceding code:</span></span>

* <span data-ttu-id="4aab4-498">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="4aab4-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4aab4-499">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="4aab4-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4aab4-500">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="4aab4-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4aab4-501">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="4aab4-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-503">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4aab4-504">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4aab4-505">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4aab4-506">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-508">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4aab4-509">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4aab4-510">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4aab4-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4aab4-511">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="4aab4-511">The preceding code:</span></span>

* <span data-ttu-id="4aab4-512">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="4aab4-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4aab4-513">Marca a classe com o [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4aab4-514">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4aab4-515">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4aab4-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4aab4-516">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4aab4-517">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4aab4-518">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="4aab4-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4aab4-519">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="4aab4-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4aab4-520">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4aab4-521">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="4aab4-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4aab4-522">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="4aab4-522">Add Get methods</span></span>

<span data-ttu-id="4aab4-523">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="4aab4-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4aab4-524">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="4aab4-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4aab4-525">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="4aab4-525">Stop the app if it's still running.</span></span> <span data-ttu-id="4aab4-526">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="4aab4-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4aab4-527">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4aab4-528">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="4aab4-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4aab4-529">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="4aab4-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4aab4-530">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="4aab4-530">Routing and URL paths</span></span>

<span data-ttu-id="4aab4-531">O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="4aab4-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4aab4-532">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="4aab4-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4aab4-533">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="4aab4-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4aab4-534">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="4aab4-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4aab4-535">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="4aab4-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4aab4-536">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4aab4-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4aab4-537">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="4aab4-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4aab4-538">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-538">This sample doesn't use a template.</span></span> <span data-ttu-id="4aab4-539">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4aab4-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4aab4-540">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4aab4-541">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4aab4-542">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="4aab4-542">Return values</span></span>

<span data-ttu-id="4aab4-543">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4aab4-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4aab4-544">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="4aab4-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4aab4-545">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="4aab4-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4aab4-546">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="4aab4-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4aab4-547">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="4aab4-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4aab4-548">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="4aab4-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4aab4-549">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="4aab4-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4aab4-550">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="4aab4-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4aab4-551">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4aab4-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4aab4-552">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="4aab4-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="4aab4-553">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4aab4-554">Instalar o [postmaster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4aab4-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="4aab4-555">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-555">Start the web app.</span></span>
* <span data-ttu-id="4aab4-556">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="4aab4-556">Start Postman.</span></span>
* <span data-ttu-id="4aab4-557">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4aab4-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4aab4-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4aab4-559">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4aab4-560">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4aab4-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4aab4-561">De **Postman** > **preferências** do postmaster (guia**geral** ), desabilite a **verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="4aab4-562">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="4aab4-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="4aab4-563">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="4aab4-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4aab4-564">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="4aab4-564">Create a new request.</span></span>
  * <span data-ttu-id="4aab4-565">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4aab4-566">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4aab4-567">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4aab4-568">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="4aab4-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4aab4-569">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-569">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4aab4-571">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="4aab4-571">Add a Create method</span></span>

<span data-ttu-id="4aab4-572">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="4aab4-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4aab4-573">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="4aab4-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4aab4-574">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="4aab4-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4aab4-575">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="4aab4-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4aab4-576">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="4aab4-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4aab4-577">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="4aab4-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4aab4-578">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="4aab4-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4aab4-579">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4aab4-580">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4aab4-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4aab4-581">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="4aab4-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4aab4-582">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4aab4-583">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4aab4-584">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-584">Build the project.</span></span>
* <span data-ttu-id="4aab4-585">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4aab4-586">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="4aab4-587">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4aab4-588">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4aab4-589">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="4aab4-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4aab4-590">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-590">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="4aab4-592">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4aab4-593">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="4aab4-593">Test the location header URI</span></span>

* <span data-ttu-id="4aab4-594">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4aab4-595">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="4aab4-595">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4aab4-597">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="4aab4-597">Set the method to GET.</span></span>
* <span data-ttu-id="4aab4-598">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="4aab4-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="4aab4-599">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4aab4-600">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="4aab4-601">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="4aab4-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4aab4-602">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4aab4-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4aab4-603">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4aab4-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4aab4-604">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="4aab4-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4aab4-605">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="4aab4-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4aab4-606">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4aab4-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4aab4-607">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="4aab4-608">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4aab4-609">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4aab4-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4aab4-610">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4aab4-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4aab4-611">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="4aab4-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4aab4-612">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="4aab4-612">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4aab4-614">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4aab4-615">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="4aab4-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4aab4-616">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4aab4-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4aab4-617">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4aab4-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4aab4-618">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="4aab4-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4aab4-619">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4aab4-620">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="4aab4-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="4aab4-621">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4aab4-621">Select **Send**.</span></span>

<span data-ttu-id="4aab4-622">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="4aab4-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4aab4-623">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="4aab4-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4aab4-624">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="4aab4-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="4aab4-625">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="4aab4-626">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="4aab4-626">jQuery initiates the request.</span></span> <span data-ttu-id="4aab4-627">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="4aab4-628">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="4aab4-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4aab4-629">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4aab4-630">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4aab4-631">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="4aab4-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4aab4-632">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4aab4-633">Substitua o conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="4aab4-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4aab4-634">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="4aab4-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4aab4-635">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4aab4-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4aab4-636">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index. html*&mdash;o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="4aab4-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4aab4-637">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="4aab4-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="4aab4-638">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="4aab4-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4aab4-639">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="4aab4-639">Get a list of to-do items</span></span>

<span data-ttu-id="4aab4-640">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="4aab4-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4aab4-641">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="4aab4-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4aab4-642">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="4aab4-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4aab4-643">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="4aab4-643">Add a to-do item</span></span>

<span data-ttu-id="4aab4-644">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="4aab4-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="4aab4-645">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="4aab4-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4aab4-646">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="4aab4-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4aab4-647">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="4aab4-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4aab4-648">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="4aab4-648">Update a to-do item</span></span>

<span data-ttu-id="4aab4-649">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="4aab4-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4aab4-650">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="4aab4-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4aab4-651">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="4aab4-651">Delete a to-do item</span></span>

<span data-ttu-id="4aab4-652">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="4aab4-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="4aab4-653">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="4aab4-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="4aab4-654">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4aab4-654">Additional resources</span></span>

<span data-ttu-id="4aab4-655">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="4aab4-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4aab4-656">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4aab4-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4aab4-657">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4aab4-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4aab4-658">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="4aab4-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
