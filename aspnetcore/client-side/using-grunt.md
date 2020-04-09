---
title: Use grunhido em ASP.NET Core
author: rick-anderson
description: Use grunhido em ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657587"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="287d4-103">Use grunhido em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="287d4-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="287d4-104">Grunt é um corredor de tarefas JavaScript que automatiza a minificação de script, compilação TypeScript, ferramentas de "fiapos" de qualidade de código, pré-processadores CSS e qualquer tarefa repetitiva que precisa ser feito para dar suporte ao desenvolvimento do cliente.</span><span class="sxs-lookup"><span data-stu-id="287d4-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="287d4-105">O Grunt é totalmente suportado no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="287d4-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="287d4-106">Este exemplo usa um projeto vazio ASP.NET Core como ponto de partida, para mostrar como automatizar o processo de construção do cliente do zero.</span><span class="sxs-lookup"><span data-stu-id="287d4-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="287d4-107">O exemplo acabado limpa o diretório de implantação de destino, combina arquivos JavaScript, verifica a qualidade do código, condensa o conteúdo do arquivo JavaScript e implanta na raiz do seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="287d4-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="287d4-108">Usaremos os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="287d4-108">We will use the following packages:</span></span>

* <span data-ttu-id="287d4-109">**grunhido**: O pacote de corredor de tarefas grunt.</span><span class="sxs-lookup"><span data-stu-id="287d4-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="287d4-110">**grunhido-contrib-clean**: Um plugin que remove arquivos ou diretórios.</span><span class="sxs-lookup"><span data-stu-id="287d4-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="287d4-111">**grunhido-contrib-jshint**: Um plugin que analisa a qualidade do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="287d4-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="287d4-112">**grunhido-contrib-concat**: Um plugin que une arquivos em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="287d4-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="287d4-113">**grunhido-contrib-uglify**: Um plugin que minisfaz JavaScript para reduzir o tamanho.</span><span class="sxs-lookup"><span data-stu-id="287d4-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="287d4-114">**grunhido-contrib-watch**: Um plugin que observa a atividade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="287d4-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="287d4-115">Preparando a aplicação</span><span class="sxs-lookup"><span data-stu-id="287d4-115">Preparing the application</span></span>

<span data-ttu-id="287d4-116">Para começar, configure um novo aplicativo web vazio e adicione arquivos de exemplo typeScript.</span><span class="sxs-lookup"><span data-stu-id="287d4-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="287d4-117">Os arquivos TypeScript são compilados automaticamente em JavaScript usando as configurações padrão do Visual Studio e serão nossa matéria-prima para processar usando o Grunt.</span><span class="sxs-lookup"><span data-stu-id="287d4-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="287d4-118">No Visual Studio, `ASP.NET Web Application`crie um novo .</span><span class="sxs-lookup"><span data-stu-id="287d4-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="287d4-119">Na caixa de diálogo **Projeto novo ASP.NET,** selecione o modelo ASP.NET Core **Empty** e clique no botão OK.</span><span class="sxs-lookup"><span data-stu-id="287d4-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="287d4-120">No Solution Explorer, revise a estrutura do projeto.</span><span class="sxs-lookup"><span data-stu-id="287d4-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="287d4-121">A `\src` pasta inclui `wwwroot` `Dependencies` nós vazios e vazios.</span><span class="sxs-lookup"><span data-stu-id="287d4-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solução web vazia](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="287d4-123">Adicione uma nova `TypeScript` pasta nomeada ao diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="287d4-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="287d4-124">Antes de adicionar quaisquer arquivos, certifique-se de que o Visual Studio tenha a opção 'compilar em salvar' para arquivos TypeScript verificados.</span><span class="sxs-lookup"><span data-stu-id="287d4-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="287d4-125">Navegue até **ferramentas** > **opções Projeto** > **de escrita do editor** > **de** > **texto:**</span><span class="sxs-lookup"><span data-stu-id="287d4-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![opções de configuração de compilação automática de arquivos TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="287d4-127">Clique com `TypeScript` o botão direito do mouse no diretório e **selecione Adicionar > Novo Item** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="287d4-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="287d4-128">Selecione o item **de arquivo JavaScript** e nomeie o arquivo *Tastes.ts* (observe a \*extensão .ts).</span><span class="sxs-lookup"><span data-stu-id="287d4-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="287d4-129">Copie a linha de código TypeScript abaixo no arquivo (quando você salvar, um novo arquivo *Tastes.js* aparecerá com a fonte JavaScript).</span><span class="sxs-lookup"><span data-stu-id="287d4-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="287d4-130">Adicione um segundo arquivo ao diretório **TypeScript** e nomeie-o `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="287d4-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="287d4-131">Copie o código abaixo no arquivo.</span><span class="sxs-lookup"><span data-stu-id="287d4-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="287d4-132">Configuração do NPM</span><span class="sxs-lookup"><span data-stu-id="287d4-132">Configuring NPM</span></span>

<span data-ttu-id="287d4-133">Em seguida, configure o NPM para baixar grunhidos e grunhidos.</span><span class="sxs-lookup"><span data-stu-id="287d4-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="287d4-134">No Solution Explorer, clique com o botão direito do mouse no projeto e selecione **Adicionar > Novo Item** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="287d4-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="287d4-135">Selecione o item de **arquivo de configuração npm,** deixe o nome padrão, *package.json*e clique no botão **Adicionar.**</span><span class="sxs-lookup"><span data-stu-id="287d4-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="287d4-136">No arquivo *package.json,* `devDependencies` dentro do aparelho, digite "grunhido".</span><span class="sxs-lookup"><span data-stu-id="287d4-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="287d4-137">Selecione `grunt` na lista Intellisense e pressione a tecla Enter.</span><span class="sxs-lookup"><span data-stu-id="287d4-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="287d4-138">O Visual Studio citará o nome do pacote grunhido e adicionará um cólon.</span><span class="sxs-lookup"><span data-stu-id="287d4-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="287d4-139">À direita do cólon, selecione a versão estável mais recente do pacote `Ctrl-Space` no topo da lista Intellisense (pressione se o Intellisense não aparecer).</span><span class="sxs-lookup"><span data-stu-id="287d4-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunhido Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="287d4-141">O NPM usa [versões semânticas](https://semver.org/) para organizar dependências.</span><span class="sxs-lookup"><span data-stu-id="287d4-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="287d4-142">A versão semântica, também conhecida como SemVer, \<identifica pacotes com o esquema de numeração principal>. \<> menores. \<remendar>.</span><span class="sxs-lookup"><span data-stu-id="287d4-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="287d4-143">O Intellisense simplifica a versão semântica mostrando apenas algumas escolhas comuns.</span><span class="sxs-lookup"><span data-stu-id="287d4-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="287d4-144">O item principal da lista Intellisense (0,4,5 no exemplo acima) é considerado a versão estável mais recente do pacote.</span><span class="sxs-lookup"><span data-stu-id="287d4-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="287d4-145">O símbolo caret (^) corresponde à versão principal mais recente e o tilde (~) corresponde à versão menor mais recente.</span><span class="sxs-lookup"><span data-stu-id="287d4-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="287d4-146">Consulte a referência do [analisador de versão semver do NPM](https://www.npmjs.com/package/semver) como um guia para a expressividade completa que o SemVer fornece.</span><span class="sxs-lookup"><span data-stu-id="287d4-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="287d4-147">Adicione mais dependências para carregar\* pacotes grunhidos-contrib para *limpar,* *jshint,* *concat,* *uglify,* e *assistir* como mostrado no exemplo abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="287d4-148">As versões não precisam combinar com o exemplo.</span><span class="sxs-lookup"><span data-stu-id="287d4-148">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="287d4-149">Salve o arquivo *package.json.*</span><span class="sxs-lookup"><span data-stu-id="287d4-149">Save the *package.json* file.</span></span>

<span data-ttu-id="287d4-150">Os pacotes `devDependencies` para cada item serão baixados, juntamente com todos os arquivos que cada pacote requer.</span><span class="sxs-lookup"><span data-stu-id="287d4-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="287d4-151">Você pode encontrar os arquivos do pacote no diretório *node_modules* ativando o botão **Mostrar todos os arquivos** no Solution **Explorer**.</span><span class="sxs-lookup"><span data-stu-id="287d4-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunhido node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="287d4-153">Se você precisar, você pode restaurar manualmente as dependências `Dependencies\NPM` do Solution **Explorer** clicando com o botão direito do mouse e selecionando a opção Menu Restaurar **pacotes.**</span><span class="sxs-lookup"><span data-stu-id="287d4-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![restaurar pacotes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="287d4-155">Configurando grunhido</span><span class="sxs-lookup"><span data-stu-id="287d4-155">Configuring Grunt</span></span>

<span data-ttu-id="287d4-156">O Grunt é configurado usando um manifesto chamado *Gruntfile.js* que define, carrega e registra tarefas que podem ser executadas manualmente ou configuradas para serem executadas automaticamente com base em eventos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="287d4-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="287d4-157">Clique com o botão direito do mouse no projeto e **selecione Adicionar** > **novo item**.</span><span class="sxs-lookup"><span data-stu-id="287d4-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="287d4-158">Selecione o modelo de item **arquivo JavaScript,** altere o nome para *Gruntfile.js*e clique no botão **Adicionar.**</span><span class="sxs-lookup"><span data-stu-id="287d4-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="287d4-159">Adicione o seguinte código ao *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="287d4-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="287d4-160">A `initConfig` função define opções para cada pacote e o restante das cargas do módulo e as tarefas de registro.</span><span class="sxs-lookup"><span data-stu-id="287d4-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="287d4-161">Dentro `initConfig` da função, adicione `clean` opções para a tarefa como mostrado no exemplo *Gruntfile.js* abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="287d4-162">A `clean` tarefa aceita uma matriz de strings de diretório.</span><span class="sxs-lookup"><span data-stu-id="287d4-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="287d4-163">Esta tarefa remove arquivos de *wwwroot/lib* e remove todo o diretório */temp.*</span><span class="sxs-lookup"><span data-stu-id="287d4-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="287d4-164">Abaixo `initConfig` da função, adicione `grunt.loadNpmTasks`uma chamada a .</span><span class="sxs-lookup"><span data-stu-id="287d4-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="287d4-165">Isso tornará a tarefa executada no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="287d4-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="287d4-166">Salvar *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="287d4-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="287d4-167">O arquivo deve se parecer com a captura de tela abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-167">The file should look something like the screenshot below.</span></span>

    ![grunhido inicial](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="287d4-169">Clique com o botão direito do mouse *Gruntfile.js* e selecione **Task Runner Explorer** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="287d4-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="287d4-170">A janela **Task Runner Explorer** será aberta.</span><span class="sxs-lookup"><span data-stu-id="287d4-170">The **Task Runner Explorer** window will open.</span></span>

    ![menu explorador corredor tarefa](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="287d4-172">Verifique `clean` se as apresentações em **Tarefas** no **Explorador de Corredores de Tarefas**.</span><span class="sxs-lookup"><span data-stu-id="287d4-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![lista de tarefas explorador corredor](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="287d4-174">Clique com o botão direito do mouse na tarefa limpa e **selecione Executar** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="287d4-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="287d4-175">Uma janela de comando exibe o progresso da tarefa.</span><span class="sxs-lookup"><span data-stu-id="287d4-175">A command window displays progress of the task.</span></span>

    ![explorador corredor tarefa executar tarefa limpa](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="287d4-177">Ainda não há arquivos ou diretórios para limpar.</span><span class="sxs-lookup"><span data-stu-id="287d4-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="287d4-178">Se você quiser, você pode criá-los manualmente no Solution Explorer e, em seguida, executar a tarefa limpa como um teste.</span><span class="sxs-lookup"><span data-stu-id="287d4-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="287d4-179">Na `initConfig` função, adicione uma `concat` entrada para usar o código abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="287d4-180">O `src` array de propriedade lista arquivos para combinar, na ordem de que eles devem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="287d4-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="287d4-181">A `dest` propriedade atribui o caminho para o arquivo combinado que é produzido.</span><span class="sxs-lookup"><span data-stu-id="287d4-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="287d4-182">A `all` propriedade no código acima é o nome de um alvo.</span><span class="sxs-lookup"><span data-stu-id="287d4-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="287d4-183">Os alvos são usados em algumas tarefas do Grunt para permitir vários ambientes de construção.</span><span class="sxs-lookup"><span data-stu-id="287d4-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="287d4-184">Você pode visualizar os alvos incorporados usando o IntelliSense ou atribuir o seu próprio.</span><span class="sxs-lookup"><span data-stu-id="287d4-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="287d4-185">Adicione `jshint` a tarefa usando o código abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="287d4-186">O utilitário `code-quality` jshint é executado contra todos os arquivos JavaScript encontrados no diretório *temporário.*</span><span class="sxs-lookup"><span data-stu-id="287d4-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="287d4-187">A opção "-W069" é um erro produzido pela jshint quando javaScript usa sintaxe de suporte `Tastes["Sweet"]` para `Tastes.Sweet`atribuir uma propriedade em vez de notação de pontos, ou seja, em vez de .</span><span class="sxs-lookup"><span data-stu-id="287d4-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="287d4-188">A opção desliga o aviso para permitir que o resto do processo continue.</span><span class="sxs-lookup"><span data-stu-id="287d4-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="287d4-189">Adicione `uglify` a tarefa usando o código abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="287d4-190">A tarefa minisfaz o arquivo *combined.js* encontrado no diretório temporário e cria o arquivo de resultado em wwwroot/lib seguindo o nome \* \<\>\* padrão do arquivo de convenção de nomeação .min.js .</span><span class="sxs-lookup"><span data-stu-id="287d4-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="287d4-191">Sob a `grunt.loadNpmTasks` chamada para `grunt-contrib-clean`essas cargas, inclua a mesma chamada para jshint, concat e uglify usando o código abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="287d4-192">Salvar *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="287d4-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="287d4-193">O arquivo deve se parecer com o exemplo abaixo.</span><span class="sxs-lookup"><span data-stu-id="287d4-193">The file should look something like the example below.</span></span>

    ![exemplo completo de arquivo grunhido](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="287d4-195">Observe que a lista Task `clean`Runner `concat` `jshint` **Explorer** Tasks inclui , e `uglify` tarefas.</span><span class="sxs-lookup"><span data-stu-id="287d4-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="287d4-196">Execute cada tarefa em ordem e observe os resultados no **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="287d4-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="287d4-197">Cada tarefa deve ser executada sem erros.</span><span class="sxs-lookup"><span data-stu-id="287d4-197">Each task should run without errors.</span></span>

    ![explorador corredor de tarefas executar cada tarefa](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="287d4-199">A tarefa concat cria um novo arquivo *combined.js* e coloca-o no diretório temporário.</span><span class="sxs-lookup"><span data-stu-id="287d4-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="287d4-200">A `jshint` tarefa simplesmente funciona e não produz saída.</span><span class="sxs-lookup"><span data-stu-id="287d4-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="287d4-201">A `uglify` tarefa cria um novo arquivo *combinado.min.js* e o coloca em *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="287d4-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="287d4-202">Após a conclusão, a solução deve se parecer com a captura de tela abaixo:</span><span class="sxs-lookup"><span data-stu-id="287d4-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![explorador de soluções depois de todas as tarefas](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="287d4-204">Para obter mais informações sobre as [https://www.npmjs.com/](https://www.npmjs.com/) opções de cada pacote, visite e procure o nome do pacote na caixa de pesquisa na página principal.</span><span class="sxs-lookup"><span data-stu-id="287d4-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="287d4-205">Por exemplo, você pode procurar o pacote grunhido-contrib-clean para obter um link de documentação que explica todos os seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="287d4-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="287d4-206">Todos juntos agora</span><span class="sxs-lookup"><span data-stu-id="287d4-206">All together now</span></span>

<span data-ttu-id="287d4-207">Use o `registerTask()` método Grunt para executar uma série de tarefas em uma seqüência específica.</span><span class="sxs-lookup"><span data-stu-id="287d4-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="287d4-208">Por exemplo, para executar as etapas de exemplo acima na ordem clean -> concat -> jshint -> uglify, adicione o código abaixo ao módulo.</span><span class="sxs-lookup"><span data-stu-id="287d4-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="287d4-209">O código deve ser adicionado ao mesmo nível das chamadas loadNpmTasks(), fora initConfig.</span><span class="sxs-lookup"><span data-stu-id="287d4-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="287d4-210">A nova tarefa aparece no Task Runner Explorer em Alias Tasks.</span><span class="sxs-lookup"><span data-stu-id="287d4-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="287d4-211">Você pode clicar com o botão direito do mouse e executá-lo como faria com outras tarefas.</span><span class="sxs-lookup"><span data-stu-id="287d4-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="287d4-212">A `all` tarefa `clean`será `concat` `jshint` executada, e `uglify`, em ordem.</span><span class="sxs-lookup"><span data-stu-id="287d4-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![codinome tarefas grunhidas](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="287d4-214">Monitorando alterações</span><span class="sxs-lookup"><span data-stu-id="287d4-214">Watching for changes</span></span>

<span data-ttu-id="287d4-215">Uma `watch` tarefa mantém um olho em arquivos e diretórios.</span><span class="sxs-lookup"><span data-stu-id="287d4-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="287d4-216">O relógio aciona tarefas automaticamente se detectar alterações.</span><span class="sxs-lookup"><span data-stu-id="287d4-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="287d4-217">Adicione o código abaixo ao initConfig \*para observar alterações em arquivos .js no diretório TypeScript.</span><span class="sxs-lookup"><span data-stu-id="287d4-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="287d4-218">Se um arquivo JavaScript `watch` for `all` alterado, executará a tarefa.</span><span class="sxs-lookup"><span data-stu-id="287d4-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="287d4-219">Adicione uma `loadNpmTasks()` chamada para `watch` mostrar a tarefa no Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="287d4-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="287d4-220">Clique com o botão direito do mouse na tarefa do relógio no Task Runner Explorer e selecione Executar no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="287d4-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="287d4-221">A janela de comando que mostra a tarefa do relógio em execução exibirá um "Waiting..." Mensagem.</span><span class="sxs-lookup"><span data-stu-id="287d4-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="287d4-222">Abra um dos arquivos TypeScript, adicione um espaço e salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="287d4-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="287d4-223">Isso acionará a tarefa do relógio e acionará as outras tarefas a serem executadas em ordem.</span><span class="sxs-lookup"><span data-stu-id="287d4-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="287d4-224">A captura de tela abaixo mostra uma amostra executada.</span><span class="sxs-lookup"><span data-stu-id="287d4-224">The screenshot below shows a sample run.</span></span>

![executando a saída de tarefas](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="287d4-226">Vinculação aos eventos do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="287d4-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="287d4-227">A menos que você queira iniciar manualmente suas tarefas toda vez que trabalhar no Visual Studio, vincule tarefas a eventos **antes de construir,** **depois de construir,** **limpar**e projetar **eventos abertos.**</span><span class="sxs-lookup"><span data-stu-id="287d4-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="287d4-228">Ligue `watch` para que seja executado toda vez que o Visual Studio abrir.</span><span class="sxs-lookup"><span data-stu-id="287d4-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="287d4-229">No Task Runner Explorer, clique com o botão direito do mouse na tarefa do relógio e selecione >  **''Projeto ''Abrir',** a partir do menu de contexto.**Project Open**</span><span class="sxs-lookup"><span data-stu-id="287d4-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![vincular uma tarefa à abertura do projeto](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="287d4-231">Descarregar e recarregar o projeto.</span><span class="sxs-lookup"><span data-stu-id="287d4-231">Unload and reload the project.</span></span> <span data-ttu-id="287d4-232">Quando o projeto é carregado novamente, a tarefa do relógio começa a ser reiniciada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="287d4-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="287d4-233">Resumo</span><span class="sxs-lookup"><span data-stu-id="287d4-233">Summary</span></span>

<span data-ttu-id="287d4-234">O Grunt é um poderoso corredor de tarefas que pode ser usado para automatizar a maioria das tarefas de compilação do cliente.</span><span class="sxs-lookup"><span data-stu-id="287d4-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="287d4-235">O Grunr aproveita o NPM para entregar seus pacotes e apresenta a integração de ferramentas com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="287d4-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="287d4-236">O Supervisor Runner Explorer do Visual Studio detecta alterações nos arquivos de configuração e fornece uma interface conveniente para executar tarefas, visualizar tarefas em execução e vincular tarefas a eventos do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="287d4-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
