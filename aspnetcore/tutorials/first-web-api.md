---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 71ab0fc0a679acf540fa53fa2a9c8ba893b34edf
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724348"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="e6645-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6645-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="e6645-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="e6645-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="e6645-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6645-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6645-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="e6645-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e6645-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-107">Create a web API project.</span></span>
> * <span data-ttu-id="e6645-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6645-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e6645-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="e6645-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="e6645-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="e6645-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="e6645-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="e6645-111">Call the web API with Postman.</span></span>

<span data-ttu-id="e6645-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6645-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="e6645-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="e6645-113">Overview</span></span>

<span data-ttu-id="e6645-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="e6645-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e6645-115">API</span><span class="sxs-lookup"><span data-stu-id="e6645-115">API</span></span> | <span data-ttu-id="e6645-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="e6645-116">Description</span></span> | <span data-ttu-id="e6645-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="e6645-117">Request body</span></span> | <span data-ttu-id="e6645-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="e6645-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="e6645-119">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-119">Get all to-do items</span></span> | <span data-ttu-id="e6645-120">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-120">None</span></span> | <span data-ttu-id="e6645-121">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="e6645-122">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="e6645-122">Get an item by ID</span></span> | <span data-ttu-id="e6645-123">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-123">None</span></span> | <span data-ttu-id="e6645-124">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="e6645-125">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="e6645-125">Add a new item</span></span> | <span data-ttu-id="e6645-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-126">To-do item</span></span> | <span data-ttu-id="e6645-127">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="e6645-128">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="e6645-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e6645-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-129">To-do item</span></span> | <span data-ttu-id="e6645-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-130">None</span></span> |
|<span data-ttu-id="e6645-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e6645-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="e6645-132">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e6645-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e6645-133">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-133">None</span></span> | <span data-ttu-id="e6645-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-134">None</span></span>|

<span data-ttu-id="e6645-135">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-135">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e6645-141">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e6645-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e6645-145">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="e6645-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-147">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="e6645-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e6645-148">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="e6645-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e6645-149">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e6645-150">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="e6645-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="e6645-151">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-151">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e6645-154">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e6645-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e6645-155">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e6645-156">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e6645-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="e6645-157">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="e6645-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="e6645-158">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="e6645-158">The preceding commands:</span></span>

  * <span data-ttu-id="e6645-159">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e6645-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="e6645-160">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="e6645-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6645-162">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="e6645-162">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e6645-164">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="e6645-165">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="e6645-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="e6645-167">Confirme se a **estrutura de destino** está definida como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="e6645-167">Confirm the **Target Framework** is set to **.NET Core 3.1**.</span></span> <span data-ttu-id="e6645-168">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-168">Select **Next**.</span></span>

  ![seleção do macOS .NET Core 3,1](first-web-api-mac/_static/api_31_config.png)

* <span data-ttu-id="e6645-170">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="e6645-172">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e6645-172">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="e6645-173">Testar a API</span><span class="sxs-lookup"><span data-stu-id="e6645-173">Test the API</span></span>

<span data-ttu-id="e6645-174">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="e6645-174">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="e6645-175">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-175">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6645-177">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-177">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e6645-178">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e6645-178">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e6645-179">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="e6645-179">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e6645-180">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="e6645-180">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e6645-182">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-182">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e6645-183">Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="e6645-183">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-184">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-184">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e6645-185">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-185">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e6645-186">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e6645-186">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e6645-187">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="e6645-187">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e6645-188">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="e6645-188">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="e6645-189">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="e6645-189">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="e6645-190">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="e6645-190">Add a model class</span></span>

<span data-ttu-id="e6645-191">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-191">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e6645-192">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e6645-192">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-194">No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-194">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e6645-195">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="e6645-195">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e6645-196">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-196">Name the folder *Models*.</span></span>

* <span data-ttu-id="e6645-197">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="e6645-197">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e6645-198">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-198">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e6645-199">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-199">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e6645-201">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-201">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e6645-202">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-202">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-203">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6645-204">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-204">Right-click the project.</span></span> <span data-ttu-id="e6645-205">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="e6645-205">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e6645-206">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-206">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e6645-208">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="e6645-208">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e6645-209">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="e6645-209">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e6645-210">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-210">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="e6645-211">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="e6645-211">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e6645-212">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="e6645-212">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e6645-213">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="e6645-213">Add a database context</span></span>

<span data-ttu-id="e6645-214">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e6645-214">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e6645-215">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e6645-215">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-216">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="e6645-217">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e6645-217">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="e6645-218">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="e6645-218">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="e6645-219">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="e6645-219">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="e6645-220">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="e6645-220">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="e6645-221">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-221">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="e6645-222">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="e6645-222">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="e6645-224">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="e6645-224">Add the TodoContext database context</span></span>

* <span data-ttu-id="e6645-225">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="e6645-225">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e6645-226">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-226">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e6645-227">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-227">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e6645-228">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-228">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e6645-229">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-229">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e6645-230">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="e6645-230">Register the database context</span></span>

<span data-ttu-id="e6645-231">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6645-231">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e6645-232">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="e6645-232">The container provides the service to controllers.</span></span>

<span data-ttu-id="e6645-233">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="e6645-233">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="e6645-234">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="e6645-234">The preceding code:</span></span>

* <span data-ttu-id="e6645-235">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="e6645-235">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e6645-236">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="e6645-236">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e6645-237">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="e6645-237">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="e6645-238">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="e6645-238">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-239">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-240">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="e6645-240">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e6645-241">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="e6645-241">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e6645-242">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-242">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="e6645-243">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="e6645-243">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="e6645-244">Selecione **TodoItem (TodoApi. Models)** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="e6645-244">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="e6645-245">Selecione **TodoContext (TodoApi. Models)** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="e6645-245">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="e6645-246">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-246">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e6645-247">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e6645-248">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e6645-248">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="e6645-249">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="e6645-249">The preceding commands:</span></span>

* <span data-ttu-id="e6645-250">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="e6645-250">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="e6645-251">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="e6645-251">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="e6645-252">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="e6645-252">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="e6645-253">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="e6645-253">The generated code:</span></span>

* <span data-ttu-id="e6645-254">Marca a classe com o [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="e6645-254">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e6645-255">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-255">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e6645-256">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="e6645-256">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e6645-257">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="e6645-257">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e6645-258">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="e6645-258">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="e6645-259">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="e6645-259">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="e6645-260">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="e6645-260">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="e6645-261">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="e6645-261">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="e6645-262">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="e6645-262">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="e6645-263">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="e6645-263">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="e6645-264">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-264">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="e6645-265">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="e6645-265">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="e6645-266">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="e6645-266">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e6645-267">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6645-267">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e6645-268">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="e6645-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="e6645-269">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="e6645-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="e6645-270">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="e6645-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e6645-271">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="e6645-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="e6645-272">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="e6645-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="e6645-273">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e6645-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e6645-274">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="e6645-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e6645-275">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="e6645-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="e6645-276">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="e6645-276">Install Postman</span></span>

<span data-ttu-id="e6645-277">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e6645-278">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="e6645-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="e6645-279">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-279">Start the web app.</span></span>
* <span data-ttu-id="e6645-280">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="e6645-280">Start Postman.</span></span>
* <span data-ttu-id="e6645-281">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="e6645-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="e6645-282">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e6645-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="e6645-283">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="e6645-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="e6645-284">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="e6645-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="e6645-285">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="e6645-285">Create a new request.</span></span>
* <span data-ttu-id="e6645-286">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="e6645-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e6645-287">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="e6645-287">Select the **Body** tab.</span></span>
* <span data-ttu-id="e6645-288">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="e6645-288">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e6645-289">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="e6645-289">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e6645-290">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="e6645-290">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e6645-291">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-291">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e6645-293">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="e6645-293">Test the location header URI</span></span>

* <span data-ttu-id="e6645-294">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="e6645-294">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e6645-295">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="e6645-295">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="e6645-297">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="e6645-297">Set the method to GET.</span></span>
* <span data-ttu-id="e6645-298">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="e6645-298">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e6645-299">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-299">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="e6645-300">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="e6645-300">Examine the GET methods</span></span>

<span data-ttu-id="e6645-301">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="e6645-301">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="e6645-302">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="e6645-302">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="e6645-303">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e6645-303">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="e6645-304">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="e6645-304">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="e6645-305">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="e6645-305">Test Get with Postman</span></span>

* <span data-ttu-id="e6645-306">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="e6645-306">Create a new request.</span></span>
* <span data-ttu-id="e6645-307">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="e6645-307">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="e6645-308">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e6645-308">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e6645-309">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e6645-309">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e6645-310">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="e6645-310">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e6645-311">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-311">Select **Send**.</span></span>

<span data-ttu-id="e6645-312">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="e6645-312">This app uses an in-memory database.</span></span> <span data-ttu-id="e6645-313">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="e6645-313">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="e6645-314">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-314">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="e6645-315">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="e6645-315">Routing and URL paths</span></span>

<span data-ttu-id="e6645-316">O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="e6645-316">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e6645-317">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="e6645-317">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e6645-318">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="e6645-318">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="e6645-319">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="e6645-319">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e6645-320">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="e6645-320">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="e6645-321">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e6645-321">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e6645-322">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="e6645-322">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e6645-323">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="e6645-323">This sample doesn't use a template.</span></span> <span data-ttu-id="e6645-324">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e6645-324">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e6645-325">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="e6645-325">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e6645-326">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="e6645-326">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e6645-327">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="e6645-327">Return values</span></span>

<span data-ttu-id="e6645-328">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e6645-328">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e6645-329">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="e6645-329">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e6645-330">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="e6645-330">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e6645-331">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="e6645-331">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e6645-332">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6645-332">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e6645-333">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="e6645-333">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e6645-334">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="e6645-334">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e6645-335">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="e6645-335">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e6645-336">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e6645-336">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="e6645-337">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-337">The PutTodoItem method</span></span>

<span data-ttu-id="e6645-338">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e6645-338">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="e6645-339">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e6645-339">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e6645-340">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e6645-340">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e6645-341">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="e6645-341">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e6645-342">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e6645-342">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e6645-343">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6645-343">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e6645-344">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-344">Test the PutTodoItem method</span></span>

<span data-ttu-id="e6645-345">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="e6645-345">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e6645-346">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e6645-346">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e6645-347">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e6645-347">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e6645-348">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="e6645-348">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e6645-349">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="e6645-349">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="e6645-351">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-351">The DeleteTodoItem method</span></span>

<span data-ttu-id="e6645-352">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e6645-352">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e6645-353">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e6645-354">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="e6645-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e6645-355">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="e6645-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e6645-356">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="e6645-356">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e6645-357">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-357">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="e6645-358">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="e6645-358">Prevent over-posting</span></span>

<span data-ttu-id="e6645-359">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="e6645-359">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="e6645-360">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="e6645-360">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="e6645-361">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="e6645-361">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="e6645-362">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="e6645-362">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="e6645-363">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="e6645-363">**DTO** is used in this article.</span></span>

<span data-ttu-id="e6645-364">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="e6645-364">A DTO may be used to:</span></span>

* <span data-ttu-id="e6645-365">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="e6645-365">Prevent over-posting.</span></span>
* <span data-ttu-id="e6645-366">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="e6645-366">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="e6645-367">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="e6645-367">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="e6645-368">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="e6645-368">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="e6645-369">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="e6645-369">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="e6645-370">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="e6645-370">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="e6645-371">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="e6645-371">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="e6645-372">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="e6645-372">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="e6645-373">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="e6645-373">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="e6645-374">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="e6645-374">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="e6645-375">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="e6645-375">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e6645-376">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="e6645-376">Call the web API with JavaScript</span></span>

<span data-ttu-id="e6645-377">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="e6645-377">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6645-378">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="e6645-378">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e6645-379">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-379">Create a web API project.</span></span>
> * <span data-ttu-id="e6645-380">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6645-380">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e6645-381">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="e6645-381">Add a controller.</span></span>
> * <span data-ttu-id="e6645-382">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="e6645-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="e6645-383">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="e6645-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="e6645-384">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="e6645-384">Specify return values.</span></span>
> * <span data-ttu-id="e6645-385">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="e6645-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="e6645-386">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e6645-386">Call the web API with JavaScript.</span></span>

<span data-ttu-id="e6645-387">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="e6645-387">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="e6645-388">Visão geral</span><span class="sxs-lookup"><span data-stu-id="e6645-388">Overview</span></span>

<span data-ttu-id="e6645-389">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="e6645-389">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e6645-390">API</span><span class="sxs-lookup"><span data-stu-id="e6645-390">API</span></span> | <span data-ttu-id="e6645-391">Descrição</span><span class="sxs-lookup"><span data-stu-id="e6645-391">Description</span></span> | <span data-ttu-id="e6645-392">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="e6645-392">Request body</span></span> | <span data-ttu-id="e6645-393">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="e6645-393">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="e6645-394">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e6645-394">GET /api/TodoItems</span></span> | <span data-ttu-id="e6645-395">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-395">Get all to-do items</span></span> | <span data-ttu-id="e6645-396">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-396">None</span></span> | <span data-ttu-id="e6645-397">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-397">Array of to-do items</span></span>|
|<span data-ttu-id="e6645-398">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e6645-398">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="e6645-399">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="e6645-399">Get an item by ID</span></span> | <span data-ttu-id="e6645-400">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-400">None</span></span> | <span data-ttu-id="e6645-401">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-401">To-do item</span></span>|
|<span data-ttu-id="e6645-402">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e6645-402">POST /api/TodoItems</span></span> | <span data-ttu-id="e6645-403">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="e6645-403">Add a new item</span></span> | <span data-ttu-id="e6645-404">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-404">To-do item</span></span> | <span data-ttu-id="e6645-405">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-405">To-do item</span></span> |
|<span data-ttu-id="e6645-406">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e6645-406">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="e6645-407">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="e6645-407">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e6645-408">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-408">To-do item</span></span> | <span data-ttu-id="e6645-409">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-409">None</span></span> |
|<span data-ttu-id="e6645-410">EXCLUIR/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e6645-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="e6645-411">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e6645-411">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e6645-412">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-412">None</span></span> | <span data-ttu-id="e6645-413">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e6645-413">None</span></span>|

<span data-ttu-id="e6645-414">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-414">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e6645-420">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e6645-420">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-421">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-423">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-423">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e6645-424">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="e6645-424">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-425">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-425">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-426">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="e6645-426">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e6645-427">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="e6645-427">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e6645-428">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-428">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e6645-429">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="e6645-429">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="e6645-430">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-430">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="e6645-431">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="e6645-431">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e6645-434">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e6645-434">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e6645-435">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-435">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e6645-436">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e6645-436">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="e6645-437">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-437">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="e6645-438">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="e6645-438">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-439">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6645-440">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="e6645-440">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e6645-442">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-442">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="e6645-443">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-443">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="e6645-444">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="e6645-444">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="e6645-445">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="e6645-447">Testar a API</span><span class="sxs-lookup"><span data-stu-id="e6645-447">Test the API</span></span>

<span data-ttu-id="e6645-448">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="e6645-448">The project template creates a `values` API.</span></span> <span data-ttu-id="e6645-449">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6645-451">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e6645-452">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e6645-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e6645-453">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="e6645-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e6645-454">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="e6645-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e6645-456">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e6645-457">Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="e6645-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-458">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e6645-459">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e6645-460">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e6645-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e6645-461">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="e6645-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e6645-462">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="e6645-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="e6645-463">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="e6645-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="e6645-464">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="e6645-464">Add a model class</span></span>

<span data-ttu-id="e6645-465">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6645-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e6645-466">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e6645-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-468">No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e6645-469">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="e6645-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e6645-470">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="e6645-471">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="e6645-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e6645-472">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e6645-473">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e6645-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6645-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e6645-475">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e6645-476">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e6645-477">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6645-478">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-478">Right-click the project.</span></span> <span data-ttu-id="e6645-479">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="e6645-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e6645-480">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-480">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e6645-482">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="e6645-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e6645-483">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="e6645-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e6645-484">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="e6645-485">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="e6645-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e6645-486">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="e6645-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e6645-487">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="e6645-487">Add a database context</span></span>

<span data-ttu-id="e6645-488">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e6645-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e6645-489">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e6645-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-491">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="e6645-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e6645-492">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e6645-493">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e6645-494">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="e6645-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e6645-495">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e6645-496">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="e6645-496">Register the database context</span></span>

<span data-ttu-id="e6645-497">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6645-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e6645-498">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="e6645-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="e6645-499">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="e6645-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="e6645-500">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="e6645-500">The preceding code:</span></span>

* <span data-ttu-id="e6645-501">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="e6645-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e6645-502">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="e6645-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e6645-503">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="e6645-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e6645-504">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="e6645-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-506">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="e6645-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e6645-507">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="e6645-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="e6645-508">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="e6645-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="e6645-509">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e6645-511">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e6645-512">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="e6645-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="e6645-513">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e6645-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="e6645-514">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="e6645-514">The preceding code:</span></span>

* <span data-ttu-id="e6645-515">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="e6645-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="e6645-516">Marca a classe com o [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="e6645-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e6645-517">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e6645-518">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="e6645-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e6645-519">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="e6645-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e6645-520">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="e6645-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="e6645-521">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="e6645-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="e6645-522">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6645-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="e6645-523">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="e6645-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="e6645-524">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="e6645-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="e6645-525">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="e6645-525">Add Get methods</span></span>

<span data-ttu-id="e6645-526">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="e6645-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="e6645-527">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="e6645-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="e6645-528">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="e6645-528">Stop the app if it's still running.</span></span> <span data-ttu-id="e6645-529">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="e6645-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="e6645-530">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="e6645-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="e6645-531">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e6645-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="e6645-532">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="e6645-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="e6645-533">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="e6645-533">Routing and URL paths</span></span>

<span data-ttu-id="e6645-534">O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="e6645-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e6645-535">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="e6645-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e6645-536">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="e6645-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="e6645-537">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="e6645-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e6645-538">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="e6645-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="e6645-539">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e6645-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e6645-540">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="e6645-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e6645-541">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="e6645-541">This sample doesn't use a template.</span></span> <span data-ttu-id="e6645-542">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e6645-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e6645-543">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="e6645-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e6645-544">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="e6645-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e6645-545">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="e6645-545">Return values</span></span>

<span data-ttu-id="e6645-546">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e6645-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e6645-547">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="e6645-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e6645-548">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="e6645-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e6645-549">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="e6645-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e6645-550">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6645-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e6645-551">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="e6645-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e6645-552">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="e6645-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e6645-553">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="e6645-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e6645-554">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e6645-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="e6645-555">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="e6645-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="e6645-556">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e6645-557">Instalar o [postmaster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="e6645-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="e6645-558">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-558">Start the web app.</span></span>
* <span data-ttu-id="e6645-559">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="e6645-559">Start Postman.</span></span>
* <span data-ttu-id="e6645-560">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e6645-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6645-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6645-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6645-562">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e6645-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e6645-563">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e6645-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e6645-564">De **Postman**  >  **preferências** do postmaster (guia**geral** ), desabilite a **verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e6645-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="e6645-565">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="e6645-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="e6645-566">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="e6645-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="e6645-567">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="e6645-567">Create a new request.</span></span>
  * <span data-ttu-id="e6645-568">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="e6645-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="e6645-569">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e6645-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="e6645-570">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e6645-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="e6645-571">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="e6645-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e6645-572">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-572">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="e6645-574">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="e6645-574">Add a Create method</span></span>

<span data-ttu-id="e6645-575">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="e6645-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="e6645-576">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="e6645-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e6645-577">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6645-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e6645-578">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="e6645-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="e6645-579">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="e6645-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="e6645-580">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="e6645-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e6645-581">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="e6645-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="e6645-582">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="e6645-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="e6645-583">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e6645-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e6645-584">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="e6645-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e6645-585">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="e6645-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="e6645-586">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="e6645-587">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-587">Build the project.</span></span>
* <span data-ttu-id="e6645-588">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="e6645-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e6645-589">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="e6645-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="e6645-590">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="e6645-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e6645-591">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="e6645-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e6645-592">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="e6645-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e6645-593">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-593">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="e6645-595">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e6645-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e6645-596">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="e6645-596">Test the location header URI</span></span>

* <span data-ttu-id="e6645-597">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="e6645-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e6645-598">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="e6645-598">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="e6645-600">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="e6645-600">Set the method to GET.</span></span>
* <span data-ttu-id="e6645-601">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="e6645-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="e6645-602">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="e6645-603">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="e6645-604">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6645-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="e6645-605">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e6645-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e6645-606">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e6645-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e6645-607">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="e6645-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e6645-608">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e6645-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e6645-609">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6645-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e6645-610">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="e6645-611">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="e6645-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e6645-612">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e6645-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e6645-613">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e6645-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e6645-614">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="e6645-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e6645-615">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="e6645-615">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="e6645-617">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="e6645-618">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6645-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="e6645-619">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e6645-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e6645-620">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e6645-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e6645-621">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="e6645-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e6645-622">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="e6645-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e6645-623">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="e6645-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="e6645-624">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e6645-624">Select **Send**.</span></span>

<span data-ttu-id="e6645-625">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="e6645-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="e6645-626">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="e6645-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e6645-627">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="e6645-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="e6645-628">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="e6645-629">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="e6645-629">jQuery initiates the request.</span></span> <span data-ttu-id="e6645-630">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="e6645-631">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6645-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="e6645-632">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="e6645-633">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e6645-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="e6645-634">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e6645-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="e6645-635">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e6645-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="e6645-636">Substitua o conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6645-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="e6645-637">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="e6645-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="e6645-638">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e6645-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="e6645-639">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index.html* &mdash; o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6645-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="e6645-640">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="e6645-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="e6645-641">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="e6645-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="e6645-642">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="e6645-642">Get a list of to-do items</span></span>

<span data-ttu-id="e6645-643">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="e6645-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="e6645-644">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="e6645-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="e6645-645">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="e6645-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="e6645-646">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="e6645-646">Add a to-do item</span></span>

<span data-ttu-id="e6645-647">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="e6645-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="e6645-648">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="e6645-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="e6645-649">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="e6645-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="e6645-650">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="e6645-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="e6645-651">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="e6645-651">Update a to-do item</span></span>

<span data-ttu-id="e6645-652">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="e6645-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="e6645-653">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="e6645-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="e6645-654">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="e6645-654">Delete a to-do item</span></span>

<span data-ttu-id="e6645-655">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="e6645-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="e6645-656">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="e6645-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="e6645-657">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e6645-657">Additional resources</span></span>

<span data-ttu-id="e6645-658">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="e6645-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="e6645-659">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e6645-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e6645-660">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6645-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="e6645-661">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="e6645-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
