---
title: Integração e implantação contínua - DevOps com ASP.NET Core e Azure
author: CamSoper
description: Integração e implantação contínuas em DevOps com ASP.NET Core e Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655830"
---
# <a name="continuous-integration-and-deployment"></a>Integração e implantação contínuas

No capítulo anterior, você criou um repositório git local para o aplicativo Simple Feed Reader. Neste capítulo, você publicará esse código em um repositório do GitHub e construirá um pipeline azure DevOps Services usando o Azure Pipelines. O pipeline permite compilações e implantações contínuas do aplicativo. Qualquer compromisso com o repositório do GitHub desencadeia uma compilação e uma implantação no slot de preparação do Azure Web App.

Nesta seção, você completará as seguintes tarefas:

* Publique o código do aplicativo no GitHub
* Desconectar a implantação local do Git
* Criar uma organização do Azure DevOps
* Crie um projeto de equipe nos Serviços Azure DevOps
* Criar a definição de build
* Criar um pipeline de lançamento
* Confirmar alterações no GitHub e implantar automaticamente no Azure
* Examine o pipeline da Azure Pipelines

## <a name="publish-the-apps-code-to-github"></a>Publique o código do aplicativo no GitHub

1. Abra uma janela do `https://github.com`navegador e navegue para .
1. Clique **+** na queda no cabeçalho e selecione **Novo repositório**:

    ![Opção de Novo Repositório do GitHub](media/cicd/github-new-repo.png)

1. Selecione sua conta na **ativada do Proprietário** e digite o *leitor de feed simples* na caixa de texto nome do **Repositório.**
1. Clique no botão **Criar repositório.**
1. Abra o projétil de comando da máquina local. Navegue até o diretório no qual o repositório Git *de leitor de simples* alimentação é armazenado.
1. Renomeie o controle remoto de *origem* existente para *upstream*. Execute o comando a seguir:

    ```console
    git remote rename origin upstream
    ```

1. Adicione um novo controle remoto *de origem* apontando para sua cópia do repositório no GitHub. Execute o comando a seguir:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publique seu repositório git local para o repositório GitHub recém-criado. Execute o comando a seguir:

    ```console
    git push -u origin master
    ```

1. Abra uma janela do `https://github.com/<GitHub_username>/simple-feed-reader/`navegador e navegue para . Valide se seu código aparece no repositório do GitHub.

## <a name="disconnect-local-git-deployment"></a>Desconectar a implantação local do Git

Remova a implantação local do Git com as seguintes etapas. O Azure Pipelines (um serviço Azure DevOps) substitui e aumenta essa funcionalidade.

1. Abra o [portal Azure](https://portal.azure.com/)e navegue até o *\<webapp (mywebapp unique_number/staging)\>* Web App. O Web App pode ser rapidamente localizado inserindo a *encenação* na caixa de pesquisa do portal:

    ![encenando o termo de pesquisa do Web App](media/cicd/portal-search-box.png)

1. Clique **em Centro de implantação**. Um novo painel aparece. Clique **em Desconectar** para remover a configuração local de controle de origem do Git adicionada no capítulo anterior. Confirme a operação de remoção clicando no botão **Sim.**
1. Navegue até o *mywebapp<unique_number>* App Service. Como lembrete, a caixa de pesquisa do portal pode ser usada para localizar rapidamente o Serviço de Aplicativos.
1. Clique **em Centro de implantação**. Um novo painel aparece. Clique **em Desconectar** para remover a configuração local de controle de origem do Git adicionada no capítulo anterior. Confirme a operação de remoção clicando no botão **Sim.**

## <a name="create-an-azure-devops-organization"></a>Criar uma organização do Azure DevOps

1. Abra um navegador e navegue até a página de criação da [organização Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Digite um nome exclusivo na Caixa de texto **Escolha um nome memorável** para formar a URL para acessar sua organização Azure DevOps.
1. Selecione o botão de rádio **Git,** já que o código está hospedado em um repositório do GitHub.
1. Clique no botão **Continuar**. Depois de uma pequena espera, uma conta e um projeto de equipe, chamado *MyFirstProject,* são criados.

    ![Página de criação da organização Azure DevOps](media/cicd/vsts-account-creation.png)

1. Abra o e-mail de confirmação indicando que a organização e o projeto Azure DevOps estão prontos para uso. Clique no botão **Iniciar seu projeto:**

    ![Inicie o botão do projeto](media/cicd/vsts-start-project.png)

1. Um navegador é aberto para * \<\>account_name .visualstudio.com*. Clique no link *MyFirstProject* para começar a configurar o pipeline DevOps do projeto.

## <a name="configure-the-azure-pipelines-pipeline"></a>Configure o pipeline da Azure Pipelines

Há três passos distintos para completar. Completar as etapas nas três seções seguintes resulta em um pipeline de DevOps operacional.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Grant Azure DevOps acesso ao repositório GitHub

1. Expanda o **código ou a construção a partir de um acordeom de repositório externo.** Clique no botão **Configurar construir:**

    ![Botão de compilação de configuração](media/cicd/vsts-setup-build.png)

1. Selecione a opção **GitHub** na **seção Selecionar uma** fonte:

    ![Selecione uma fonte - GitHub](media/cicd/vsts-select-source.png)

1. A autorização é necessária antes que o Azure DevOps possa acessar seu repositório gitHub. Digite *<GitHub_username> conexão GitHub* na caixa de texto **nome conexão.** Por exemplo:

    ![Nome de conexão GitHub](media/cicd/vsts-repo-authz.png)

1. Se a autenticação de dois fatores estiver ativada em sua conta do GitHub, um token de acesso pessoal será necessário. Nesse caso, clique no Autorizar com um link **de token de acesso pessoal do GitHub.** Consulte as [instruções oficiais de criação de tokens de acesso pessoal do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obter ajuda. Apenas o escopo de *relo* de permissões é necessário. Caso contrário, clique no **botão Autorizar usando OAuth.**
1. Quando solicitado, faça login na sua conta do GitHub. Em seguida, selecione Autorizar para conceder acesso à sua organização Azure DevOps. Se for bem-sucedido, um novo ponto final de serviço será criado.
1. Clique no botão elipse ao lado do botão **Repositório.** Selecione o repositório *<GitHub_username>/simples da* lista. Clique no botão **Selecionar**.
1. Selecione o ramo *mestre* no **ramo Padrão para que as compilações manuais e programadas** se abaixem. Clique no botão **Continuar**. A página de seleção de modelos é exibida.

### <a name="create-the-build-definition"></a>Crie a definição de compilação

1. Na página de seleção do modelo, digite *ASP.NET Core* na caixa de pesquisa:

    ![pesquisa ASP.NET Core na página do modelo](media/cicd/vsts-template-selection.png)

1. Os resultados da pesquisa do modelo aparecem. Passar o mouse sobre o modelo **ASP.NET Core** e clique no botão **Aplicar.**
1. A guia **Tarefas** da definição de compilação é exibida. Clique na guia **Gatilhos.**
1. Verifique a **caixa Ativar integração contínua.** Na seção **'Ramificação' filtros,** confirme se **a** lista de itens de isto está definida como *Incluir*. Defina a **especificação de ramificação** para baixo como *mestre*.

    ![Habilite configurações de integração contínua](media/cicd/vsts-enable-ci.png)

    Essas configurações fazem com que uma compilação seja acionada quando qualquer alteração é empurrada para o ramo *principal* do repositório GitHub. A integração contínua é testada nas alterações do Commit no GitHub e implantada automaticamente na seção [Azure.](#commit-changes-to-github-and-automatically-deploy-to-azure)

1. Clique no botão **Salvar & fila** e selecione a opção **Salvar:**

    ![Botão Salvar](media/cicd/vsts-save-build.png)

1. A seguinte diálogo modal aparece:

    ![Salvar definição de construção - diálogo modal](media/cicd/vsts-save-modal.png)

    Use a pasta *\\*padrão de e clique no botão **Salvar.**

### <a name="create-the-release-pipeline"></a>Criar o pipeline de lançamento

1. Clique na guia **Lançamentos** do projeto da sua equipe. Clique no botão **Novo pipeline.**

    ![Guia de lançamentos - Novo botão de definição](media/cicd/vsts-new-release-definition.png)

    O painel de seleção do modelo é exibido.

1. Na página de seleção do modelo, digite *O Serviço de Aplicativo* na caixa de pesquisa:

    ![Liberar caixa de pesquisa do modelo de pipeline](media/cicd/vsts-release-template-search.png)

1. Os resultados da pesquisa do modelo aparecem. Passar o mouse sobre a implantação do serviço de aplicativo do **Azure com** o modelo Slot e clique no botão **Aplicar.** A guia **Pipeline** do pipeline de liberação é exibida.

    ![Liberar a guia pipeline pipeline](media/cicd/vsts-release-definition-pipeline.png)

1. Clique no botão **Adicionar** na caixa **Artefatos.** O **painel Adicionar artefato** é exibido:

    ![Gasoduto de liberação - Adicionar painel de artefatos](media/cicd/vsts-release-add-artifact.png)

1. Selecione o bloco **'Construir'** na seção **Tipo de origem.** Este tipo permite a vinculação do pipeline de liberação à definição de compilação.
1. Selecione *MyFirstProject* na parada do **Projeto.**
1. Selecione o nome da definição de compilação, *MyFirstProject-ASP.NET Core-CI*, a partir da **parada de origem (definição de compilação).**
1. Selecione *Mais recente* na **versão padrão.** Esta opção constrói os artefatos produzidos pela última execução da definição de construção.
1. Substitua o texto na caixa de texto de código de texto **da fonte alias** *por Drop*.
1. Clique no botão **Adicionar**. A seção **Artefatos** atualiza-se para exibir as alterações.
1. Clique no ícone do relâmpago para ativar implantações contínuas:

    ![Lançar artefatos do gasoduto - ícone de relâmpago](media/cicd/vsts-artifacts-lightning-bolt.png)

    Com essa opção ativada, uma implantação ocorre cada vez que uma nova compilação está disponível.
1. Um **painel de gatilho de implantação contínua** aparece à direita. Clique no botão alternar para ativar o recurso. Não é necessário ativar o gatilho de **solicitação de puxar**.
1. Clique em **Adicionar** a lista de filtros de **ramificação Build.** Escolha a opção **de ramificação padrão da Configuração de compilação.** Este filtro faz com que a liberação seja acionada apenas para uma compilação a partir do ramo *principal* do repositório GitHub.
1. Clique no botão **Salvar**. Clique no botão **OK** na caixa de diálogo modal **Salvar** resultante.
1. Clique na caixa **Ambiente 1.** Um **painel de ambiente** aparece à direita. Alterar o *texto 'Ambiente 1'* no **nome ambiente** caixa de texto para *Produção*.

   ![Pipeline de liberação - Caixa de texto nome do ambiente](media/cicd/vsts-environment-name-textbox.png)

1. Clique na **fase 1, 2 tarefas** link na caixa **Produção:**

    ![Gasoduto de liberação - Link ambiente de produção.png](media/cicd/vsts-production-link.png)

    A guia **Tarefas** do ambiente é exibida.
1. Clique na **tarefa Implantar serviço de aplicativo do Azure para slot.** Suas configurações aparecem em um painel à direita.
1. Selecione a assinatura do Azure associada ao Serviço de Aplicativo a partir da entrada de assinatura do **Azure.** Uma vez selecionado, clique no botão **Autorizar.**
1. Selecione *o Aplicativo* da Web a partir do tipo de **aplicativo** parado.
1. Selecione *mywebapp/<unique_number/>* na parada de nome do serviço do **aplicativo.**
1. Selecione *AzureTutorial* no **grupo de** recursos parado.
1. Selecione *a encenação* na estada de **slot.**
1. Clique no botão **Salvar**.
1. Passar o passar por cima do nome do pipeline de liberação padrão. Clique no ícone do lápis para editá-lo. Use *MyFirstProject-ASP.NET Core-CD* como nome.

    ![Nome do gasoduto de liberação](media/cicd/vsts-release-definition-name.png)

1. Clique no botão **Salvar**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Confirmar alterações no GitHub e implantar automaticamente no Azure

1. Abra *simpleFeedReader.sln* no Visual Studio.
1. No Solution Explorer, abra *Pages\Index.cshtml*. Alterar `<h2>Simple Feed Reader - V3</h2>` para `<h2>Simple Feed Reader - V4</h2>`.
1. Pressione **ctrl**+**shift**+**B** para construir o aplicativo.
1. Compreenda o arquivo no repositório do GitHub. Use a página **Alterações** na guia Explorador de *equipe* do Visual Studio ou execute o seguinte usando o shell de comando da máquina local:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Empurre a alteração no ramo *mestre* para o controle remoto de *origem* do seu repositório GitHub:

    ```console
    git push origin master
    ```

    O commit aparece no ramo *principal* do repositório GitHub:

    ![GitHub se compromete em ramo mestre](media/cicd/github-commit.png)

    A compilação é acionada, uma vez que a integração contínua está ativada na guia **Gatilhos** da definição de compilação:

    ![permitir a integração contínua](media/cicd/enable-ci.png)

1. Navegue até a guia **Enfileirada** da página >  **Builds do Azure Pipelines**nos**Serviços** Azure DevOps. A compilação enfileirada mostra o ramo e o compromisso que desencadeou a compilação:

    ![construção enfileirada](media/cicd/build-queued.png)

1. Uma vez que a compilação é bem sucedida, uma implantação no Azure ocorre. Navegue até o aplicativo no navegador. Observe que o texto "V4" aparece no título:

    ![aplicativo atualizado](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Examine o pipeline da Azure Pipelines

### <a name="build-definition"></a>Definição da compilação

Uma definição de compilação foi criada com o nome *MyFirstProject-ASP.NET Core-CI*. Após a conclusão, a compilação produz um arquivo *.zip,* incluindo os ativos a serem publicados. O oleoduto de liberação implanta esses ativos no Azure.

A guia **Tarefas** da definição de compilação lista as etapas individuais que estão sendo usadas. Há cinco tarefas de construção.

![construir tarefas de definição](media/cicd/build-definition-tasks.png)

1. **Restaurar** &mdash; Executa `dotnet restore` o comando para restaurar os pacotes NuGet do aplicativo. A alimentação padrão do pacote usada é nuget.org.
1. **Build** &mdash; Executa `dotnet build --configuration release` o comando para compilar o código do aplicativo. Esta `--configuration` opção é usada para produzir uma versão otimizada do código, que é adequada para implantação em um ambiente de produção. Modifique a variável *BuildConfiguration* na guia **Variáveis** da definição de compilação se, por exemplo, uma configuração de depuração for necessária.
1. **Teste** &mdash; Executa `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` o comando para executar os testes unitários do aplicativo. Os testes unitários são executados `**/*Tests/*.csproj` dentro de qualquer projeto C# que corresponda ao padrão glob. Os resultados do teste são salvos em um `--results-directory` arquivo *.trx* no local especificado pela opção. Se algum teste falhar, a compilação falha e não é implantada.

    > [!NOTE]
    > Para verificar o trabalho dos testes unitários, modifique *o SimpleFeedReader.Tests\Services\NewsServiceTests.cs* para quebrar propositalmente um dos testes. Por exemplo, `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` mude `Returns_News_Stories_Given_Valid_Uri` para no método. Comprometa e empurre a mudança para o GitHub. A construção é acionada e falha. O status do pipeline de construção muda para **falha**. Reverter a mudança, comprometer e empurrar novamente. A construção é bem sucedida.

1. **Publicar** &mdash; Executa `dotnet publish --configuration release --output <local_path_on_build_agent>` o comando para produzir um arquivo *.zip* com os artefatos a serem implantados. A `--output` opção especifica o local de publicação do arquivo *.zip.* Esse local é especificado passando uma `$(build.artifactstagingdirectory)` [variável predefinida](/azure/devops/pipelines/build/variables) chamada . Essa variável expande-se para um caminho local, como *c:\agent\_work\1\a*, no agente de compilação.
1. **Publicar artefato** &mdash; Publica o arquivo *.zip* produzido pela tarefa **Publicar.** A tarefa aceita a localização do arquivo *.zip* como parâmetro, que é a variável `$(build.artifactstagingdirectory)`predefinida . O arquivo *.zip* é publicado como uma pasta chamada *drop*.

Clique no link **Resumo** da definição de compilação para exibir um histórico de compilações com a definição:

![Captura de tela mostrando o histórico de definição de compilação](media/cicd/build-definition-summary.png)

Na página resultante, clique no link correspondente ao número de compilação exclusivo:

![Captura de tela mostrando página de resumo de definição de compilação](media/cicd/build-definition-completed.png)

Um resumo desta compilação específica é exibido. Clique na guia **Artefatos** e observe que a pasta *de queda* produzida pela compilação está listada:

![Captura de tela mostrando artefatos de definição de construção - pasta de queda](media/cicd/build-definition-artifacts.png)

Use os links **Download** e **Explore** para inspecionar os artefatos publicados.

### <a name="release-pipeline"></a>Pipeline de lançamento

Um pipeline de lançamento foi criado com o nome *MyFirstProject-ASP.NET Core-CD*:

![Captura de tela mostrando visão geral do pipeline de liberação](media/cicd/release-definition-overview.png)

Os dois principais componentes do gasoduto de liberação são os **Artefatos** e os **Ambientes.** Clicar na caixa na seção **Artefatos** revela o seguinte painel:

![Captura de tela mostrando artefatos de tubulação de liberação](media/cicd/release-definition-artifacts.png)

O valor **Source (Build definition)** representa a definição de compilação à qual este pipeline de versão está vinculado. O arquivo *.zip* produzido por uma execução bem-sucedida da definição de compilação é fornecido ao ambiente *De produção* para implantação no Azure. Clique na *fase 1, 2 tarefas* link na caixa ambiente *Produção* para visualizar as tarefas do pipeline de liberação:

![Captura de tela mostrando tarefas de pipeline de liberação](media/cicd/release-definition-tasks.png)

O pipeline de lançamento consiste em duas tarefas: *Implantar o Serviço de Aplicativos Azure para slot* e gerenciar o serviço de aplicativos do *Azure - Slot Swap*. Clicar na primeira tarefa revela a seguinte configuração de tarefa:

![Captura de tela mostrando a tarefa de implantação do pipeline de lançamento](media/cicd/release-definition-task1.png)

A assinatura do Azure, o tipo de serviço, o nome do aplicativo da Web, o grupo de recursos e o slot de implantação são definidos na tarefa de implantação. A caixa de texto **'Pacote ou pasta'** contém o caminho do arquivo *.zip* a ser extraído e implantado no slot de *preparação* do *aplicativo webapp\<unique_number\> * web.

Clicar na tarefa de troca de slot revela a seguinte configuração de tarefa:

![Captura de tela mostrando a tarefa de troca de slot do pipeline de liberação](media/cicd/release-definition-task2.png)

Os detalhes de assinatura, grupo de recursos, tipo de serviço, nome do aplicativo web e detalhes do slot de implantação são fornecidos. A caixa de seleção **Swap with Production** é verificada. Consequentemente, os bits implantados no slot *de encenação* são trocados para o ambiente de produção.

## <a name="additional-reading"></a>Leitura adicional

* [Criar seu primeiro pipeline com o Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projeto Build e .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Implantar um aplicativo web com a Azure Pipelines](/azure/devops/pipelines/targets/webapp)
