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
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="ab303-103">Implantar um aplicativo no serviço de aplicativo</span><span class="sxs-lookup"><span data-stu-id="ab303-103">Deploy an app to App Service</span></span>

<span data-ttu-id="ab303-104">[Azure app serviço](/azure/app-service/) é a plataforma de hospedagem na Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="ab303-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="ab303-105">Implantar um aplicativo Web em Azure App serviço pode ser feito manualmente ou por um processo automatizado.</span><span class="sxs-lookup"><span data-stu-id="ab303-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="ab303-106">Esta seção do guia discute os métodos de implantação que podem ser disparados manualmente ou por script usando a linha de comando ou disparados manualmente usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ab303-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="ab303-107">Nesta seção, você realizará as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="ab303-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="ab303-108">Baixe e compile o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ab303-108">Download and build the sample app.</span></span>
* <span data-ttu-id="ab303-109">Crie um aplicativo Web do serviço de Azure App usando o Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="ab303-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="ab303-110">Implante o aplicativo de exemplo no Azure usando o git.</span><span class="sxs-lookup"><span data-stu-id="ab303-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="ab303-111">Implante uma alteração no aplicativo usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ab303-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="ab303-112">Adicione um slot de preparo ao aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ab303-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="ab303-113">Implante uma atualização no slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="ab303-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="ab303-114">Alterne os slots de preparo e de produção.</span><span class="sxs-lookup"><span data-stu-id="ab303-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="ab303-115">Baixar e testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ab303-115">Download and test the app</span></span>

<span data-ttu-id="ab303-116">O aplicativo usado neste guia é um aplicativo de ASP.NET Core pré-compilado, leitor de [feeds simples](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="ab303-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="ab303-117">É um Razor aplicativo de páginas que usa a `Microsoft.SyndicationFeed.ReaderWriter` API para recuperar um feed RSS/Atom e exibir os itens de notícias em uma lista.</span><span class="sxs-lookup"><span data-stu-id="ab303-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="ab303-118">Sinta-se à vontade para examinar o código, mas é importante entender que não há nada de especial sobre esse aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="ab303-119">É apenas um aplicativo ASP.NET Core simples para fins ilustrativos.</span><span class="sxs-lookup"><span data-stu-id="ab303-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="ab303-120">Em um shell de comando, baixe o código, compile o projeto e execute-o da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="ab303-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="ab303-121">*Observação: os usuários do Linux/macOS devem fazer as alterações apropriadas para caminhos, por exemplo, usando `/` a barra () em vez da barra invertida ( `\` ).*</span><span class="sxs-lookup"><span data-stu-id="ab303-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="ab303-122">Clone o código em uma pasta no computador local.</span><span class="sxs-lookup"><span data-stu-id="ab303-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="ab303-123">Altere sua pasta de trabalho para a pasta de *leitor de feed simples* que foi criada.</span><span class="sxs-lookup"><span data-stu-id="ab303-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="ab303-124">Restaure os pacotes e Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="ab303-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="ab303-125">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![O comando dotnet Run foi bem-sucedido](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="ab303-127">Abra um navegador e navegue até `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ab303-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="ab303-128">O aplicativo permite digitar ou colar uma URL de feed de agregação e exibir uma lista de itens de notícias.</span><span class="sxs-lookup"><span data-stu-id="ab303-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![O aplicativo que exibe o conteúdo de um RSS feed](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="ab303-130">Quando estiver satisfeito, o aplicativo está funcionando corretamente, desative-o pressionando **Ctrl** + **C** no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="ab303-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="ab303-131">Criar o aplicativo Web do serviço de Azure App</span><span class="sxs-lookup"><span data-stu-id="ab303-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="ab303-132">Para implantar o aplicativo, você precisará criar um [aplicativo Web](/azure/app-service/app-service-web-overview)do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="ab303-133">Após a criação do aplicativo Web, você irá implantá-lo a partir de seu computador local usando o git.</span><span class="sxs-lookup"><span data-stu-id="ab303-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="ab303-134">Entrar no [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="ab303-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="ab303-135">Observação: quando você entra pela primeira vez, Cloud Shell solicita a criação de uma conta de armazenamento para arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="ab303-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="ab303-136">Aceite os padrões ou forneça um nome exclusivo.</span><span class="sxs-lookup"><span data-stu-id="ab303-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="ab303-137">Use o Cloud Shell para as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="ab303-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="ab303-138">a.</span><span class="sxs-lookup"><span data-stu-id="ab303-138">a.</span></span> <span data-ttu-id="ab303-139">Declare uma variável para armazenar o nome do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ab303-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="ab303-140">O nome deve ser exclusivo para ser usado na URL padrão.</span><span class="sxs-lookup"><span data-stu-id="ab303-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="ab303-141">O uso da `$RANDOM` função bash para construir o nome garante exclusividade e resulta no formato `webappname99999` .</span><span class="sxs-lookup"><span data-stu-id="ab303-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="ab303-142">b.</span><span class="sxs-lookup"><span data-stu-id="ab303-142">b.</span></span> <span data-ttu-id="ab303-143">Crie um grupos de recursos.</span><span class="sxs-lookup"><span data-stu-id="ab303-143">Create a resource group.</span></span> <span data-ttu-id="ab303-144">Os grupos de recursos fornecem um meio de agregar recursos do Azure a serem gerenciados como um grupo.</span><span class="sxs-lookup"><span data-stu-id="ab303-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="ab303-145">O `az` comando invoca o [CLI do Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="ab303-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="ab303-146">A CLI pode ser executada localmente, mas usá-la no Cloud Shell poupa tempo e configuração.</span><span class="sxs-lookup"><span data-stu-id="ab303-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="ab303-147">c.</span><span class="sxs-lookup"><span data-stu-id="ab303-147">c.</span></span> <span data-ttu-id="ab303-148">Crie um plano do serviço de aplicativo na camada S1.</span><span class="sxs-lookup"><span data-stu-id="ab303-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="ab303-149">Um plano do serviço de aplicativo é um agrupamento de aplicativos Web que compartilham o mesmo tipo de preço.</span><span class="sxs-lookup"><span data-stu-id="ab303-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="ab303-150">A camada S1 não é gratuita, mas é necessária para o recurso de Slots de preparo.</span><span class="sxs-lookup"><span data-stu-id="ab303-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="ab303-151">d.</span><span class="sxs-lookup"><span data-stu-id="ab303-151">d.</span></span> <span data-ttu-id="ab303-152">Crie o recurso de aplicativo Web usando o plano do serviço de aplicativo no mesmo grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="ab303-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="ab303-153">e.</span><span class="sxs-lookup"><span data-stu-id="ab303-153">e.</span></span> <span data-ttu-id="ab303-154">Defina as credenciais de implantação.</span><span class="sxs-lookup"><span data-stu-id="ab303-154">Set the deployment credentials.</span></span> <span data-ttu-id="ab303-155">Essas credenciais de implantação se aplicam a todos os aplicativos Web em sua assinatura.</span><span class="sxs-lookup"><span data-stu-id="ab303-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="ab303-156">Não use caracteres especiais no nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="ab303-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="ab303-157">f.</span><span class="sxs-lookup"><span data-stu-id="ab303-157">f.</span></span> <span data-ttu-id="ab303-158">Configure o aplicativo Web para aceitar implantações do git local e exibir a *URL de implantação do git*.</span><span class="sxs-lookup"><span data-stu-id="ab303-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="ab303-159">**Anote essa URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="ab303-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="ab303-160">g.</span><span class="sxs-lookup"><span data-stu-id="ab303-160">g.</span></span> <span data-ttu-id="ab303-161">Exiba a *URL do aplicativo Web*.</span><span class="sxs-lookup"><span data-stu-id="ab303-161">Display the *web app URL*.</span></span> <span data-ttu-id="ab303-162">Navegue até essa URL para ver o aplicativo Web em branco.</span><span class="sxs-lookup"><span data-stu-id="ab303-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="ab303-163">**Anote essa URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="ab303-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="ab303-164">Usando um shell de comando em seu computador local, navegue até a pasta do projeto do aplicativo Web (por exemplo, `.\simple-feed-reader\SimpleFeedReader` ).</span><span class="sxs-lookup"><span data-stu-id="ab303-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="ab303-165">Execute os comandos a seguir para configurar o Git para enviar por push para a URL de implantação:</span><span class="sxs-lookup"><span data-stu-id="ab303-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="ab303-166">a.</span><span class="sxs-lookup"><span data-stu-id="ab303-166">a.</span></span> <span data-ttu-id="ab303-167">Adicione a URL remota ao repositório local.</span><span class="sxs-lookup"><span data-stu-id="ab303-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="ab303-168">b.</span><span class="sxs-lookup"><span data-stu-id="ab303-168">b.</span></span> <span data-ttu-id="ab303-169">Envie por push o Branch *mestre* local para o Branch *mestre* do *Azure-prod* remoto.</span><span class="sxs-lookup"><span data-stu-id="ab303-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="ab303-170">Você será solicitado a fornecer as credenciais de implantação que criou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ab303-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="ab303-171">Observe a saída no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="ab303-171">Observe the output in the command shell.</span></span> <span data-ttu-id="ab303-172">O Azure cria o aplicativo ASP.NET Core remotamente.</span><span class="sxs-lookup"><span data-stu-id="ab303-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="ab303-173">Em um navegador, navegue até a *URL do aplicativo Web* e anote que o aplicativo foi compilado e implantado.</span><span class="sxs-lookup"><span data-stu-id="ab303-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="ab303-174">Alterações adicionais podem ser confirmadas no repositório git local com `git commit` .</span><span class="sxs-lookup"><span data-stu-id="ab303-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="ab303-175">Essas alterações são enviadas por push para o Azure com o `git push` comando anterior.</span><span class="sxs-lookup"><span data-stu-id="ab303-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="ab303-176">Implantação com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab303-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="ab303-177">*Observação: Esta seção aplica-se somente ao Windows. Os usuários do Linux e do macOS devem fazer a alteração descrita na etapa 2 abaixo. Salve o arquivo e confirme a alteração no repositório local com `git commit` . Por fim, envie por push a alteração com `git push` , como na primeira seção.*</span><span class="sxs-lookup"><span data-stu-id="ab303-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="ab303-178">O aplicativo já foi implantado a partir do Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="ab303-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="ab303-179">Vamos usar as ferramentas integradas do Visual Studio para implantar uma atualização para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="ab303-180">Nos bastidores, o Visual Studio realiza a mesma coisa que as ferramentas de linha de comando, mas dentro da interface do usuário familiar do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ab303-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="ab303-181">Abra *SimpleFeedReader. sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ab303-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="ab303-182">Em Gerenciador de Soluções, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ab303-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="ab303-183">Alterar `<h2>Simple Feed Reader</h2>` para `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="ab303-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="ab303-184">Pressione **Ctrl** + **Shift** + **B** para compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="ab303-185">Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **publicar**.</span><span class="sxs-lookup"><span data-stu-id="ab303-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Captura de tela mostrando clique com o botão direito do mouse, publicar](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="ab303-187">O Visual Studio pode criar um novo recurso do serviço de aplicativo, mas essa atualização será publicada na implantação existente.</span><span class="sxs-lookup"><span data-stu-id="ab303-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="ab303-188">Na caixa de diálogo **escolher um destino de publicação** , selecione **serviço de aplicativo** na lista à esquerda e selecione **selecionar existente**.</span><span class="sxs-lookup"><span data-stu-id="ab303-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="ab303-189">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="ab303-189">Click **Publish**.</span></span>
6. <span data-ttu-id="ab303-190">Na caixa de diálogo **serviço de aplicativo** , confirme se a conta da Microsoft ou da organização usada para criar sua assinatura do Azure é exibida no canto superior direito.</span><span class="sxs-lookup"><span data-stu-id="ab303-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="ab303-191">Se não estiver, clique na lista suspensa e adicione-a.</span><span class="sxs-lookup"><span data-stu-id="ab303-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="ab303-192">Confirme se a **assinatura** correta do Azure está selecionada.</span><span class="sxs-lookup"><span data-stu-id="ab303-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="ab303-193">Para **exibição**, selecione **grupo de recursos**.</span><span class="sxs-lookup"><span data-stu-id="ab303-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="ab303-194">Expanda o grupo de recursos **AzureTutorial** e selecione o aplicativo Web existente.</span><span class="sxs-lookup"><span data-stu-id="ab303-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="ab303-195">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="ab303-195">Click **OK**.</span></span>

    ![Captura de tela mostrando a caixa de diálogo Publicar serviço de aplicativo](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="ab303-197">O Visual Studio compila e implanta o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="ab303-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="ab303-198">Navegue até a URL do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ab303-198">Browse to the web app URL.</span></span> <span data-ttu-id="ab303-199">Valide se a `<h2>` modificação do elemento está ativa.</span><span class="sxs-lookup"><span data-stu-id="ab303-199">Validate that the `<h2>` element modification is live.</span></span>

![O aplicativo com o título alterado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="ab303-201">Slots de implantação</span><span class="sxs-lookup"><span data-stu-id="ab303-201">Deployment slots</span></span>

<span data-ttu-id="ab303-202">Os slots de implantação dão suporte à preparação de alterações sem afetar o aplicativo em execução na produção.</span><span class="sxs-lookup"><span data-stu-id="ab303-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="ab303-203">Depois que a versão em etapas do aplicativo for validada por uma equipe de garantia de qualidade, os slots de produção e de preparo poderão ser trocados.</span><span class="sxs-lookup"><span data-stu-id="ab303-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="ab303-204">O aplicativo em preparo é promovido para produção dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="ab303-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="ab303-205">As etapas a seguir criam um slot de preparo, implantam algumas alterações e trocam o slot de preparo com produção após a verificação.</span><span class="sxs-lookup"><span data-stu-id="ab303-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="ab303-206">Entre no [Azure cloud Shell](https://shell.azure.com/bash), se ainda não tiver feito logon.</span><span class="sxs-lookup"><span data-stu-id="ab303-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="ab303-207">Crie o slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="ab303-207">Create the staging slot.</span></span>

    <span data-ttu-id="ab303-208">a.</span><span class="sxs-lookup"><span data-stu-id="ab303-208">a.</span></span> <span data-ttu-id="ab303-209">Crie um slot de implantação com o nome de *preparo*.</span><span class="sxs-lookup"><span data-stu-id="ab303-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="ab303-210">b.</span><span class="sxs-lookup"><span data-stu-id="ab303-210">b.</span></span> <span data-ttu-id="ab303-211">Configure o slot de preparo para usar a implantação do git local e obtenha a URL de implantação de **preparo** .</span><span class="sxs-lookup"><span data-stu-id="ab303-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="ab303-212">**Anote essa URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="ab303-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="ab303-213">c.</span><span class="sxs-lookup"><span data-stu-id="ab303-213">c.</span></span> <span data-ttu-id="ab303-214">Exiba a URL do slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="ab303-214">Display the staging slot's URL.</span></span> <span data-ttu-id="ab303-215">Navegue até a URL para ver o slot de preparo vazio.</span><span class="sxs-lookup"><span data-stu-id="ab303-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="ab303-216">**Anote essa URL para referência mais tarde**.</span><span class="sxs-lookup"><span data-stu-id="ab303-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="ab303-217">Em um editor de texto ou no Visual Studio, modifique *pages/index. cshtml* novamente para que o `<h2>` elemento leia `<h2>Simple Feed Reader - V3</h2>` e salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="ab303-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="ab303-218">Confirme o arquivo no repositório git local, usando a página **alterações** na guia *Team Explorer* do Visual Studio ou digitando o seguinte usando o Shell de comando do computador local:</span><span class="sxs-lookup"><span data-stu-id="ab303-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="ab303-219">Usando o Shell de comando do computador local, adicione a URL de implantação de preparo como um git remoto e envie por push as alterações confirmadas:</span><span class="sxs-lookup"><span data-stu-id="ab303-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="ab303-220">a.</span><span class="sxs-lookup"><span data-stu-id="ab303-220">a.</span></span> <span data-ttu-id="ab303-221">Adicione a URL remota para preparo para o repositório git local.</span><span class="sxs-lookup"><span data-stu-id="ab303-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="ab303-222">b.</span><span class="sxs-lookup"><span data-stu-id="ab303-222">b.</span></span> <span data-ttu-id="ab303-223">Envie por push o Branch *mestre* local para o Branch *mestre* do *Azure-preparo* remoto.</span><span class="sxs-lookup"><span data-stu-id="ab303-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="ab303-224">Aguarde enquanto o Azure cria e implanta o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="ab303-225">Para verificar se o V3 foi implantado no slot de preparo, abra duas janelas do navegador.</span><span class="sxs-lookup"><span data-stu-id="ab303-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="ab303-226">Em uma janela, navegue até a URL do aplicativo Web original.</span><span class="sxs-lookup"><span data-stu-id="ab303-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="ab303-227">Na outra janela, navegue até a URL do aplicativo Web de preparo.</span><span class="sxs-lookup"><span data-stu-id="ab303-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="ab303-228">A URL de produção serve v2 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="ab303-229">A URL de preparo serve V3 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab303-229">The staging URL serves V3 of the app.</span></span>

    ![Captura de tela comparando as janelas do navegador](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="ab303-231">Na Cloud Shell, troque o slot de preparo verificado/quente em produção.</span><span class="sxs-lookup"><span data-stu-id="ab303-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="ab303-232">Verifique se a troca ocorreu atualizando as duas janelas do navegador.</span><span class="sxs-lookup"><span data-stu-id="ab303-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Comparando as janelas do navegador após a permuta](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="ab303-234">Resumo</span><span class="sxs-lookup"><span data-stu-id="ab303-234">Summary</span></span>

<span data-ttu-id="ab303-235">Nesta seção, as seguintes tarefas foram concluídas:</span><span class="sxs-lookup"><span data-stu-id="ab303-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="ab303-236">Baixado e criado o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ab303-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="ab303-237">Criou um aplicativo Web do serviço de Azure App usando o Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="ab303-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="ab303-238">O aplicativo de exemplo foi implantado no Azure usando o git.</span><span class="sxs-lookup"><span data-stu-id="ab303-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="ab303-239">Implantou uma alteração no aplicativo usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ab303-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="ab303-240">Um slot de preparo foi adicionado ao aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ab303-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="ab303-241">Implantou uma atualização para o slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="ab303-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="ab303-242">Trocados os slots de preparo e de produção.</span><span class="sxs-lookup"><span data-stu-id="ab303-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="ab303-243">Na próxima seção, você aprenderá a criar um pipeline do DevOps com o Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="ab303-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="ab303-244">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="ab303-244">Additional reading</span></span>

* [<span data-ttu-id="ab303-245">Visão geral dos aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="ab303-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="ab303-246">Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ab303-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="ab303-247">Configurar as credenciais de implantação do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ab303-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="ab303-248">Configurar ambientes de preparo no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ab303-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
