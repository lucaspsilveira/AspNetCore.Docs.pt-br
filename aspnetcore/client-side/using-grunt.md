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
# <a name="use-grunt-in-aspnet-core"></a>Use grunhido em ASP.NET Core

Grunt é um corredor de tarefas JavaScript que automatiza a minificação de script, compilação TypeScript, ferramentas de "fiapos" de qualidade de código, pré-processadores CSS e qualquer tarefa repetitiva que precisa ser feito para dar suporte ao desenvolvimento do cliente. O Grunt é totalmente suportado no Visual Studio.

Este exemplo usa um projeto vazio ASP.NET Core como ponto de partida, para mostrar como automatizar o processo de construção do cliente do zero.

O exemplo acabado limpa o diretório de implantação de destino, combina arquivos JavaScript, verifica a qualidade do código, condensa o conteúdo do arquivo JavaScript e implanta na raiz do seu aplicativo web. Usaremos os seguintes pacotes:

* **grunhido**: O pacote de corredor de tarefas grunt.

* **grunhido-contrib-clean**: Um plugin que remove arquivos ou diretórios.

* **grunhido-contrib-jshint**: Um plugin que analisa a qualidade do código JavaScript.

* **grunhido-contrib-concat**: Um plugin que une arquivos em um único arquivo.

* **grunhido-contrib-uglify**: Um plugin que minisfaz JavaScript para reduzir o tamanho.

* **grunhido-contrib-watch**: Um plugin que observa a atividade do arquivo.

## <a name="preparing-the-application"></a>Preparando a aplicação

Para começar, configure um novo aplicativo web vazio e adicione arquivos de exemplo typeScript. Os arquivos TypeScript são compilados automaticamente em JavaScript usando as configurações padrão do Visual Studio e serão nossa matéria-prima para processar usando o Grunt.

1. No Visual Studio, `ASP.NET Web Application`crie um novo .

2. Na caixa de diálogo **Projeto novo ASP.NET,** selecione o modelo ASP.NET Core **Empty** e clique no botão OK.

3. No Solution Explorer, revise a estrutura do projeto. A `\src` pasta inclui `wwwroot` `Dependencies` nós vazios e vazios.

    ![solução web vazia](using-grunt/_static/grunt-solution-explorer.png)

4. Adicione uma nova `TypeScript` pasta nomeada ao diretório do projeto.

5. Antes de adicionar quaisquer arquivos, certifique-se de que o Visual Studio tenha a opção 'compilar em salvar' para arquivos TypeScript verificados. Navegue até **ferramentas** > **opções Projeto** > **de escrita do editor** > **de** > **texto:**

    ![opções de configuração de compilação automática de arquivos TypeScript](using-grunt/_static/typescript-options.png)

6. Clique com `TypeScript` o botão direito do mouse no diretório e **selecione Adicionar > Novo Item** no menu de contexto. Selecione o item **de arquivo JavaScript** e nomeie o arquivo *Tastes.ts* (observe a \*extensão .ts). Copie a linha de código TypeScript abaixo no arquivo (quando você salvar, um novo arquivo *Tastes.js* aparecerá com a fonte JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Adicione um segundo arquivo ao diretório **TypeScript** e nomeie-o `Food.ts`. Copie o código abaixo no arquivo.

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

## <a name="configuring-npm"></a>Configuração do NPM

Em seguida, configure o NPM para baixar grunhidos e grunhidos.

1. No Solution Explorer, clique com o botão direito do mouse no projeto e selecione **Adicionar > Novo Item** no menu de contexto. Selecione o item de **arquivo de configuração npm,** deixe o nome padrão, *package.json*e clique no botão **Adicionar.**

2. No arquivo *package.json,* `devDependencies` dentro do aparelho, digite "grunhido". Selecione `grunt` na lista Intellisense e pressione a tecla Enter. O Visual Studio citará o nome do pacote grunhido e adicionará um cólon. À direita do cólon, selecione a versão estável mais recente do pacote `Ctrl-Space` no topo da lista Intellisense (pressione se o Intellisense não aparecer).

    ![grunhido Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > O NPM usa [versões semânticas](https://semver.org/) para organizar dependências. A versão semântica, também conhecida como SemVer, \<identifica pacotes com o esquema de numeração principal>. \<> menores. \<remendar>. O Intellisense simplifica a versão semântica mostrando apenas algumas escolhas comuns. O item principal da lista Intellisense (0,4,5 no exemplo acima) é considerado a versão estável mais recente do pacote. O símbolo caret (^) corresponde à versão principal mais recente e o tilde (~) corresponde à versão menor mais recente. Consulte a referência do [analisador de versão semver do NPM](https://www.npmjs.com/package/semver) como um guia para a expressividade completa que o SemVer fornece.

3. Adicione mais dependências para carregar\* pacotes grunhidos-contrib para *limpar,* *jshint,* *concat,* *uglify,* e *assistir* como mostrado no exemplo abaixo. As versões não precisam combinar com o exemplo.

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

4. Salve o arquivo *package.json.*

Os pacotes `devDependencies` para cada item serão baixados, juntamente com todos os arquivos que cada pacote requer. Você pode encontrar os arquivos do pacote no diretório *node_modules* ativando o botão **Mostrar todos os arquivos** no Solution **Explorer**.

![grunhido node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Se você precisar, você pode restaurar manualmente as dependências `Dependencies\NPM` do Solution **Explorer** clicando com o botão direito do mouse e selecionando a opção Menu Restaurar **pacotes.**

![restaurar pacotes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configurando grunhido

O Grunt é configurado usando um manifesto chamado *Gruntfile.js* que define, carrega e registra tarefas que podem ser executadas manualmente ou configuradas para serem executadas automaticamente com base em eventos no Visual Studio.

1. Clique com o botão direito do mouse no projeto e **selecione Adicionar** > **novo item**. Selecione o modelo de item **arquivo JavaScript,** altere o nome para *Gruntfile.js*e clique no botão **Adicionar.**

1. Adicione o seguinte código ao *Gruntfile.js*. A `initConfig` função define opções para cada pacote e o restante das cargas do módulo e as tarefas de registro.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Dentro `initConfig` da função, adicione `clean` opções para a tarefa como mostrado no exemplo *Gruntfile.js* abaixo. A `clean` tarefa aceita uma matriz de strings de diretório. Esta tarefa remove arquivos de *wwwroot/lib* e remove todo o diretório */temp.*

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Abaixo `initConfig` da função, adicione `grunt.loadNpmTasks`uma chamada a . Isso tornará a tarefa executada no Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Salvar *Gruntfile.js*. O arquivo deve se parecer com a captura de tela abaixo.

    ![grunhido inicial](using-grunt/_static/gruntfile-js-initial.png)

1. Clique com o botão direito do mouse *Gruntfile.js* e selecione **Task Runner Explorer** no menu de contexto. A janela **Task Runner Explorer** será aberta.

    ![menu explorador corredor tarefa](using-grunt/_static/task-runner-explorer-menu.png)

1. Verifique `clean` se as apresentações em **Tarefas** no **Explorador de Corredores de Tarefas**.

    ![lista de tarefas explorador corredor](using-grunt/_static/task-runner-explorer-tasks.png)

1. Clique com o botão direito do mouse na tarefa limpa e **selecione Executar** no menu de contexto. Uma janela de comando exibe o progresso da tarefa.

    ![explorador corredor tarefa executar tarefa limpa](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Ainda não há arquivos ou diretórios para limpar. Se você quiser, você pode criá-los manualmente no Solution Explorer e, em seguida, executar a tarefa limpa como um teste.

1. Na `initConfig` função, adicione uma `concat` entrada para usar o código abaixo.

    O `src` array de propriedade lista arquivos para combinar, na ordem de que eles devem ser combinados. A `dest` propriedade atribui o caminho para o arquivo combinado que é produzido.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > A `all` propriedade no código acima é o nome de um alvo. Os alvos são usados em algumas tarefas do Grunt para permitir vários ambientes de construção. Você pode visualizar os alvos incorporados usando o IntelliSense ou atribuir o seu próprio.

1. Adicione `jshint` a tarefa usando o código abaixo.

    O utilitário `code-quality` jshint é executado contra todos os arquivos JavaScript encontrados no diretório *temporário.*

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > A opção "-W069" é um erro produzido pela jshint quando javaScript usa sintaxe de suporte `Tastes["Sweet"]` para `Tastes.Sweet`atribuir uma propriedade em vez de notação de pontos, ou seja, em vez de . A opção desliga o aviso para permitir que o resto do processo continue.

1. Adicione `uglify` a tarefa usando o código abaixo.

    A tarefa minisfaz o arquivo *combined.js* encontrado no diretório temporário e cria o arquivo de resultado em wwwroot/lib seguindo o nome * \<\>* padrão do arquivo de convenção de nomeação .min.js .

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Sob a `grunt.loadNpmTasks` chamada para `grunt-contrib-clean`essas cargas, inclua a mesma chamada para jshint, concat e uglify usando o código abaixo.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Salvar *Gruntfile.js*. O arquivo deve se parecer com o exemplo abaixo.

    ![exemplo completo de arquivo grunhido](using-grunt/_static/gruntfile-js-complete.png)

1. Observe que a lista Task `clean`Runner `concat` `jshint` **Explorer** Tasks inclui , e `uglify` tarefas. Execute cada tarefa em ordem e observe os resultados no **Solution Explorer**. Cada tarefa deve ser executada sem erros.

    ![explorador corredor de tarefas executar cada tarefa](using-grunt/_static/task-runner-explorer-run-each-task.png)

    A tarefa concat cria um novo arquivo *combined.js* e coloca-o no diretório temporário. A `jshint` tarefa simplesmente funciona e não produz saída. A `uglify` tarefa cria um novo arquivo *combinado.min.js* e o coloca em *wwwroot/lib*. Após a conclusão, a solução deve se parecer com a captura de tela abaixo:

    ![explorador de soluções depois de todas as tarefas](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Para obter mais informações sobre as [https://www.npmjs.com/](https://www.npmjs.com/) opções de cada pacote, visite e procure o nome do pacote na caixa de pesquisa na página principal. Por exemplo, você pode procurar o pacote grunhido-contrib-clean para obter um link de documentação que explica todos os seus parâmetros.

### <a name="all-together-now"></a>Todos juntos agora

Use o `registerTask()` método Grunt para executar uma série de tarefas em uma seqüência específica. Por exemplo, para executar as etapas de exemplo acima na ordem clean -> concat -> jshint -> uglify, adicione o código abaixo ao módulo. O código deve ser adicionado ao mesmo nível das chamadas loadNpmTasks(), fora initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

A nova tarefa aparece no Task Runner Explorer em Alias Tasks. Você pode clicar com o botão direito do mouse e executá-lo como faria com outras tarefas. A `all` tarefa `clean`será `concat` `jshint` executada, e `uglify`, em ordem.

![codinome tarefas grunhidas](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Monitorando alterações

Uma `watch` tarefa mantém um olho em arquivos e diretórios. O relógio aciona tarefas automaticamente se detectar alterações. Adicione o código abaixo ao initConfig \*para observar alterações em arquivos .js no diretório TypeScript. Se um arquivo JavaScript `watch` for `all` alterado, executará a tarefa.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Adicione uma `loadNpmTasks()` chamada para `watch` mostrar a tarefa no Task Runner Explorer.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Clique com o botão direito do mouse na tarefa do relógio no Task Runner Explorer e selecione Executar no menu de contexto. A janela de comando que mostra a tarefa do relógio em execução exibirá um "Waiting..." Mensagem. Abra um dos arquivos TypeScript, adicione um espaço e salve o arquivo. Isso acionará a tarefa do relógio e acionará as outras tarefas a serem executadas em ordem. A captura de tela abaixo mostra uma amostra executada.

![executando a saída de tarefas](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Vinculação aos eventos do Visual Studio

A menos que você queira iniciar manualmente suas tarefas toda vez que trabalhar no Visual Studio, vincule tarefas a eventos **antes de construir,** **depois de construir,** **limpar**e projetar **eventos abertos.**

Ligue `watch` para que seja executado toda vez que o Visual Studio abrir. No Task Runner Explorer, clique com o botão direito do mouse na tarefa do relógio e selecione >  **''Projeto ''Abrir',** a partir do menu de contexto.**Project Open**

![vincular uma tarefa à abertura do projeto](using-grunt/_static/bindings-project-open.png)

Descarregar e recarregar o projeto. Quando o projeto é carregado novamente, a tarefa do relógio começa a ser reiniciada automaticamente.

## <a name="summary"></a>Resumo

O Grunt é um poderoso corredor de tarefas que pode ser usado para automatizar a maioria das tarefas de compilação do cliente. O Grunr aproveita o NPM para entregar seus pacotes e apresenta a integração de ferramentas com o Visual Studio. O Supervisor Runner Explorer do Visual Studio detecta alterações nos arquivos de configuração e fornece uma interface conveniente para executar tarefas, visualizar tarefas em execução e vincular tarefas a eventos do Visual Studio.
