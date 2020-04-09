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
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="b134a-103">Integração e implantação contínuas</span><span class="sxs-lookup"><span data-stu-id="b134a-103">Continuous integration and deployment</span></span>

<span data-ttu-id="b134a-104">No capítulo anterior, você criou um repositório git local para o aplicativo Simple Feed Reader.</span><span class="sxs-lookup"><span data-stu-id="b134a-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="b134a-105">Neste capítulo, você publicará esse código em um repositório do GitHub e construirá um pipeline azure DevOps Services usando o Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="b134a-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="b134a-106">O pipeline permite compilações e implantações contínuas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b134a-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="b134a-107">Qualquer compromisso com o repositório do GitHub desencadeia uma compilação e uma implantação no slot de preparação do Azure Web App.</span><span class="sxs-lookup"><span data-stu-id="b134a-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="b134a-108">Nesta seção, você completará as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="b134a-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="b134a-109">Publique o código do aplicativo no GitHub</span><span class="sxs-lookup"><span data-stu-id="b134a-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="b134a-110">Desconectar a implantação local do Git</span><span class="sxs-lookup"><span data-stu-id="b134a-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="b134a-111">Criar uma organização do Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="b134a-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="b134a-112">Crie um projeto de equipe nos Serviços Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="b134a-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="b134a-113">Criar a definição de build</span><span class="sxs-lookup"><span data-stu-id="b134a-113">Create a build definition</span></span>
* <span data-ttu-id="b134a-114">Criar um pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="b134a-114">Create a release pipeline</span></span>
* <span data-ttu-id="b134a-115">Confirmar alterações no GitHub e implantar automaticamente no Azure</span><span class="sxs-lookup"><span data-stu-id="b134a-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="b134a-116">Examine o pipeline da Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="b134a-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="b134a-117">Publique o código do aplicativo no GitHub</span><span class="sxs-lookup"><span data-stu-id="b134a-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="b134a-118">Abra uma janela do `https://github.com`navegador e navegue para .</span><span class="sxs-lookup"><span data-stu-id="b134a-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="b134a-119">Clique **+** na queda no cabeçalho e selecione **Novo repositório**:</span><span class="sxs-lookup"><span data-stu-id="b134a-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Opção de Novo Repositório do GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="b134a-121">Selecione sua conta na **ativada do Proprietário** e digite o *leitor de feed simples* na caixa de texto nome do **Repositório.**</span><span class="sxs-lookup"><span data-stu-id="b134a-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="b134a-122">Clique no botão **Criar repositório.**</span><span class="sxs-lookup"><span data-stu-id="b134a-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="b134a-123">Abra o projétil de comando da máquina local.</span><span class="sxs-lookup"><span data-stu-id="b134a-123">Open your local machine's command shell.</span></span> <span data-ttu-id="b134a-124">Navegue até o diretório no qual o repositório Git *de leitor de simples* alimentação é armazenado.</span><span class="sxs-lookup"><span data-stu-id="b134a-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="b134a-125">Renomeie o controle remoto de *origem* existente para *upstream*.</span><span class="sxs-lookup"><span data-stu-id="b134a-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="b134a-126">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="b134a-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="b134a-127">Adicione um novo controle remoto *de origem* apontando para sua cópia do repositório no GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="b134a-128">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="b134a-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="b134a-129">Publique seu repositório git local para o repositório GitHub recém-criado.</span><span class="sxs-lookup"><span data-stu-id="b134a-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="b134a-130">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="b134a-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="b134a-131">Abra uma janela do `https://github.com/<GitHub_username>/simple-feed-reader/`navegador e navegue para .</span><span class="sxs-lookup"><span data-stu-id="b134a-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="b134a-132">Valide se seu código aparece no repositório do GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="b134a-133">Desconectar a implantação local do Git</span><span class="sxs-lookup"><span data-stu-id="b134a-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="b134a-134">Remova a implantação local do Git com as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="b134a-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="b134a-135">O Azure Pipelines (um serviço Azure DevOps) substitui e aumenta essa funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="b134a-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="b134a-136">Abra o [portal Azure](https://portal.azure.com/)e navegue até o *\<webapp (mywebapp unique_number/staging)\>* Web App.</span><span class="sxs-lookup"><span data-stu-id="b134a-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="b134a-137">O Web App pode ser rapidamente localizado inserindo a *encenação* na caixa de pesquisa do portal:</span><span class="sxs-lookup"><span data-stu-id="b134a-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![encenando o termo de pesquisa do Web App](media/cicd/portal-search-box.png)

1. <span data-ttu-id="b134a-139">Clique **em Centro de implantação**.</span><span class="sxs-lookup"><span data-stu-id="b134a-139">Click **Deployment Center**.</span></span> <span data-ttu-id="b134a-140">Um novo painel aparece.</span><span class="sxs-lookup"><span data-stu-id="b134a-140">A new panel appears.</span></span> <span data-ttu-id="b134a-141">Clique **em Desconectar** para remover a configuração local de controle de origem do Git adicionada no capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="b134a-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="b134a-142">Confirme a operação de remoção clicando no botão **Sim.**</span><span class="sxs-lookup"><span data-stu-id="b134a-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="b134a-143">Navegue até o *mywebapp<unique_number>* App Service.</span><span class="sxs-lookup"><span data-stu-id="b134a-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="b134a-144">Como lembrete, a caixa de pesquisa do portal pode ser usada para localizar rapidamente o Serviço de Aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b134a-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="b134a-145">Clique **em Centro de implantação**.</span><span class="sxs-lookup"><span data-stu-id="b134a-145">Click **Deployment Center**.</span></span> <span data-ttu-id="b134a-146">Um novo painel aparece.</span><span class="sxs-lookup"><span data-stu-id="b134a-146">A new panel appears.</span></span> <span data-ttu-id="b134a-147">Clique **em Desconectar** para remover a configuração local de controle de origem do Git adicionada no capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="b134a-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="b134a-148">Confirme a operação de remoção clicando no botão **Sim.**</span><span class="sxs-lookup"><span data-stu-id="b134a-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="b134a-149">Criar uma organização do Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="b134a-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="b134a-150">Abra um navegador e navegue até a página de criação da [organização Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="b134a-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="b134a-151">Digite um nome exclusivo na Caixa de texto **Escolha um nome memorável** para formar a URL para acessar sua organização Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="b134a-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="b134a-152">Selecione o botão de rádio **Git,** já que o código está hospedado em um repositório do GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="b134a-153">Clique no botão **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-153">Click the **Continue** button.</span></span> <span data-ttu-id="b134a-154">Depois de uma pequena espera, uma conta e um projeto de equipe, chamado *MyFirstProject,* são criados.</span><span class="sxs-lookup"><span data-stu-id="b134a-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Página de criação da organização Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="b134a-156">Abra o e-mail de confirmação indicando que a organização e o projeto Azure DevOps estão prontos para uso.</span><span class="sxs-lookup"><span data-stu-id="b134a-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="b134a-157">Clique no botão **Iniciar seu projeto:**</span><span class="sxs-lookup"><span data-stu-id="b134a-157">Click the **Start your project** button:</span></span>

    ![Inicie o botão do projeto](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="b134a-159">Um navegador é aberto para \* \<\>account_name .visualstudio.com\*.</span><span class="sxs-lookup"><span data-stu-id="b134a-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="b134a-160">Clique no link *MyFirstProject* para começar a configurar o pipeline DevOps do projeto.</span><span class="sxs-lookup"><span data-stu-id="b134a-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="b134a-161">Configure o pipeline da Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="b134a-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="b134a-162">Há três passos distintos para completar.</span><span class="sxs-lookup"><span data-stu-id="b134a-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="b134a-163">Completar as etapas nas três seções seguintes resulta em um pipeline de DevOps operacional.</span><span class="sxs-lookup"><span data-stu-id="b134a-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="b134a-164">Grant Azure DevOps acesso ao repositório GitHub</span><span class="sxs-lookup"><span data-stu-id="b134a-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="b134a-165">Expanda o **código ou a construção a partir de um acordeom de repositório externo.**</span><span class="sxs-lookup"><span data-stu-id="b134a-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="b134a-166">Clique no botão **Configurar construir:**</span><span class="sxs-lookup"><span data-stu-id="b134a-166">Click the **Setup Build** button:</span></span>

    ![Botão de compilação de configuração](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="b134a-168">Selecione a opção **GitHub** na **seção Selecionar uma** fonte:</span><span class="sxs-lookup"><span data-stu-id="b134a-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Selecione uma fonte - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="b134a-170">A autorização é necessária antes que o Azure DevOps possa acessar seu repositório gitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="b134a-171">Digite *<GitHub_username> conexão GitHub* na caixa de texto **nome conexão.**</span><span class="sxs-lookup"><span data-stu-id="b134a-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="b134a-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b134a-172">For example:</span></span>

    ![Nome de conexão GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="b134a-174">Se a autenticação de dois fatores estiver ativada em sua conta do GitHub, um token de acesso pessoal será necessário.</span><span class="sxs-lookup"><span data-stu-id="b134a-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="b134a-175">Nesse caso, clique no Autorizar com um link **de token de acesso pessoal do GitHub.**</span><span class="sxs-lookup"><span data-stu-id="b134a-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="b134a-176">Consulte as [instruções oficiais de criação de tokens de acesso pessoal do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obter ajuda.</span><span class="sxs-lookup"><span data-stu-id="b134a-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="b134a-177">Apenas o escopo de *relo* de permissões é necessário.</span><span class="sxs-lookup"><span data-stu-id="b134a-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="b134a-178">Caso contrário, clique no **botão Autorizar usando OAuth.**</span><span class="sxs-lookup"><span data-stu-id="b134a-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="b134a-179">Quando solicitado, faça login na sua conta do GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="b134a-180">Em seguida, selecione Autorizar para conceder acesso à sua organização Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="b134a-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="b134a-181">Se for bem-sucedido, um novo ponto final de serviço será criado.</span><span class="sxs-lookup"><span data-stu-id="b134a-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="b134a-182">Clique no botão elipse ao lado do botão **Repositório.**</span><span class="sxs-lookup"><span data-stu-id="b134a-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="b134a-183">Selecione o repositório *<GitHub_username>/simples da* lista.</span><span class="sxs-lookup"><span data-stu-id="b134a-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="b134a-184">Clique no botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="b134a-185">Selecione o ramo *mestre* no **ramo Padrão para que as compilações manuais e programadas** se abaixem.</span><span class="sxs-lookup"><span data-stu-id="b134a-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="b134a-186">Clique no botão **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-186">Click the **Continue** button.</span></span> <span data-ttu-id="b134a-187">A página de seleção de modelos é exibida.</span><span class="sxs-lookup"><span data-stu-id="b134a-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="b134a-188">Crie a definição de compilação</span><span class="sxs-lookup"><span data-stu-id="b134a-188">Create the build definition</span></span>

1. <span data-ttu-id="b134a-189">Na página de seleção do modelo, digite *ASP.NET Core* na caixa de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="b134a-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![pesquisa ASP.NET Core na página do modelo](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="b134a-191">Os resultados da pesquisa do modelo aparecem.</span><span class="sxs-lookup"><span data-stu-id="b134a-191">The template search results appear.</span></span> <span data-ttu-id="b134a-192">Passar o mouse sobre o modelo **ASP.NET Core** e clique no botão **Aplicar.**</span><span class="sxs-lookup"><span data-stu-id="b134a-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="b134a-193">A guia **Tarefas** da definição de compilação é exibida.</span><span class="sxs-lookup"><span data-stu-id="b134a-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="b134a-194">Clique na guia **Gatilhos.**</span><span class="sxs-lookup"><span data-stu-id="b134a-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="b134a-195">Verifique a **caixa Ativar integração contínua.**</span><span class="sxs-lookup"><span data-stu-id="b134a-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="b134a-196">Na seção **'Ramificação' filtros,** confirme se **a** lista de itens de isto está definida como *Incluir*.</span><span class="sxs-lookup"><span data-stu-id="b134a-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="b134a-197">Defina a **especificação de ramificação** para baixo como *mestre*.</span><span class="sxs-lookup"><span data-stu-id="b134a-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Habilite configurações de integração contínua](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="b134a-199">Essas configurações fazem com que uma compilação seja acionada quando qualquer alteração é empurrada para o ramo *principal* do repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="b134a-200">A integração contínua é testada nas alterações do Commit no GitHub e implantada automaticamente na seção [Azure.](#commit-changes-to-github-and-automatically-deploy-to-azure)</span><span class="sxs-lookup"><span data-stu-id="b134a-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="b134a-201">Clique no botão **Salvar & fila** e selecione a opção **Salvar:**</span><span class="sxs-lookup"><span data-stu-id="b134a-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Botão Salvar](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="b134a-203">A seguinte diálogo modal aparece:</span><span class="sxs-lookup"><span data-stu-id="b134a-203">The following modal dialog appears:</span></span>

    ![Salvar definição de construção - diálogo modal](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="b134a-205">Use a pasta *\\*padrão de e clique no botão **Salvar.**</span><span class="sxs-lookup"><span data-stu-id="b134a-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="b134a-206">Criar o pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="b134a-206">Create the release pipeline</span></span>

1. <span data-ttu-id="b134a-207">Clique na guia **Lançamentos** do projeto da sua equipe.</span><span class="sxs-lookup"><span data-stu-id="b134a-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="b134a-208">Clique no botão **Novo pipeline.**</span><span class="sxs-lookup"><span data-stu-id="b134a-208">Click the **New pipeline** button.</span></span>

    ![Guia de lançamentos - Novo botão de definição](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="b134a-210">O painel de seleção do modelo é exibido.</span><span class="sxs-lookup"><span data-stu-id="b134a-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="b134a-211">Na página de seleção do modelo, digite *O Serviço de Aplicativo* na caixa de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="b134a-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Liberar caixa de pesquisa do modelo de pipeline](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="b134a-213">Os resultados da pesquisa do modelo aparecem.</span><span class="sxs-lookup"><span data-stu-id="b134a-213">The template search results appear.</span></span> <span data-ttu-id="b134a-214">Passar o mouse sobre a implantação do serviço de aplicativo do **Azure com** o modelo Slot e clique no botão **Aplicar.**</span><span class="sxs-lookup"><span data-stu-id="b134a-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="b134a-215">A guia **Pipeline** do pipeline de liberação é exibida.</span><span class="sxs-lookup"><span data-stu-id="b134a-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Liberar a guia pipeline pipeline](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="b134a-217">Clique no botão **Adicionar** na caixa **Artefatos.**</span><span class="sxs-lookup"><span data-stu-id="b134a-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="b134a-218">O **painel Adicionar artefato** é exibido:</span><span class="sxs-lookup"><span data-stu-id="b134a-218">The **Add artifact** panel appears:</span></span>

    ![Gasoduto de liberação - Adicionar painel de artefatos](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="b134a-220">Selecione o bloco **'Construir'** na seção **Tipo de origem.**</span><span class="sxs-lookup"><span data-stu-id="b134a-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="b134a-221">Este tipo permite a vinculação do pipeline de liberação à definição de compilação.</span><span class="sxs-lookup"><span data-stu-id="b134a-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="b134a-222">Selecione *MyFirstProject* na parada do **Projeto.**</span><span class="sxs-lookup"><span data-stu-id="b134a-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="b134a-223">Selecione o nome da definição de compilação, *MyFirstProject-ASP.NET Core-CI*, a partir da **parada de origem (definição de compilação).**</span><span class="sxs-lookup"><span data-stu-id="b134a-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="b134a-224">Selecione *Mais recente* na **versão padrão.**</span><span class="sxs-lookup"><span data-stu-id="b134a-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="b134a-225">Esta opção constrói os artefatos produzidos pela última execução da definição de construção.</span><span class="sxs-lookup"><span data-stu-id="b134a-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="b134a-226">Substitua o texto na caixa de texto de código de texto **da fonte alias** *por Drop*.</span><span class="sxs-lookup"><span data-stu-id="b134a-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="b134a-227">Clique no botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-227">Click the **Add** button.</span></span> <span data-ttu-id="b134a-228">A seção **Artefatos** atualiza-se para exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="b134a-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="b134a-229">Clique no ícone do relâmpago para ativar implantações contínuas:</span><span class="sxs-lookup"><span data-stu-id="b134a-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Lançar artefatos do gasoduto - ícone de relâmpago](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="b134a-231">Com essa opção ativada, uma implantação ocorre cada vez que uma nova compilação está disponível.</span><span class="sxs-lookup"><span data-stu-id="b134a-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="b134a-232">Um **painel de gatilho de implantação contínua** aparece à direita.</span><span class="sxs-lookup"><span data-stu-id="b134a-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="b134a-233">Clique no botão alternar para ativar o recurso.</span><span class="sxs-lookup"><span data-stu-id="b134a-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="b134a-234">Não é necessário ativar o gatilho de **solicitação de puxar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="b134a-235">Clique em **Adicionar** a lista de filtros de **ramificação Build.**</span><span class="sxs-lookup"><span data-stu-id="b134a-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="b134a-236">Escolha a opção **de ramificação padrão da Configuração de compilação.**</span><span class="sxs-lookup"><span data-stu-id="b134a-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="b134a-237">Este filtro faz com que a liberação seja acionada apenas para uma compilação a partir do ramo *principal* do repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="b134a-238">Clique no botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-238">Click the **Save** button.</span></span> <span data-ttu-id="b134a-239">Clique no botão **OK** na caixa de diálogo modal **Salvar** resultante.</span><span class="sxs-lookup"><span data-stu-id="b134a-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="b134a-240">Clique na caixa **Ambiente 1.**</span><span class="sxs-lookup"><span data-stu-id="b134a-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="b134a-241">Um **painel de ambiente** aparece à direita.</span><span class="sxs-lookup"><span data-stu-id="b134a-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="b134a-242">Alterar o *texto 'Ambiente 1'* no **nome ambiente** caixa de texto para *Produção*.</span><span class="sxs-lookup"><span data-stu-id="b134a-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Pipeline de liberação - Caixa de texto nome do ambiente](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="b134a-244">Clique na **fase 1, 2 tarefas** link na caixa **Produção:**</span><span class="sxs-lookup"><span data-stu-id="b134a-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Gasoduto de liberação - Link ambiente de produção.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="b134a-246">A guia **Tarefas** do ambiente é exibida.</span><span class="sxs-lookup"><span data-stu-id="b134a-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="b134a-247">Clique na **tarefa Implantar serviço de aplicativo do Azure para slot.**</span><span class="sxs-lookup"><span data-stu-id="b134a-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="b134a-248">Suas configurações aparecem em um painel à direita.</span><span class="sxs-lookup"><span data-stu-id="b134a-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="b134a-249">Selecione a assinatura do Azure associada ao Serviço de Aplicativo a partir da entrada de assinatura do **Azure.**</span><span class="sxs-lookup"><span data-stu-id="b134a-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="b134a-250">Uma vez selecionado, clique no botão **Autorizar.**</span><span class="sxs-lookup"><span data-stu-id="b134a-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="b134a-251">Selecione *o Aplicativo* da Web a partir do tipo de **aplicativo** parado.</span><span class="sxs-lookup"><span data-stu-id="b134a-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="b134a-252">Selecione *mywebapp/<unique_number/>* na parada de nome do serviço do **aplicativo.**</span><span class="sxs-lookup"><span data-stu-id="b134a-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="b134a-253">Selecione *AzureTutorial* no **grupo de** recursos parado.</span><span class="sxs-lookup"><span data-stu-id="b134a-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="b134a-254">Selecione *a encenação* na estada de **slot.**</span><span class="sxs-lookup"><span data-stu-id="b134a-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="b134a-255">Clique no botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="b134a-256">Passar o passar por cima do nome do pipeline de liberação padrão.</span><span class="sxs-lookup"><span data-stu-id="b134a-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="b134a-257">Clique no ícone do lápis para editá-lo.</span><span class="sxs-lookup"><span data-stu-id="b134a-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="b134a-258">Use *MyFirstProject-ASP.NET Core-CD* como nome.</span><span class="sxs-lookup"><span data-stu-id="b134a-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nome do gasoduto de liberação](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="b134a-260">Clique no botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="b134a-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="b134a-261">Confirmar alterações no GitHub e implantar automaticamente no Azure</span><span class="sxs-lookup"><span data-stu-id="b134a-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="b134a-262">Abra *simpleFeedReader.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b134a-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="b134a-263">No Solution Explorer, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b134a-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="b134a-264">Alterar `<h2>Simple Feed Reader - V3</h2>` para `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="b134a-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="b134a-265">Pressione **ctrl**+**shift**+**B** para construir o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b134a-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="b134a-266">Compreenda o arquivo no repositório do GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="b134a-267">Use a página **Alterações** na guia Explorador de *equipe* do Visual Studio ou execute o seguinte usando o shell de comando da máquina local:</span><span class="sxs-lookup"><span data-stu-id="b134a-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="b134a-268">Empurre a alteração no ramo *mestre* para o controle remoto de *origem* do seu repositório GitHub:</span><span class="sxs-lookup"><span data-stu-id="b134a-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="b134a-269">O commit aparece no ramo *principal* do repositório GitHub:</span><span class="sxs-lookup"><span data-stu-id="b134a-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![GitHub se compromete em ramo mestre](media/cicd/github-commit.png)

    <span data-ttu-id="b134a-271">A compilação é acionada, uma vez que a integração contínua está ativada na guia **Gatilhos** da definição de compilação:</span><span class="sxs-lookup"><span data-stu-id="b134a-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![permitir a integração contínua](media/cicd/enable-ci.png)

1. <span data-ttu-id="b134a-273">Navegue até a guia **Enfileirada** da página >  **Builds do Azure Pipelines**nos**Serviços** Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="b134a-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="b134a-274">A compilação enfileirada mostra o ramo e o compromisso que desencadeou a compilação:</span><span class="sxs-lookup"><span data-stu-id="b134a-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![construção enfileirada](media/cicd/build-queued.png)

1. <span data-ttu-id="b134a-276">Uma vez que a compilação é bem sucedida, uma implantação no Azure ocorre.</span><span class="sxs-lookup"><span data-stu-id="b134a-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="b134a-277">Navegue até o aplicativo no navegador.</span><span class="sxs-lookup"><span data-stu-id="b134a-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="b134a-278">Observe que o texto "V4" aparece no título:</span><span class="sxs-lookup"><span data-stu-id="b134a-278">Notice that the "V4" text appears in the heading:</span></span>

    ![aplicativo atualizado](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="b134a-280">Examine o pipeline da Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="b134a-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="b134a-281">Definição da compilação</span><span class="sxs-lookup"><span data-stu-id="b134a-281">Build definition</span></span>

<span data-ttu-id="b134a-282">Uma definição de compilação foi criada com o nome *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="b134a-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="b134a-283">Após a conclusão, a compilação produz um arquivo *.zip,* incluindo os ativos a serem publicados.</span><span class="sxs-lookup"><span data-stu-id="b134a-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="b134a-284">O oleoduto de liberação implanta esses ativos no Azure.</span><span class="sxs-lookup"><span data-stu-id="b134a-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="b134a-285">A guia **Tarefas** da definição de compilação lista as etapas individuais que estão sendo usadas.</span><span class="sxs-lookup"><span data-stu-id="b134a-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="b134a-286">Há cinco tarefas de construção.</span><span class="sxs-lookup"><span data-stu-id="b134a-286">There are five build tasks.</span></span>

![construir tarefas de definição](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="b134a-288">**Restaurar** &mdash; Executa `dotnet restore` o comando para restaurar os pacotes NuGet do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b134a-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="b134a-289">A alimentação padrão do pacote usada é nuget.org.</span><span class="sxs-lookup"><span data-stu-id="b134a-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="b134a-290">**Build** &mdash; Executa `dotnet build --configuration release` o comando para compilar o código do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b134a-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="b134a-291">Esta `--configuration` opção é usada para produzir uma versão otimizada do código, que é adequada para implantação em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="b134a-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="b134a-292">Modifique a variável *BuildConfiguration* na guia **Variáveis** da definição de compilação se, por exemplo, uma configuração de depuração for necessária.</span><span class="sxs-lookup"><span data-stu-id="b134a-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="b134a-293">**Teste** &mdash; Executa `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` o comando para executar os testes unitários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b134a-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="b134a-294">Os testes unitários são executados `**/*Tests/*.csproj` dentro de qualquer projeto C# que corresponda ao padrão glob.</span><span class="sxs-lookup"><span data-stu-id="b134a-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="b134a-295">Os resultados do teste são salvos em um `--results-directory` arquivo *.trx* no local especificado pela opção.</span><span class="sxs-lookup"><span data-stu-id="b134a-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="b134a-296">Se algum teste falhar, a compilação falha e não é implantada.</span><span class="sxs-lookup"><span data-stu-id="b134a-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b134a-297">Para verificar o trabalho dos testes unitários, modifique *o SimpleFeedReader.Tests\Services\NewsServiceTests.cs* para quebrar propositalmente um dos testes.</span><span class="sxs-lookup"><span data-stu-id="b134a-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="b134a-298">Por exemplo, `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` mude `Returns_News_Stories_Given_Valid_Uri` para no método.</span><span class="sxs-lookup"><span data-stu-id="b134a-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="b134a-299">Comprometa e empurre a mudança para o GitHub.</span><span class="sxs-lookup"><span data-stu-id="b134a-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="b134a-300">A construção é acionada e falha.</span><span class="sxs-lookup"><span data-stu-id="b134a-300">The build is triggered and fails.</span></span> <span data-ttu-id="b134a-301">O status do pipeline de construção muda para **falha**.</span><span class="sxs-lookup"><span data-stu-id="b134a-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="b134a-302">Reverter a mudança, comprometer e empurrar novamente.</span><span class="sxs-lookup"><span data-stu-id="b134a-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="b134a-303">A construção é bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="b134a-303">The build succeeds.</span></span>

1. <span data-ttu-id="b134a-304">**Publicar** &mdash; Executa `dotnet publish --configuration release --output <local_path_on_build_agent>` o comando para produzir um arquivo *.zip* com os artefatos a serem implantados.</span><span class="sxs-lookup"><span data-stu-id="b134a-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="b134a-305">A `--output` opção especifica o local de publicação do arquivo *.zip.*</span><span class="sxs-lookup"><span data-stu-id="b134a-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="b134a-306">Esse local é especificado passando uma `$(build.artifactstagingdirectory)` [variável predefinida](/azure/devops/pipelines/build/variables) chamada .</span><span class="sxs-lookup"><span data-stu-id="b134a-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="b134a-307">Essa variável expande-se para um caminho local, como *c:\agent\_work\1\a*, no agente de compilação.</span><span class="sxs-lookup"><span data-stu-id="b134a-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="b134a-308">**Publicar artefato** &mdash; Publica o arquivo *.zip* produzido pela tarefa **Publicar.**</span><span class="sxs-lookup"><span data-stu-id="b134a-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="b134a-309">A tarefa aceita a localização do arquivo *.zip* como parâmetro, que é a variável `$(build.artifactstagingdirectory)`predefinida .</span><span class="sxs-lookup"><span data-stu-id="b134a-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="b134a-310">O arquivo *.zip* é publicado como uma pasta chamada *drop*.</span><span class="sxs-lookup"><span data-stu-id="b134a-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="b134a-311">Clique no link **Resumo** da definição de compilação para exibir um histórico de compilações com a definição:</span><span class="sxs-lookup"><span data-stu-id="b134a-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Captura de tela mostrando o histórico de definição de compilação](media/cicd/build-definition-summary.png)

<span data-ttu-id="b134a-313">Na página resultante, clique no link correspondente ao número de compilação exclusivo:</span><span class="sxs-lookup"><span data-stu-id="b134a-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Captura de tela mostrando página de resumo de definição de compilação](media/cicd/build-definition-completed.png)

<span data-ttu-id="b134a-315">Um resumo desta compilação específica é exibido.</span><span class="sxs-lookup"><span data-stu-id="b134a-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="b134a-316">Clique na guia **Artefatos** e observe que a pasta *de queda* produzida pela compilação está listada:</span><span class="sxs-lookup"><span data-stu-id="b134a-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Captura de tela mostrando artefatos de definição de construção - pasta de queda](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="b134a-318">Use os links **Download** e **Explore** para inspecionar os artefatos publicados.</span><span class="sxs-lookup"><span data-stu-id="b134a-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="b134a-319">Pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="b134a-319">Release pipeline</span></span>

<span data-ttu-id="b134a-320">Um pipeline de lançamento foi criado com o nome *MyFirstProject-ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="b134a-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Captura de tela mostrando visão geral do pipeline de liberação](media/cicd/release-definition-overview.png)

<span data-ttu-id="b134a-322">Os dois principais componentes do gasoduto de liberação são os **Artefatos** e os **Ambientes.**</span><span class="sxs-lookup"><span data-stu-id="b134a-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="b134a-323">Clicar na caixa na seção **Artefatos** revela o seguinte painel:</span><span class="sxs-lookup"><span data-stu-id="b134a-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Captura de tela mostrando artefatos de tubulação de liberação](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="b134a-325">O valor **Source (Build definition)** representa a definição de compilação à qual este pipeline de versão está vinculado.</span><span class="sxs-lookup"><span data-stu-id="b134a-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="b134a-326">O arquivo *.zip* produzido por uma execução bem-sucedida da definição de compilação é fornecido ao ambiente *De produção* para implantação no Azure.</span><span class="sxs-lookup"><span data-stu-id="b134a-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="b134a-327">Clique na *fase 1, 2 tarefas* link na caixa ambiente *Produção* para visualizar as tarefas do pipeline de liberação:</span><span class="sxs-lookup"><span data-stu-id="b134a-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Captura de tela mostrando tarefas de pipeline de liberação](media/cicd/release-definition-tasks.png)

<span data-ttu-id="b134a-329">O pipeline de lançamento consiste em duas tarefas: *Implantar o Serviço de Aplicativos Azure para slot* e gerenciar o serviço de aplicativos do *Azure - Slot Swap*.</span><span class="sxs-lookup"><span data-stu-id="b134a-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="b134a-330">Clicar na primeira tarefa revela a seguinte configuração de tarefa:</span><span class="sxs-lookup"><span data-stu-id="b134a-330">Clicking the first task reveals the following task configuration:</span></span>

![Captura de tela mostrando a tarefa de implantação do pipeline de lançamento](media/cicd/release-definition-task1.png)

<span data-ttu-id="b134a-332">A assinatura do Azure, o tipo de serviço, o nome do aplicativo da Web, o grupo de recursos e o slot de implantação são definidos na tarefa de implantação.</span><span class="sxs-lookup"><span data-stu-id="b134a-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="b134a-333">A caixa de texto **'Pacote ou pasta'** contém o caminho do arquivo *.zip* a ser extraído e implantado no slot de *preparação* do \*aplicativo webapp\<unique_number\> \* web.</span><span class="sxs-lookup"><span data-stu-id="b134a-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="b134a-334">Clicar na tarefa de troca de slot revela a seguinte configuração de tarefa:</span><span class="sxs-lookup"><span data-stu-id="b134a-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Captura de tela mostrando a tarefa de troca de slot do pipeline de liberação](media/cicd/release-definition-task2.png)

<span data-ttu-id="b134a-336">Os detalhes de assinatura, grupo de recursos, tipo de serviço, nome do aplicativo web e detalhes do slot de implantação são fornecidos.</span><span class="sxs-lookup"><span data-stu-id="b134a-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="b134a-337">A caixa de seleção **Swap with Production** é verificada.</span><span class="sxs-lookup"><span data-stu-id="b134a-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="b134a-338">Consequentemente, os bits implantados no slot *de encenação* são trocados para o ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="b134a-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="b134a-339">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="b134a-339">Additional reading</span></span>

* [<span data-ttu-id="b134a-340">Criar seu primeiro pipeline com o Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="b134a-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="b134a-341">Projeto Build e .NET Core</span><span class="sxs-lookup"><span data-stu-id="b134a-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="b134a-342">Implantar um aplicativo web com a Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="b134a-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
