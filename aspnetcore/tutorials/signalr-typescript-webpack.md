---
title: Use ASP.NET SignalR Core com TypeScript e Webpack
author: ssougnez
description: Neste tutorial, você configura o Webpack para SignalR empacotar e construir um aplicativo web ASP.NET Core cujo cliente está escrito no TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511334"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="c8ca9-103">Usar o SignalR do ASP.NET Core com TypeScript e Webpack</span><span class="sxs-lookup"><span data-stu-id="c8ca9-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="c8ca9-104">Por [Sébastien Sougnez](https://twitter.com/ssougnez) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="c8ca9-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="c8ca9-105">O [Webpack](https://webpack.js.org/) habilita os desenvolvedores a agrupar e criar recursos de um aplicativo Web do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="c8ca9-106">Este tutorial demonstra como usar o Webpack em um aplicativo Web SignalR do ASP.NET Core cujo cliente é escrito em [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="c8ca9-107">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c8ca9-108">Gerar um aplicativo inicial SignalR do ASP.NET Core por scaffold</span><span class="sxs-lookup"><span data-stu-id="c8ca9-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="c8ca9-109">Configurar o cliente TypeScript do SignalR</span><span class="sxs-lookup"><span data-stu-id="c8ca9-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="c8ca9-110">Configurar um pipeline de build usando o Webpack</span><span class="sxs-lookup"><span data-stu-id="c8ca9-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="c8ca9-111">Configurar o servidor SignalR</span><span class="sxs-lookup"><span data-stu-id="c8ca9-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="c8ca9-112">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="c8ca9-112">Enable communication between client and server</span></span>

<span data-ttu-id="c8ca9-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c8ca9-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c8ca9-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c8ca9-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8ca9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8ca9-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8ca9-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **carga de trabalho de ASP.NET e desenvolvimento web**</span><span class="sxs-lookup"><span data-stu-id="c8ca9-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c8ca9-117">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c8ca9-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c8ca9-118">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c8ca9-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8ca9-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c8ca9-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c8ca9-121">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c8ca9-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c8ca9-122">C# para Visual Studio Code versão 1.17.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c8ca9-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="c8ca9-123">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c8ca9-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="c8ca9-124">Criar o aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8ca9-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8ca9-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8ca9-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c8ca9-126">Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="c8ca9-127">Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="c8ca9-128">Siga estas instruções no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="c8ca9-129">Inicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-129">Launch Visual Studio.</span></span> <span data-ttu-id="c8ca9-130">Na janela inicial, **selecione Continuar sem código**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="c8ca9-131">Navegue para **ferramentas** > **projetos de opções** > **e soluções** > **Web Package Management** > **External Web Tools**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="c8ca9-132">Selecione a entrada *$(PATH)* na lista.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="c8ca9-133">Clique na seta para cima para mover a entrada para a segunda posição da lista e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="c8ca9-135">A configuração do Visual Studio está completa.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="c8ca9-136">Use a opção **Menu Arquivo** > **Novo** > **Projeto** e escolha o modelo ASP.NET Do **Aplicativo Web** Principal.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="c8ca9-137">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-137">Select **Next**.</span></span>
1. <span data-ttu-id="c8ca9-138">Nomeie o projeto *SignalRWebPack*e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="c8ca9-139">Selecione *.NET Core* na parte de entrada do quadro de destino e selecione *ASP.NET Núcleo 3.1* na parada do seletor de quadros.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="c8ca9-140">Selecione o **modelo Vazio** e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="c8ca9-141">Adicione `Microsoft.TypeScript.MSBuild` o pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="c8ca9-142">No **Solution Explorer** (painel direito), clique com o botão direito do mouse no nó do projeto e **selecione Gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="c8ca9-143">Na guia **Procurar,** `Microsoft.TypeScript.MSBuild`procure e clique em **Instalar** à direita para instalar o pacote.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="c8ca9-144">O Visual Studio adiciona o pacote NuGet sob o nó **Dependencies** no **Solution Explorer**, permitindo a compilação TypeScript no projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8ca9-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c8ca9-146">Execute o comando a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="c8ca9-147">O `dotnet new` comando cria um aplicativo web ASP.NET Core vazio em um diretório *SignalRWebPack.*</span><span class="sxs-lookup"><span data-stu-id="c8ca9-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="c8ca9-148">O `code` comando abre a pasta *SignalRWebPack* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="c8ca9-149">Execute o seguinte comando .NET Core CLI no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="c8ca9-150">O comando anterior adiciona o pacote [Microsoft.TypeScript.MSBuild,](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) permitindo a compilação TypeScript no projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="c8ca9-151">Configurar Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="c8ca9-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="c8ca9-152">As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="c8ca9-153">Execute o seguinte comando na raiz do projeto para criar um arquivo *package.json:*</span><span class="sxs-lookup"><span data-stu-id="c8ca9-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="c8ca9-154">Adicione a propriedade destacada ao arquivo *package.json* e salve as alterações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="c8ca9-155">Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="c8ca9-156">Instalar os pacotes de npm exigidos.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-156">Install the required npm packages.</span></span> <span data-ttu-id="c8ca9-157">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="c8ca9-158">Alguns detalhes de comando a se observar:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-158">Some command details to note:</span></span>

    * <span data-ttu-id="c8ca9-159">Um número de versão segue o sinal `@` para cada nome de pacote.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="c8ca9-160">O npm instala essas versões específicas do pacote.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="c8ca9-161">A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="c8ca9-162">Por exemplo, `"webpack": "4.41.5"` é usado em vez de `"webpack": "^4.41.5"`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="c8ca9-163">Essa opção evita atualizações não intencionais para versões de pacote mais recentes.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="c8ca9-164">Consulte os docs [npm-install](https://docs.npmjs.com/cli/install) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="c8ca9-165">Substitua `scripts` a propriedade do arquivo *package.json* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="c8ca9-166">Uma explicação sobre os scripts:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="c8ca9-167">`build`: Agrupa os recursos do lado do cliente no modo de desenvolvimento e observa para alterações de arquivos.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="c8ca9-168">O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="c8ca9-169">A opção `mode` desabilita otimizações de produção, como tree shaking e minificação.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="c8ca9-170">Use somente `build` no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="c8ca9-171">`release`: Agrupa os recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="c8ca9-172">`publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="c8ca9-173">Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="c8ca9-174">Crie um arquivo chamado *webpack.config.js*, na raiz do projeto, com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="c8ca9-175">O arquivo precedente configura a compilação Webpack.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="c8ca9-176">Detalhes de configuração a serem notados:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="c8ca9-177">A propriedade `output` substitui o valor padrão do *dist*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="c8ca9-178">Em vez disso, o lote é emitido no diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="c8ca9-179">A matriz `resolve.extensions` inclui *.js* para importar o JavaScript do cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="c8ca9-180">Crie um novo diretório *src* na raiz do projeto para armazenar os ativos do lado do cliente do projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="c8ca9-181">Crie *src/index.html* com a marcação seguinte.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="c8ca9-182">A HTML precedente define a marcação clichê da página inicial.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="c8ca9-183">Crie um novo diretório *src/css*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="c8ca9-184">Seu propósito é armazenar os arquivos do projeto *.css*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="c8ca9-185">Crie *src/css/main.css* com o seguinte CSS:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="c8ca9-186">O arquivo precedente *main.css* define o estilo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="c8ca9-187">Crie *src/tsconfig.json* com o seguinte JSON:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="c8ca9-188">O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="c8ca9-189">Crie *src/index.ts* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="c8ca9-190">O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="c8ca9-191">`keyup`: Este evento é acionado `tbMessage`quando o usuário digita na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="c8ca9-192">A função `send` é chamada quando o usuário pressionar a tecla **Enter**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="c8ca9-193">`click`: esse evento é acionado quando o usuário clica no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="c8ca9-194">A função `send` é chamada.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="c8ca9-195">Configurar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c8ca9-195">Configure the app</span></span>

1. <span data-ttu-id="c8ca9-196">Em `Startup.Configure`, adicionar chamadas ao [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="c8ca9-197">O código anterior permite que o servidor localize e sirva o arquivo *index.html.*</span><span class="sxs-lookup"><span data-stu-id="c8ca9-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="c8ca9-198">O arquivo é servido se o usuário insere sua URL completa ou a URL raiz do aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="c8ca9-199">No final `Startup.Configure`do , mapeie `ChatHub` uma *rota /hub* para o hub.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="c8ca9-200">Substitua o código que exibe *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="c8ca9-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="c8ca9-201">pela seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="c8ca9-202">In `Startup.ConfigureServices`, chamar [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="c8ca9-203">Crie um novo diretório chamado *Hubs* no projeto root *SignalRWebPack/* para armazenar o hub SignalR.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="c8ca9-204">Crie o hub *Hubs/ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="c8ca9-205">Adicione a `using` seguinte declaração na parte superior `ChatHub` do arquivo *Startup.cs* para resolver a referência:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="c8ca9-206">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="c8ca9-206">Enable client and server communication</span></span>

<span data-ttu-id="c8ca9-207">Atualmente, o aplicativo exibe um formulário básico para enviar mensagens, mas ainda não está funcional.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="c8ca9-208">O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="c8ca9-209">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="c8ca9-210">O comando anterior instala:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-210">The preceding command installs:</span></span>

     * <span data-ttu-id="c8ca9-211">O [cliente SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), que permite que o cliente envie mensagens para o servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="c8ca9-212">As definições do tipo TypeScript para Node.js, que permite a verificação de tempo de compilação dos tipos Node.js.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="c8ca9-213">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="c8ca9-214">O código precedente é compatível com o recebimento de mensagens do servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="c8ca9-215">A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="c8ca9-216">A função `withUrl` configura a URL do hub.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="c8ca9-217">O SignalR habilita a troca de mensagens entre um cliente e um servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="c8ca9-218">Cada mensagem tem um nome específico.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-218">Each message has a specific name.</span></span> <span data-ttu-id="c8ca9-219">Por exemplo, mensagens `messageReceived` com o nome podem executar a lógica responsável por exibir a nova mensagem na região de mensagens.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="c8ca9-220">É possível escutar uma mensagem específica por meio da função `on`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="c8ca9-221">Qualquer número de nomes de mensagens pode ser ouvido.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="c8ca9-222">Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="c8ca9-223">Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="c8ca9-224">Ele é adicionado ao elemento principal `div` que exibe as mensagens.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="c8ca9-225">Agora que o cliente pode receber mensagens, configure-o para enviá-las.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="c8ca9-226">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="c8ca9-227">Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="c8ca9-228">O primeiro parâmetro do método é o nome da mensagem.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="c8ca9-229">Os dados da mensagem residem nos outros parâmetros.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="c8ca9-230">Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="c8ca9-231">A mensagem é composta do nome de usuário e da entrada em uma caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="c8ca9-232">Se o envio for bem-sucedido, o valor da caixa de texto será limpo.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="c8ca9-233">Adicione o método `NewMessage` à classe `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="c8ca9-234">O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="c8ca9-235">Não é necessário ter um método genérico `on` para receber todas as mensagens.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="c8ca9-236">Um método nomeado com o nome da mensagem é suficiente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="c8ca9-237">Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="c8ca9-238">O método `NewMessage` de C# espera os dados enviados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="c8ca9-239">Uma chamada é feita para [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="c8ca9-240">As mensagens recebidas são enviadas a todos os clientes conectados ao hub.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="c8ca9-241">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c8ca9-241">Test the app</span></span>

<span data-ttu-id="c8ca9-242">Confirme que o aplicativo funciona com as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8ca9-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8ca9-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c8ca9-244">Execute o Webpack no modo de *versão*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="c8ca9-245">Usando a janela **Console do Gerenciador de pacotes,** execute o seguinte comando na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="c8ca9-246">Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c8ca9-247">Selecione **Depurar** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="c8ca9-248">O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="c8ca9-249">Se você tiver erros de compilação, tente fechar e reabrir a solução.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="c8ca9-250">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="c8ca9-251">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c8ca9-252">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c8ca9-253">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8ca9-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c8ca9-255">Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c8ca9-256">Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="c8ca9-257">O servidor Web inicia o aplicativo e o disponibiliza no localhost.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="c8ca9-258">Abra um navegador em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="c8ca9-259">O arquivo *wwwroot/index.html* é fornecido.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="c8ca9-260">Copie a URL da barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="c8ca9-261">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="c8ca9-262">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c8ca9-263">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c8ca9-264">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c8ca9-266">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c8ca9-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8ca9-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8ca9-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8ca9-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **carga de trabalho de ASP.NET e desenvolvimento web**</span><span class="sxs-lookup"><span data-stu-id="c8ca9-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c8ca9-269">.NET Core SDK 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c8ca9-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c8ca9-270">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c8ca9-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8ca9-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c8ca9-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c8ca9-273">.NET Core SDK 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c8ca9-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c8ca9-274">C# para Visual Studio Code versão 1.17.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c8ca9-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="c8ca9-275">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c8ca9-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="c8ca9-276">Criar o aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8ca9-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8ca9-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8ca9-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c8ca9-278">Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="c8ca9-279">Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="c8ca9-280">Siga estas instruções no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="c8ca9-281">Navegue para **ferramentas** > **projetos de opções** > **e soluções** > **Web Package Management** > **External Web Tools**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="c8ca9-282">Selecione a entrada *$(PATH)* na lista.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="c8ca9-283">Clique na seta para cima para mover a entrada para a segunda posição da lista.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="c8ca9-285">A configuração do Visual Studio foi concluída.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="c8ca9-286">É hora de criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-286">It's time to create the project.</span></span>

1. <span data-ttu-id="c8ca9-287">Use a opção do menu **Arquivo** > **Novo** > **Projeto** e escolha o modelo **Aplicativo Web do ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="c8ca9-288">Nomeie o projeto *SignalRWebPack*e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="c8ca9-289">Selecione *.NET Core* no menu suspenso da estrutura de destino e selecione *ASP.NET Core 2.2* no menu suspenso do seletor de estrutura.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="c8ca9-290">Selecione o **modelo Vazio** e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8ca9-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c8ca9-292">Execute o comando a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="c8ca9-293">Um aplicativo Web ASP.NET Core vazio, direcionado ao .NET Core, será criado em um diretório *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="c8ca9-294">Configurar Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="c8ca9-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="c8ca9-295">As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="c8ca9-296">Execute o seguinte comando na raiz do projeto para criar um arquivo *package.json:*</span><span class="sxs-lookup"><span data-stu-id="c8ca9-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="c8ca9-297">Adicione a propriedade destacada ao arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="c8ca9-298">Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="c8ca9-299">Instalar os pacotes de npm exigidos.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-299">Install the required npm packages.</span></span> <span data-ttu-id="c8ca9-300">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="c8ca9-301">Alguns detalhes de comando a se observar:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-301">Some command details to note:</span></span>

    * <span data-ttu-id="c8ca9-302">Um número de versão segue o sinal `@` para cada nome de pacote.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="c8ca9-303">O npm instala essas versões específicas do pacote.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="c8ca9-304">A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="c8ca9-305">Por exemplo, `"webpack": "4.29.3"` é usado em vez de `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="c8ca9-306">Essa opção evita atualizações não intencionais para versões de pacote mais recentes.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="c8ca9-307">Consulte os docs [npm-install](https://docs.npmjs.com/cli/install) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="c8ca9-308">Substitua `scripts` a propriedade do arquivo *package.json* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="c8ca9-309">Uma explicação sobre os scripts:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="c8ca9-310">`build`: Agrupa os recursos do lado do cliente no modo de desenvolvimento e observa para alterações de arquivos.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="c8ca9-311">O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="c8ca9-312">A opção `mode` desabilita otimizações de produção, como tree shaking e minificação.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="c8ca9-313">Use somente `build` no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="c8ca9-314">`release`: Agrupa os recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="c8ca9-315">`publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="c8ca9-316">Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="c8ca9-317">Crie um arquivo chamado *webpack.config.js* na raiz do projeto, com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="c8ca9-318">O arquivo precedente configura a compilação Webpack.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="c8ca9-319">Detalhes de configuração a serem notados:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="c8ca9-320">A propriedade `output` substitui o valor padrão do *dist*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="c8ca9-321">Em vez disso, o lote é emitido no diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="c8ca9-322">A matriz `resolve.extensions` inclui *.js* para importar o JavaScript do cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="c8ca9-323">Crie um novo diretório *src* na raiz do projeto para armazenar os ativos do lado do cliente do projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="c8ca9-324">Crie *src/index.html* com a marcação seguinte.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="c8ca9-325">A HTML precedente define a marcação clichê da página inicial.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="c8ca9-326">Crie um novo diretório *src/css*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="c8ca9-327">Seu propósito é armazenar os arquivos do projeto *.css*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="c8ca9-328">Crie *src/css/main.css* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="c8ca9-329">O arquivo precedente *main.css* define o estilo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="c8ca9-330">Crie *src/tsconfig.json* com o seguinte JSON:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="c8ca9-331">O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="c8ca9-332">Crie *src/index.ts* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="c8ca9-333">O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="c8ca9-334">`keyup`: Este evento é acionado `tbMessage` quando o usuário digita na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="c8ca9-335">A função `send` é chamada quando o usuário pressionar a tecla **Enter**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="c8ca9-336">`click`: esse evento é acionado quando o usuário clica no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="c8ca9-337">A função `send` é chamada.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="c8ca9-338">Configurar o aplicativo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8ca9-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="c8ca9-339">O código fornecido no método `Startup.Configure` exibe *Olá, Mundo*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="c8ca9-340">Substitua a chamada para o método `app.Run` com chamadas para [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="c8ca9-341">O código precedente permite que o servidor localize e forneça o arquivo *index.html*, se o usuário inserir a URL completa ou a URL raiz do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="c8ca9-342">Chamada [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c8ca9-343">Ele adiciona os serviços SignalR ao projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="c8ca9-344">Mapeie uma rota */hub* para o hub `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="c8ca9-345">Adicione as seguintes linhas `Startup.Configure`no final de:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="c8ca9-346">Crie um novo diretório, chamado *Hubs*, na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="c8ca9-347">A finalidade é armazenar o hub SignalR, que é criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="c8ca9-348">Crie o hub *Hubs/ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="c8ca9-349">Adicione o código a seguir ao topo do arquivo *Startup.cs* para resolver a referência `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="c8ca9-350">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="c8ca9-350">Enable client and server communication</span></span>

<span data-ttu-id="c8ca9-351">Atualmente, o aplicativo exibe um formulário simples para enviar mensagens.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="c8ca9-352">Nada acontece quando você tenta fazer alguma coisa.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="c8ca9-353">O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="c8ca9-354">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="c8ca9-355">O comando precedente instala o [cliente TypeScript do SignalR](https://www.npmjs.com/package/@microsoft/signalr), que permite ao cliente enviar mensagens para o servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="c8ca9-356">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="c8ca9-357">O código precedente é compatível com o recebimento de mensagens do servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="c8ca9-358">A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="c8ca9-359">A função `withUrl` configura a URL do hub.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="c8ca9-360">O SignalR habilita a troca de mensagens entre um cliente e um servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="c8ca9-361">Cada mensagem tem um nome específico.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-361">Each message has a specific name.</span></span> <span data-ttu-id="c8ca9-362">Por exemplo, mensagens `messageReceived` com o nome podem executar a lógica responsável por exibir a nova mensagem na região de mensagens.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="c8ca9-363">É possível escutar uma mensagem específica por meio da função `on`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="c8ca9-364">Você pode escutar qualquer número de nomes de mensagem.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-364">You can listen to any number of message names.</span></span> <span data-ttu-id="c8ca9-365">Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="c8ca9-366">Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="c8ca9-367">A nova mensagem é `div` adicionada ao elemento principal que exibe as mensagens.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="c8ca9-368">Agora que o cliente pode receber mensagens, configure-o para enviá-las.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="c8ca9-369">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="c8ca9-370">Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="c8ca9-371">O primeiro parâmetro do método é o nome da mensagem.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="c8ca9-372">Os dados da mensagem residem nos outros parâmetros.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="c8ca9-373">Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="c8ca9-374">A mensagem é composta do nome de usuário e da entrada em uma caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="c8ca9-375">Se o envio for bem-sucedido, o valor da caixa de texto será limpo.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="c8ca9-376">Adicione o método `NewMessage` à classe `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="c8ca9-377">O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="c8ca9-378">Não é necessário ter um método genérico `on` para receber todas as mensagens.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="c8ca9-379">Um método nomeado com o nome da mensagem é suficiente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="c8ca9-380">Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="c8ca9-381">O método `NewMessage` de C# espera os dados enviados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="c8ca9-382">Uma chamada é feita para [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="c8ca9-383">As mensagens recebidas são enviadas a todos os clientes conectados ao hub.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="c8ca9-384">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c8ca9-384">Test the app</span></span>

<span data-ttu-id="c8ca9-385">Confirme que o aplicativo funciona com as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8ca9-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8ca9-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c8ca9-387">Execute o Webpack no modo de *versão*.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="c8ca9-388">Usando a janela **Console do Gerenciador de pacotes,** execute o seguinte comando na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="c8ca9-389">Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c8ca9-390">Selecione **Depurar** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="c8ca9-391">O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="c8ca9-392">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="c8ca9-393">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c8ca9-394">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c8ca9-395">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8ca9-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8ca9-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c8ca9-397">Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c8ca9-398">Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="c8ca9-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="c8ca9-399">O servidor Web inicia o aplicativo e o disponibiliza no localhost.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="c8ca9-400">Abra um navegador em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="c8ca9-401">O arquivo *wwwroot/index.html* é fornecido.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="c8ca9-402">Copie a URL da barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="c8ca9-403">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="c8ca9-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="c8ca9-404">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c8ca9-405">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c8ca9-406">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="c8ca9-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c8ca9-408">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c8ca9-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
