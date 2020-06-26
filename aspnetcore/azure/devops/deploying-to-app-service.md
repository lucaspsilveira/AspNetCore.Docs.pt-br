---
title: Implantar um aplicativo no serviço de aplicativo-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Implantar um aplicativo ASP.NET Core no serviço Azure App, a primeira etapa para DevOps com ASP.NET Core e Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 7cf6395b6f57413d85532ed15e5a875af10f905b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400382"
---
# <a name="deploy-an-app-to-app-service"></a>Implantar um aplicativo no serviço de aplicativo

[Azure app serviço](/azure/app-service/) é a plataforma de hospedagem na Web do Azure. Implantar um aplicativo Web em Azure App serviço pode ser feito manualmente ou por um processo automatizado. Esta seção do guia discute os métodos de implantação que podem ser disparados manualmente ou por script usando a linha de comando ou disparados manualmente usando o Visual Studio.

Nesta seção, você realizará as seguintes tarefas:

* Baixe e compile o aplicativo de exemplo.
* Crie um aplicativo Web do serviço de Azure App usando o Azure Cloud Shell.
* Implante o aplicativo de exemplo no Azure usando o git.
* Implante uma alteração no aplicativo usando o Visual Studio.
* Adicione um slot de preparo ao aplicativo Web.
* Implante uma atualização no slot de preparo.
* Alterne os slots de preparo e de produção.

## <a name="download-and-test-the-app"></a>Baixar e testar o aplicativo

O aplicativo usado neste guia é um aplicativo de ASP.NET Core pré-compilado, leitor de [feeds simples](https://github.com/Azure-Samples/simple-feed-reader/). É um Razor aplicativo de páginas que usa a `Microsoft.SyndicationFeed.ReaderWriter` API para recuperar um feed RSS/Atom e exibir os itens de notícias em uma lista.

Sinta-se à vontade para examinar o código, mas é importante entender que não há nada de especial sobre esse aplicativo. É apenas um aplicativo ASP.NET Core simples para fins ilustrativos.

Em um shell de comando, baixe o código, compile o projeto e execute-o da seguinte maneira.

> *Observação: os usuários do Linux/macOS devem fazer as alterações apropriadas para caminhos, por exemplo, usando `/` a barra () em vez da barra invertida ( `\` ).*

1. Clone o código em uma pasta no computador local.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Altere sua pasta de trabalho para a pasta de *leitor de feed simples* que foi criada.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Restaure os pacotes e Compile a solução.

    ```dotnetcli
    dotnet build
    ```

4. Execute o aplicativo.

    ```dotnetcli
    dotnet run
    ```

    ![O comando dotnet Run foi bem-sucedido](./media/deploying-to-app-service/dotnet-run.png)

5. Abra um navegador e navegue até `http://localhost:5000`. O aplicativo permite digitar ou colar uma URL de feed de agregação e exibir uma lista de itens de notícias.

     ![O aplicativo que exibe o conteúdo de um RSS feed](./media/deploying-to-app-service/app-in-browser.png)

6. Quando estiver satisfeito, o aplicativo está funcionando corretamente, desative-o pressionando **Ctrl** + **C** no Shell de comando.

## <a name="create-the-azure-app-service-web-app"></a>Criar o aplicativo Web do serviço de Azure App

Para implantar o aplicativo, você precisará criar um [aplicativo Web](/azure/app-service/app-service-web-overview)do serviço de aplicativo. Após a criação do aplicativo Web, você irá implantá-lo a partir de seu computador local usando o git.

1. Entrar no [Azure Cloud Shell](https://shell.azure.com/bash). Observação: quando você entra pela primeira vez, Cloud Shell solicita a criação de uma conta de armazenamento para arquivos de configuração. Aceite os padrões ou forneça um nome exclusivo.

2. Use o Cloud Shell para as etapas a seguir.

    a. Declare uma variável para armazenar o nome do aplicativo Web. O nome deve ser exclusivo para ser usado na URL padrão. O uso da `$RANDOM` função bash para construir o nome garante exclusividade e resulta no formato `webappname99999` .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Crie um grupos de recursos. Os grupos de recursos fornecem um meio de agregar recursos do Azure a serem gerenciados como um grupo.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    O `az` comando invoca o [CLI do Azure](/cli/azure/). A CLI pode ser executada localmente, mas usá-la no Cloud Shell poupa tempo e configuração.

    c. Crie um plano do serviço de aplicativo na camada S1. Um plano do serviço de aplicativo é um agrupamento de aplicativos Web que compartilham o mesmo tipo de preço. A camada S1 não é gratuita, mas é necessária para o recurso de Slots de preparo.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Crie o recurso de aplicativo Web usando o plano do serviço de aplicativo no mesmo grupo de recursos.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Defina as credenciais de implantação. Essas credenciais de implantação se aplicam a todos os aplicativos Web em sua assinatura. Não use caracteres especiais no nome de usuário.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Configure o aplicativo Web para aceitar implantações do git local e exibir a *URL de implantação do git*. **Anote essa URL para referência mais tarde**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Exiba a *URL do aplicativo Web*. Navegue até essa URL para ver o aplicativo Web em branco. **Anote essa URL para referência mais tarde**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Usando um shell de comando em seu computador local, navegue até a pasta do projeto do aplicativo Web (por exemplo, `.\simple-feed-reader\SimpleFeedReader` ). Execute os comandos a seguir para configurar o Git para enviar por push para a URL de implantação:

    a. Adicione a URL remota ao repositório local.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Envie por push o Branch *mestre* local para o Branch *mestre* do *Azure-prod* remoto.

    ```console
    git push azure-prod master
    ```

    Você será solicitado a fornecer as credenciais de implantação que criou anteriormente. Observe a saída no Shell de comando. O Azure cria o aplicativo ASP.NET Core remotamente.

4. Em um navegador, navegue até a *URL do aplicativo Web* e anote que o aplicativo foi compilado e implantado. Alterações adicionais podem ser confirmadas no repositório git local com `git commit` . Essas alterações são enviadas por push para o Azure com o `git push` comando anterior.

## <a name="deployment-with-visual-studio"></a>Implantação com o Visual Studio

> *Observação: Esta seção aplica-se somente ao Windows. Os usuários do Linux e do macOS devem fazer a alteração descrita na etapa 2 abaixo. Salve o arquivo e confirme a alteração no repositório local com `git commit` . Por fim, envie por push a alteração com `git push` , como na primeira seção.*

O aplicativo já foi implantado a partir do Shell de comando. Vamos usar as ferramentas integradas do Visual Studio para implantar uma atualização para o aplicativo. Nos bastidores, o Visual Studio realiza a mesma coisa que as ferramentas de linha de comando, mas dentro da interface do usuário familiar do Visual Studio.

1. Abra *SimpleFeedReader. sln* no Visual Studio.
2. Em Gerenciador de Soluções, abra *Pages\Index.cshtml*. Alterar `<h2>Simple Feed Reader</h2>` para `<h2>Simple Feed Reader - V2</h2>`.
3. Pressione **Ctrl** + **Shift** + **B** para compilar o aplicativo.
4. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **publicar**.

    ![Captura de tela mostrando clique com o botão direito do mouse, publicar](./media/deploying-to-app-service/publish.png)
5. O Visual Studio pode criar um novo recurso do serviço de aplicativo, mas essa atualização será publicada na implantação existente. Na caixa de diálogo **escolher um destino de publicação** , selecione **serviço de aplicativo** na lista à esquerda e selecione **selecionar existente**. Clique em **Publicar**.
6. Na caixa de diálogo **serviço de aplicativo** , confirme se a conta da Microsoft ou da organização usada para criar sua assinatura do Azure é exibida no canto superior direito. Se não estiver, clique na lista suspensa e adicione-a.
7. Confirme se a **assinatura** correta do Azure está selecionada. Para **exibição**, selecione **grupo de recursos**. Expanda o grupo de recursos **AzureTutorial** e selecione o aplicativo Web existente. Clique em **OK**.

    ![Captura de tela mostrando a caixa de diálogo Publicar serviço de aplicativo](./media/deploying-to-app-service/publish-dialog.png)

O Visual Studio compila e implanta o aplicativo no Azure. Navegue até a URL do aplicativo Web. Valide se a `<h2>` modificação do elemento está ativa.

![O aplicativo com o título alterado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Slots de implantação

Os slots de implantação dão suporte à preparação de alterações sem afetar o aplicativo em execução na produção. Depois que a versão em etapas do aplicativo for validada por uma equipe de garantia de qualidade, os slots de produção e de preparo poderão ser trocados. O aplicativo em preparo é promovido para produção dessa maneira. As etapas a seguir criam um slot de preparo, implantam algumas alterações e trocam o slot de preparo com produção após a verificação.

1. Entre no [Azure cloud Shell](https://shell.azure.com/bash), se ainda não tiver feito logon.
2. Crie o slot de preparo.

    a. Crie um slot de implantação com o nome de *preparo*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Configure o slot de preparo para usar a implantação do git local e obtenha a URL de implantação de **preparo** . **Anote essa URL para referência mais tarde**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Exiba a URL do slot de preparo. Navegue até a URL para ver o slot de preparo vazio. **Anote essa URL para referência mais tarde**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Em um editor de texto ou no Visual Studio, modifique *pages/index. cshtml* novamente para que o `<h2>` elemento leia `<h2>Simple Feed Reader - V3</h2>` e salve o arquivo.

4. Confirme o arquivo no repositório git local, usando a página **alterações** na guia *Team Explorer* do Visual Studio ou digitando o seguinte usando o Shell de comando do computador local:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Usando o Shell de comando do computador local, adicione a URL de implantação de preparo como um git remoto e envie por push as alterações confirmadas:

    a. Adicione a URL remota para preparo para o repositório git local.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Envie por push o Branch *mestre* local para o Branch *mestre* do *Azure-preparo* remoto.

    ```console
    git push azure-staging master
    ```

    Aguarde enquanto o Azure cria e implanta o aplicativo.

6. Para verificar se o V3 foi implantado no slot de preparo, abra duas janelas do navegador. Em uma janela, navegue até a URL do aplicativo Web original. Na outra janela, navegue até a URL do aplicativo Web de preparo. A URL de produção serve v2 do aplicativo. A URL de preparo serve V3 do aplicativo.

    ![Captura de tela comparando as janelas do navegador](./media/deploying-to-app-service/ready-to-swap.png)

7. Na Cloud Shell, troque o slot de preparo verificado/quente em produção.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Verifique se a troca ocorreu atualizando as duas janelas do navegador.

    ![Comparando as janelas do navegador após a permuta](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Resumo

Nesta seção, as seguintes tarefas foram concluídas:

* Baixado e criado o aplicativo de exemplo.
* Criou um aplicativo Web do serviço de Azure App usando o Azure Cloud Shell.
* O aplicativo de exemplo foi implantado no Azure usando o git.
* Implantou uma alteração no aplicativo usando o Visual Studio.
* Um slot de preparo foi adicionado ao aplicativo Web.
* Implantou uma atualização para o slot de preparo.
* Trocados os slots de preparo e de produção.

Na próxima seção, você aprenderá a criar um pipeline do DevOps com o Azure Pipelines.

## <a name="additional-reading"></a>Leitura adicional

* [Visão geral dos aplicativos Web](/azure/app-service/app-service-web-overview)
* [Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Configurar as credenciais de implantação do Serviço de Aplicativo do Azure](/azure/app-service/app-service-deployment-credentials)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-staged-publishing)
