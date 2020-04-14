---
title: Implantar um aplicativo para o App Service - DevOps com ASP.NET Core e Azure
author: CamSoper
description: Implante um aplicativo ASP.NET Core para o Azure App Service, o primeiro passo para DevOps com ASP.NET Core e Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: d7ee3e42d320d35c2aaff6e097203c45289ec5b1
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228121"
---
# <a name="deploy-an-app-to-app-service"></a>Implantar um aplicativo para o App Service

[O Azure App Service](/azure/app-service/) é a plataforma de hospedagem web do Azure. A implantação de um aplicativo web no Azure App Service pode ser feita manualmente ou por um processo automatizado. Esta seção do guia discute métodos de implantação que podem ser acionados manualmente ou por script usando a linha de comando, ou acionados manualmente usando o Visual Studio.

Nesta seção, você realizará as seguintes tarefas:

* Baixe e construa o aplicativo de exemplo.
* Crie um aplicativo web do azure App usando o Azure Cloud Shell.
* Implante o aplicativo de exemplo para o Azure usando o Git.
* Implante uma alteração no aplicativo usando o Visual Studio.
* Adicione um slot de preparação ao aplicativo web.
* Implante uma atualização no slot de preparação.
* Alterne os slots de preparo e de produção.

## <a name="download-and-test-the-app"></a>Baixe e teste o aplicativo

O aplicativo usado neste guia é um aplicativo de ASP.NET Core pré-construído, [O Simples Leitor de Feed](https://github.com/Azure-Samples/simple-feed-reader/). É um aplicativo Razor Pages `Microsoft.SyndicationFeed.ReaderWriter` que usa a API para recuperar um feed RSS/Atom e exibir as notícias em uma lista.

Sinta-se livre para rever o código, mas é importante entender que não há nada de especial neste aplicativo. É apenas um simples aplicativo ASP.NET Core para fins ilustrativos.

A partir de um shell de comando, baixe o código, construa o projeto e execute-o da seguinte forma.

> *Nota: Os usuários de Linux/macOS devem fazer alterações apropriadas para caminhos, por exemplo, usando barra para frente (`/`) em vez de barra traseira ().`\`*

1. Clone o código para uma pasta em sua máquina local.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Altere sua pasta de trabalho para a pasta *de leitor de feed simples* criada.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Restaure os pacotes e construa a solução.

    ```dotnetcli
    dotnet build
    ```

4. Execute o aplicativo.

    ```dotnetcli
    dotnet run
    ```

    ![O comando dotnet run é bem sucedido](./media/deploying-to-app-service/dotnet-run.png)

5. Abra um navegador e navegue até `http://localhost:5000`. O aplicativo permite que você digite ou cole um URL de feed de sindicalização e visualize uma lista de notícias.

     ![O aplicativo que exibe o conteúdo de um feed RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Uma vez satisfeito que o aplicativo esteja funcionando corretamente, desligue-o pressionando **Ctrl**+**C** no shell de comando.

## <a name="create-the-azure-app-service-web-app"></a>Crie o aplicativo web do serviço do aplicativo Azure

Para implantar o aplicativo, você precisará criar um App Service [Web App](/azure/app-service/app-service-web-overview). Após a criação do Web App, você será implantado a partir de sua máquina local usando o Git.

1. Entrar no [Azure Cloud Shell](https://shell.azure.com/bash). Nota: Quando você faz login pela primeira vez, o Cloud Shell solicita a criação de uma conta de armazenamento para arquivos de configuração. Aceite os padrões ou forneça um nome único.

2. Use o Cloud Shell para as seguintes etapas.

    a. Declare uma variável para armazenar o nome do seu aplicativo web. O nome deve ser único para ser usado na URL padrão. Usar `$RANDOM` a função Bash para construir o nome `webappname99999`garante exclusividade e resultados no formato .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Crie um grupos de recursos. Os grupos de recursos fornecem um meio para agregar recursos do Azure a serem gerenciados em grupo.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    O `az` comando invoca o [Azure CLI](/cli/azure/). O CLI pode ser executado localmente, mas usá-lo no Cloud Shell economiza tempo e configuração.

    c. Crie um plano de serviço de aplicativo no nível S1. Um plano de Serviço de Aplicativo é um agrupamento de aplicativos web que compartilham o mesmo nível de preços. O nível S1 não é gratuito, mas é necessário para o recurso de slots de encenação.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Crie o recurso do aplicativo web usando o plano App Service no mesmo grupo de recursos.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Defina as credenciais de implantação. Essas credenciais de implantação se aplicam a todos os aplicativos da Web em sua assinatura. Não use caracteres especiais no nome do usuário.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Configure o aplicativo web para aceitar implantações do Git local e exiba a URL de implantação do *Git*. **Observe esta URL para referência mais tarde**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Exibir a URL do *aplicativo web*. Navegue até esta URL para ver o aplicativo web em branco. **Observe esta URL para referência mais tarde**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Usando um shell de comando em sua máquina local, navegue `.\simple-feed-reader\SimpleFeedReader`até a pasta de projeto do aplicativo web (por exemplo, ). Execute os seguintes comandos para configurar o Git para empurrar para a URL de implantação:

    a. Adicione a URL remota ao repositório local.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Empurre o ramo *principal* local para o ramo *principal* do *azure-prod* remote.

    ```console
    git push azure-prod master
    ```

    Você será solicitado para as credenciais de implantação que você criou anteriormente. Observe a saída no projétil de comando. O Azure constrói o aplicativo ASP.NET Core remotamente.

4. Em um navegador, navegue até a URL do *aplicativo da Web* e observe que o aplicativo foi construído e implantado. Alterações adicionais podem ser comprometidas no `git commit`repositório git local com . Essas alterações são empurradas para `git push` o Azure com o comando anterior.

## <a name="deployment-with-visual-studio"></a>Implantação com o Visual Studio

> *Nota: Esta seção se aplica apenas ao Windows. Os usuários de Linux e macOS devem fazer a alteração descrita na etapa 2 abaixo. Salvar o arquivo e comprometer a alteração no `git commit`repositório local com . Por fim, empurre a mudança com `git push`, como na primeira seção.*

O aplicativo já foi implantado a partir do shell de comando. Vamos usar as ferramentas integradas do Visual Studio para implantar uma atualização no aplicativo. Nos bastidores, o Visual Studio realiza a mesma coisa que a ferramenta de linha de comando, mas dentro da iU familiar do Visual Studio.

1. Abra *simpleFeedReader.sln* no Visual Studio.
2. No Solution Explorer, abra *Pages\Index.cshtml*. Alterar `<h2>Simple Feed Reader</h2>` para `<h2>Simple Feed Reader - V2</h2>`.
3. Pressione **ctrl**+**shift**+**B** para construir o aplicativo.
4. No Solution Explorer, clique com o botão direito do mouse no projeto e clique **em Publicar**.

    ![Captura de tela mostrando o clique direito, publicar](./media/deploying-to-app-service/publish.png)
5. O Visual Studio pode criar um novo recurso de Serviço de Aplicativo, mas essa atualização será publicada durante a implantação existente. Na **marcação Escolher uma** caixa de diálogo de destino de publicação, selecione Serviço de **aplicativo** na lista à esquerda e, em seguida, **selecione Selecionar 'Existir'.** Clique em **Publicar**.
6. Na caixa de diálogo Serviço de **Aplicativo,** confirme se a conta Microsoft ou Organizacional usada para criar sua assinatura do Azure é exibida no canto superior direito. Se não for, clique no drop-down e adicione-o.
7. Confirme se a **assinatura** correta do Azure está selecionada. Para **Exibir,** selecione **Grupo de recursos**. Expanda o grupo de recursos **do AzureTutorial** e selecione o aplicativo web existente. Clique em **OK**.

    ![Captura de tela mostrando a caixa de diálogo Do Serviço de Publicação do Aplicativo](./media/deploying-to-app-service/publish-dialog.png)

O Visual Studio constrói e implanta o aplicativo no Azure. Navegue pela URL do aplicativo web. Valide `<h2>` que a modificação do elemento está ao vivo.

![O aplicativo com o título alterado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Slots de implantação

Os slots de implantação suportam a realização de alterações sem afetar o aplicativo em execução na produção. Uma vez que a versão encenada do aplicativo é validada por uma equipe de garantia de qualidade, os slots de produção e encenação podem ser trocados. O aplicativo em encenação é promovido à produção desta forma. As etapas seguintes criam um slot de preparação, implantam algumas alterações nele e trocam o slot de preparação com a produção após a verificação.

1. Faça login na [Azure Cloud Shell](https://shell.azure.com/bash), se ainda não estiver conectado.
2. Crie o slot de encenação.

    a. Crie um slot de implantação com o nome *encenando*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Configure o slot de preparação para usar a implantação do Git local e obtenha a URL de implantação **de preparação.** **Observe esta URL para referência mais tarde**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Exibir a URL do slot de preparação. Navegue até a URL para ver o slot de preparação vazio. **Observe esta URL para referência mais tarde**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Em um editor de texto ou visual studio, modifique *Páginas/Index.cshtml* novamente para que o `<h2>` elemento leia `<h2>Simple Feed Reader - V3</h2>` e salve o arquivo.

4. Comprometa o arquivo no repositório git local, usando a página **Alterações** na guia *Team Explorer* do Visual Studio ou inserindo o seguinte usando o shell de comando da máquina local:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Usando o shell de comando da máquina local, adicione a URL de implantação de preparação como um controle remoto Do Git e empurre as alterações comprometidas:

    a. Adicione a URL remota para a preparação ao repositório git local.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Empurre o ramo *principal* local para *o* ramo *principal* do controle remoto.

    ```console
    git push azure-staging master
    ```

    Espere enquanto o Azure cria e implanta o aplicativo.

6. Para verificar se o V3 foi implantado no slot de preparação, abra duas janelas do navegador. Em uma janela, navegue até a URL original do aplicativo web. Na outra janela, navegue até a URL do aplicativo web de preparação. A URL de produção serve V2 do aplicativo. A URL de encenação serve V3 do aplicativo.

    ![Captura de tela comparando as janelas do navegador](./media/deploying-to-app-service/ready-to-swap.png)

7. Na Cloud Shell, troque o slot de preparação verificado/aquecido em produção.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Verifique se a troca ocorreu atualizando as duas janelas do navegador.

    ![Comparando as janelas do navegador após a troca](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Resumo

Nesta seção, foram concluídas as seguintes tarefas:

* Baixei e construí o aplicativo de amostra.
* Criei um aplicativo web do azure App usando o Azure Cloud Shell.
* Implantei o aplicativo de exemplo no Azure usando o Git.
* Implantei uma alteração no aplicativo usando o Visual Studio.
* Adicionou um slot de preparação ao aplicativo web.
* Implantei uma atualização para o slot de preparação.
* Troquei os slots de encenação e produção.

Na próxima seção, você aprenderá a construir um pipeline DevOps com a Azure Pipelines.

## <a name="additional-reading"></a>Leitura adicional

* [Visão geral de aplicativos Web](/azure/app-service/app-service-web-overview)
* [Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Configurar as credenciais de implantação do Serviço de Aplicativo do Azure](/azure/app-service/app-service-deployment-credentials)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-staged-publishing)
