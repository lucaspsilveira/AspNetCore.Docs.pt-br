---
title: Implantar um aplicativo para o App Service - DevOps com ASP.NET Core e Azure
author: CamSoper
description: Implante um aplicativo ASP.NET Core para o Azure App Service, o primeiro passo para DevOps com ASP.NET Core e Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657741"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="bb792-103">Implantar um aplicativo para o App Service</span><span class="sxs-lookup"><span data-stu-id="bb792-103">Deploy an app to App Service</span></span>

<span data-ttu-id="bb792-104">[O Azure App Service](/azure/app-service/) é a plataforma de hospedagem web do Azure.</span><span class="sxs-lookup"><span data-stu-id="bb792-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="bb792-105">A implantação de um aplicativo web no Azure App Service pode ser feita manualmente ou por um processo automatizado.</span><span class="sxs-lookup"><span data-stu-id="bb792-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="bb792-106">Esta seção do guia discute métodos de implantação que podem ser acionados manualmente ou por script usando a linha de comando, ou acionados manualmente usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bb792-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="bb792-107">Nesta seção, você realizará as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="bb792-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="bb792-108">Baixe e construa o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="bb792-108">Download and build the sample app.</span></span>
* <span data-ttu-id="bb792-109">Crie um aplicativo web do azure App usando o Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="bb792-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="bb792-110">Implante o aplicativo de exemplo para o Azure usando o Git.</span><span class="sxs-lookup"><span data-stu-id="bb792-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="bb792-111">Implante uma alteração no aplicativo usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bb792-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="bb792-112">Adicione um slot de preparação ao aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="bb792-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="bb792-113">Implante uma atualização no slot de preparação.</span><span class="sxs-lookup"><span data-stu-id="bb792-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="bb792-114">Alterne os slots de preparo e de produção.</span><span class="sxs-lookup"><span data-stu-id="bb792-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="bb792-115">Baixe e teste o aplicativo</span><span class="sxs-lookup"><span data-stu-id="bb792-115">Download and test the app</span></span>

<span data-ttu-id="bb792-116">O aplicativo usado neste guia é um aplicativo de ASP.NET Core pré-construído, [O Simples Leitor de Feed](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="bb792-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="bb792-117">É um aplicativo Razor Pages `Microsoft.SyndicationFeed.ReaderWriter` que usa a API para recuperar um feed RSS/Atom e exibir as notícias em uma lista.</span><span class="sxs-lookup"><span data-stu-id="bb792-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="bb792-118">Sinta-se livre para rever o código, mas é importante entender que não há nada de especial neste aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb792-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="bb792-119">É apenas um simples aplicativo ASP.NET Core para fins ilustrativos.</span><span class="sxs-lookup"><span data-stu-id="bb792-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="bb792-120">A partir de um shell de comando, baixe o código, construa o projeto e execute-o da seguinte forma.</span><span class="sxs-lookup"><span data-stu-id="bb792-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="bb792-121">*Nota: Os usuários de Linux/macOS devem fazer alterações apropriadas para caminhos, por exemplo, usando barra para frente (`/`) em vez de barra traseira ().`\`*</span><span class="sxs-lookup"><span data-stu-id="bb792-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="bb792-122">Clone o código para uma pasta em sua máquina local.</span><span class="sxs-lookup"><span data-stu-id="bb792-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="bb792-123">Altere sua pasta de trabalho para a pasta *de leitor de feed simples* criada.</span><span class="sxs-lookup"><span data-stu-id="bb792-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="bb792-124">Restaure os pacotes e construa a solução.</span><span class="sxs-lookup"><span data-stu-id="bb792-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="bb792-125">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb792-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![O comando dotnet run é bem sucedido](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="bb792-127">Abra um navegador e navegue até `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="bb792-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="bb792-128">O aplicativo permite que você digite ou cole um URL de feed de sindicalização e visualize uma lista de notícias.</span><span class="sxs-lookup"><span data-stu-id="bb792-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![O aplicativo que exibe o conteúdo de um feed RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="bb792-130">Uma vez satisfeito que o aplicativo esteja funcionando corretamente, desligue-o pressionando **Ctrl**+**C** no shell de comando.</span><span class="sxs-lookup"><span data-stu-id="bb792-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="bb792-131">Crie o aplicativo web do serviço do aplicativo Azure</span><span class="sxs-lookup"><span data-stu-id="bb792-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="bb792-132">Para implantar o aplicativo, você precisará criar um App Service [Web App](/azure/app-service/app-service-web-overview).</span><span class="sxs-lookup"><span data-stu-id="bb792-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="bb792-133">Após a criação do Web App, você será implantado a partir de sua máquina local usando o Git.</span><span class="sxs-lookup"><span data-stu-id="bb792-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="bb792-134">Entrar no [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="bb792-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="bb792-135">Nota: Quando você faz login pela primeira vez, o Cloud Shell solicita a criação de uma conta de armazenamento para arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="bb792-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="bb792-136">Aceite os padrões ou forneça um nome único.</span><span class="sxs-lookup"><span data-stu-id="bb792-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="bb792-137">Use o Cloud Shell para as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="bb792-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="bb792-138">a.</span><span class="sxs-lookup"><span data-stu-id="bb792-138">a.</span></span> <span data-ttu-id="bb792-139">Declare uma variável para armazenar o nome do seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="bb792-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="bb792-140">O nome deve ser único para ser usado na URL padrão.</span><span class="sxs-lookup"><span data-stu-id="bb792-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="bb792-141">Usar `$RANDOM` a função Bash para construir o nome `webappname99999`garante exclusividade e resultados no formato .</span><span class="sxs-lookup"><span data-stu-id="bb792-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="bb792-142">b.</span><span class="sxs-lookup"><span data-stu-id="bb792-142">b.</span></span> <span data-ttu-id="bb792-143">Crie um grupos de recursos.</span><span class="sxs-lookup"><span data-stu-id="bb792-143">Create a resource group.</span></span> <span data-ttu-id="bb792-144">Os grupos de recursos fornecem um meio para agregar recursos do Azure a serem gerenciados em grupo.</span><span class="sxs-lookup"><span data-stu-id="bb792-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="bb792-145">O `az` comando invoca o [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="bb792-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="bb792-146">O CLI pode ser executado localmente, mas usá-lo no Cloud Shell economiza tempo e configuração.</span><span class="sxs-lookup"><span data-stu-id="bb792-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="bb792-147">c.</span><span class="sxs-lookup"><span data-stu-id="bb792-147">c.</span></span> <span data-ttu-id="bb792-148">Crie um plano de serviço de aplicativo no nível S1.</span><span class="sxs-lookup"><span data-stu-id="bb792-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="bb792-149">Um plano de Serviço de Aplicativo é um agrupamento de aplicativos web que compartilham o mesmo nível de preços.</span><span class="sxs-lookup"><span data-stu-id="bb792-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="bb792-150">O nível S1 não é gratuito, mas é necessário para o recurso de slots de encenação.</span><span class="sxs-lookup"><span data-stu-id="bb792-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="bb792-151">d.</span><span class="sxs-lookup"><span data-stu-id="bb792-151">d.</span></span> <span data-ttu-id="bb792-152">Crie o recurso do aplicativo web usando o plano App Service no mesmo grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="bb792-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="bb792-153">e.</span><span class="sxs-lookup"><span data-stu-id="bb792-153">e.</span></span> <span data-ttu-id="bb792-154">Defina as credenciais de implantação.</span><span class="sxs-lookup"><span data-stu-id="bb792-154">Set the deployment credentials.</span></span> <span data-ttu-id="bb792-155">Essas credenciais de implantação se aplicam a todos os aplicativos da Web em sua assinatura.</span><span class="sxs-lookup"><span data-stu-id="bb792-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="bb792-156">Não use caracteres especiais no nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="bb792-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="bb792-157">f.</span><span class="sxs-lookup"><span data-stu-id="bb792-157">f.</span></span> <span data-ttu-id="bb792-158">Configure o aplicativo web para aceitar implantações do Git local e exiba a URL de implantação do *Git*.</span><span class="sxs-lookup"><span data-stu-id="bb792-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="bb792-159">**Observe esta URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="bb792-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="bb792-160">g.</span><span class="sxs-lookup"><span data-stu-id="bb792-160">g.</span></span> <span data-ttu-id="bb792-161">Exibir a URL do *aplicativo web*.</span><span class="sxs-lookup"><span data-stu-id="bb792-161">Display the *web app URL*.</span></span> <span data-ttu-id="bb792-162">Navegue até esta URL para ver o aplicativo web em branco.</span><span class="sxs-lookup"><span data-stu-id="bb792-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="bb792-163">**Observe esta URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="bb792-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="bb792-164">Usando um shell de comando em sua máquina local, navegue `.\simple-feed-reader\SimpleFeedReader`até a pasta de projeto do aplicativo web (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="bb792-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="bb792-165">Execute os seguintes comandos para configurar o Git para empurrar para a URL de implantação:</span><span class="sxs-lookup"><span data-stu-id="bb792-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="bb792-166">a.</span><span class="sxs-lookup"><span data-stu-id="bb792-166">a.</span></span> <span data-ttu-id="bb792-167">Adicione a URL remota ao repositório local.</span><span class="sxs-lookup"><span data-stu-id="bb792-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="bb792-168">b.</span><span class="sxs-lookup"><span data-stu-id="bb792-168">b.</span></span> <span data-ttu-id="bb792-169">Empurre o ramo *principal* local para o ramo *principal* do *azure-prod* remote.</span><span class="sxs-lookup"><span data-stu-id="bb792-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="bb792-170">Você será solicitado para as credenciais de implantação que você criou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="bb792-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="bb792-171">Observe a saída no projétil de comando.</span><span class="sxs-lookup"><span data-stu-id="bb792-171">Observe the output in the command shell.</span></span> <span data-ttu-id="bb792-172">O Azure constrói o aplicativo ASP.NET Core remotamente.</span><span class="sxs-lookup"><span data-stu-id="bb792-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="bb792-173">Em um navegador, navegue até a URL do *aplicativo da Web* e observe que o aplicativo foi construído e implantado.</span><span class="sxs-lookup"><span data-stu-id="bb792-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="bb792-174">Alterações adicionais podem ser comprometidas no `git commit`repositório git local com .</span><span class="sxs-lookup"><span data-stu-id="bb792-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="bb792-175">Essas alterações são empurradas para `git push` o Azure com o comando anterior.</span><span class="sxs-lookup"><span data-stu-id="bb792-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="bb792-176">Implantação com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb792-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="bb792-177">*Nota: Esta seção se aplica apenas ao Windows. Os usuários de Linux e macOS devem fazer a alteração descrita na etapa 2 abaixo. Salvar o arquivo e comprometer a alteração no `git commit`repositório local com . Por fim, empurre a mudança com `git push`, como na primeira seção.*</span><span class="sxs-lookup"><span data-stu-id="bb792-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="bb792-178">O aplicativo já foi implantado a partir do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="bb792-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="bb792-179">Vamos usar as ferramentas integradas do Visual Studio para implantar uma atualização no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb792-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="bb792-180">Nos bastidores, o Visual Studio realiza a mesma coisa que a ferramenta de linha de comando, mas dentro da iU familiar do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bb792-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="bb792-181">Abra *simpleFeedReader.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bb792-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="bb792-182">No Solution Explorer, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb792-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="bb792-183">Alterar `<h2>Simple Feed Reader</h2>` para `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="bb792-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="bb792-184">Pressione **ctrl**+**shift**+**B** para construir o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb792-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="bb792-185">No Solution Explorer, clique com o botão direito do mouse no projeto e clique **em Publicar**.</span><span class="sxs-lookup"><span data-stu-id="bb792-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Captura de tela mostrando o clique direito, publicar](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="bb792-187">O Visual Studio pode criar um novo recurso de Serviço de Aplicativo, mas essa atualização será publicada durante a implantação existente.</span><span class="sxs-lookup"><span data-stu-id="bb792-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="bb792-188">Na **marcação Escolher uma** caixa de diálogo de destino de publicação, selecione Serviço de **aplicativo** na lista à esquerda e, em seguida, **selecione Selecionar 'Existir'.**</span><span class="sxs-lookup"><span data-stu-id="bb792-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="bb792-189">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="bb792-189">Click **Publish**.</span></span>
6. <span data-ttu-id="bb792-190">Na caixa de diálogo Serviço de **Aplicativo,** confirme se a conta Microsoft ou Organizacional usada para criar sua assinatura do Azure é exibida no canto superior direito.</span><span class="sxs-lookup"><span data-stu-id="bb792-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="bb792-191">Se não for, clique no drop-down e adicione-o.</span><span class="sxs-lookup"><span data-stu-id="bb792-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="bb792-192">Confirme se a **assinatura** correta do Azure está selecionada.</span><span class="sxs-lookup"><span data-stu-id="bb792-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="bb792-193">Para **Exibir,** selecione **Grupo de recursos**.</span><span class="sxs-lookup"><span data-stu-id="bb792-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="bb792-194">Expanda o grupo de recursos **do AzureTutorial** e selecione o aplicativo web existente.</span><span class="sxs-lookup"><span data-stu-id="bb792-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="bb792-195">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="bb792-195">Click **OK**.</span></span>

    ![Captura de tela mostrando a caixa de diálogo Do Serviço de Publicação do Aplicativo](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="bb792-197">O Visual Studio constrói e implanta o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="bb792-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="bb792-198">Navegue pela URL do aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="bb792-198">Browse to the web app URL.</span></span> <span data-ttu-id="bb792-199">Valide `<h2>` que a modificação do elemento está ao vivo.</span><span class="sxs-lookup"><span data-stu-id="bb792-199">Validate that the `<h2>` element modification is live.</span></span>

![O aplicativo com o título alterado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="bb792-201">Slots de implantação</span><span class="sxs-lookup"><span data-stu-id="bb792-201">Deployment slots</span></span>

<span data-ttu-id="bb792-202">Os slots de implantação suportam a realização de alterações sem afetar o aplicativo em execução na produção.</span><span class="sxs-lookup"><span data-stu-id="bb792-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="bb792-203">Uma vez que a versão encenada do aplicativo é validada por uma equipe de garantia de qualidade, os slots de produção e encenação podem ser trocados.</span><span class="sxs-lookup"><span data-stu-id="bb792-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="bb792-204">O aplicativo em encenação é promovido à produção desta forma.</span><span class="sxs-lookup"><span data-stu-id="bb792-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="bb792-205">As etapas seguintes criam um slot de preparação, implantam algumas alterações nele e trocam o slot de preparação com a produção após a verificação.</span><span class="sxs-lookup"><span data-stu-id="bb792-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="bb792-206">Faça login na [Azure Cloud Shell](https://shell.azure.com/bash), se ainda não estiver conectado.</span><span class="sxs-lookup"><span data-stu-id="bb792-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="bb792-207">Crie o slot de encenação.</span><span class="sxs-lookup"><span data-stu-id="bb792-207">Create the staging slot.</span></span>

    <span data-ttu-id="bb792-208">a.</span><span class="sxs-lookup"><span data-stu-id="bb792-208">a.</span></span> <span data-ttu-id="bb792-209">Crie um slot de implantação com o nome *encenando*.</span><span class="sxs-lookup"><span data-stu-id="bb792-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="bb792-210">b.</span><span class="sxs-lookup"><span data-stu-id="bb792-210">b.</span></span> <span data-ttu-id="bb792-211">Configure o slot de preparação para usar a implantação do Git local e obtenha a URL de implantação **de preparação.**</span><span class="sxs-lookup"><span data-stu-id="bb792-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="bb792-212">**Observe esta URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="bb792-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="bb792-213">c.</span><span class="sxs-lookup"><span data-stu-id="bb792-213">c.</span></span> <span data-ttu-id="bb792-214">Exibir a URL do slot de preparação.</span><span class="sxs-lookup"><span data-stu-id="bb792-214">Display the staging slot's URL.</span></span> <span data-ttu-id="bb792-215">Navegue até a URL para ver o slot de preparação vazio.</span><span class="sxs-lookup"><span data-stu-id="bb792-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="bb792-216">**Observe esta URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="bb792-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="bb792-217">Em um editor de texto ou visual studio, modifique *Páginas/Index.cshtml* novamente para que o `<h2>` elemento leia `<h2>Simple Feed Reader - V3</h2>` e salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="bb792-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="bb792-218">Comprometa o arquivo no repositório git local, usando a página **Alterações** na guia *Team Explorer* do Visual Studio ou inserindo o seguinte usando o shell de comando da máquina local:</span><span class="sxs-lookup"><span data-stu-id="bb792-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="bb792-219">Usando o shell de comando da máquina local, adicione a URL de implantação de preparação como um controle remoto Do Git e empurre as alterações comprometidas:</span><span class="sxs-lookup"><span data-stu-id="bb792-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="bb792-220">a.</span><span class="sxs-lookup"><span data-stu-id="bb792-220">a.</span></span> <span data-ttu-id="bb792-221">Adicione a URL remota para a preparação ao repositório git local.</span><span class="sxs-lookup"><span data-stu-id="bb792-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="bb792-222">b.</span><span class="sxs-lookup"><span data-stu-id="bb792-222">b.</span></span> <span data-ttu-id="bb792-223">Empurre o ramo *principal* local para *o* ramo *principal* do controle remoto.</span><span class="sxs-lookup"><span data-stu-id="bb792-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="bb792-224">Espere enquanto o Azure cria e implanta o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb792-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="bb792-225">Para verificar se o V3 foi implantado no slot de preparação, abra duas janelas do navegador.</span><span class="sxs-lookup"><span data-stu-id="bb792-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="bb792-226">Em uma janela, navegue até a URL original do aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="bb792-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="bb792-227">Na outra janela, navegue até a URL do aplicativo web de preparação.</span><span class="sxs-lookup"><span data-stu-id="bb792-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="bb792-228">A URL de produção serve V2 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb792-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="bb792-229">A URL de encenação serve V3 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb792-229">The staging URL serves V3 of the app.</span></span>

    ![Captura de tela comparando as janelas do navegador](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="bb792-231">Na Cloud Shell, troque o slot de preparação verificado/aquecido em produção.</span><span class="sxs-lookup"><span data-stu-id="bb792-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="bb792-232">Verifique se a troca ocorreu atualizando as duas janelas do navegador.</span><span class="sxs-lookup"><span data-stu-id="bb792-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Comparando as janelas do navegador após a troca](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="bb792-234">Resumo</span><span class="sxs-lookup"><span data-stu-id="bb792-234">Summary</span></span>

<span data-ttu-id="bb792-235">Nesta seção, foram concluídas as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="bb792-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="bb792-236">Baixei e construí o aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="bb792-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="bb792-237">Criei um aplicativo web do azure App usando o Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="bb792-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="bb792-238">Implantei o aplicativo de exemplo no Azure usando o Git.</span><span class="sxs-lookup"><span data-stu-id="bb792-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="bb792-239">Implantei uma alteração no aplicativo usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bb792-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="bb792-240">Adicionou um slot de preparação ao aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="bb792-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="bb792-241">Implantei uma atualização para o slot de preparação.</span><span class="sxs-lookup"><span data-stu-id="bb792-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="bb792-242">Troquei os slots de encenação e produção.</span><span class="sxs-lookup"><span data-stu-id="bb792-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="bb792-243">Na próxima seção, você aprenderá a construir um pipeline DevOps com a Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="bb792-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="bb792-244">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="bb792-244">Additional reading</span></span>

* [<span data-ttu-id="bb792-245">Visão geral de aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="bb792-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="bb792-246">Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="bb792-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="bb792-247">Configurar as credenciais de implantação do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="bb792-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="bb792-248">Configurar ambientes de preparo no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="bb792-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
