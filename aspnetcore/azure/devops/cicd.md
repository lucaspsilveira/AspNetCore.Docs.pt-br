---
title: Integração e implantação contínua-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Integração e implantação contínuas no DevOps com o ASP.NET Core e o Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/cicd
ms.openlocfilehash: 0edded18d766d6f2af08f6be5dbecbfd52a14a35
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400551"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="af02b-103">Integração e implantação contínuas</span><span class="sxs-lookup"><span data-stu-id="af02b-103">Continuous integration and deployment</span></span>

<span data-ttu-id="af02b-104">No capítulo anterior, você criou um repositório git local para o aplicativo leitor de feed simples.</span><span class="sxs-lookup"><span data-stu-id="af02b-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="af02b-105">Neste capítulo, você publicará esse código em um repositório GitHub e construirá um pipeline de Azure DevOps Services usando Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="af02b-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="af02b-106">O pipeline permite compilações e implantações contínuas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af02b-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="af02b-107">Qualquer confirmação ao repositório do GitHub dispara uma compilação e uma implantação para o slot de preparo do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="af02b-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="af02b-108">Nesta seção, você concluirá as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="af02b-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="af02b-109">Publicar o código do aplicativo no GitHub</span><span class="sxs-lookup"><span data-stu-id="af02b-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="af02b-110">Desconectar a implantação do git local</span><span class="sxs-lookup"><span data-stu-id="af02b-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="af02b-111">Criar uma organização do Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="af02b-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="af02b-112">Criar um projeto de equipe no Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="af02b-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="af02b-113">Criar a definição de build</span><span class="sxs-lookup"><span data-stu-id="af02b-113">Create a build definition</span></span>
* <span data-ttu-id="af02b-114">Criar um pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="af02b-114">Create a release pipeline</span></span>
* <span data-ttu-id="af02b-115">Confirmar alterações no GitHub e implantar automaticamente no Azure</span><span class="sxs-lookup"><span data-stu-id="af02b-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="af02b-116">Examinar o pipeline de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="af02b-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="af02b-117">Publicar o código do aplicativo no GitHub</span><span class="sxs-lookup"><span data-stu-id="af02b-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="af02b-118">Abra uma janela do navegador e navegue até `https://github.com` .</span><span class="sxs-lookup"><span data-stu-id="af02b-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="af02b-119">Clique na **+** lista suspensa no cabeçalho e selecione **novo repositório**:</span><span class="sxs-lookup"><span data-stu-id="af02b-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Opção novo repositório do GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="af02b-121">Selecione sua conta na lista suspensa **proprietário** e insira *leitor de feed simples* na caixa de texto nome do **repositório** .</span><span class="sxs-lookup"><span data-stu-id="af02b-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="af02b-122">Clique no botão **criar repositório** .</span><span class="sxs-lookup"><span data-stu-id="af02b-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="af02b-123">Abra o Shell de comando do computador local.</span><span class="sxs-lookup"><span data-stu-id="af02b-123">Open your local machine's command shell.</span></span> <span data-ttu-id="af02b-124">Navegue até o diretório no qual o repositório Git do *leitor de feeds simples* está armazenado.</span><span class="sxs-lookup"><span data-stu-id="af02b-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="af02b-125">Renomeie o remoto de *origem* existente para *upstream*.</span><span class="sxs-lookup"><span data-stu-id="af02b-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="af02b-126">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="af02b-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="af02b-127">Adicione um novo ponto remoto de *origem* apontando para sua cópia do repositório no github.</span><span class="sxs-lookup"><span data-stu-id="af02b-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="af02b-128">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="af02b-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="af02b-129">Publique seu repositório git local no repositório GitHub recém-criado.</span><span class="sxs-lookup"><span data-stu-id="af02b-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="af02b-130">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="af02b-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="af02b-131">Abra uma janela do navegador e navegue até `https://github.com/<GitHub_username>/simple-feed-reader/` .</span><span class="sxs-lookup"><span data-stu-id="af02b-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="af02b-132">Valide se seu código aparece no repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="af02b-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="af02b-133">Desconectar a implantação do git local</span><span class="sxs-lookup"><span data-stu-id="af02b-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="af02b-134">Remova a implantação do git local com as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="af02b-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="af02b-135">Azure Pipelines (um serviço DevOps do Azure) substitui e aumenta essa funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="af02b-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="af02b-136">Abra o [portal do Azure](https://portal.azure.com/)e navegue até o aplicativo Web de *preparo (myWebApp \<unique_number\> /staging)* .</span><span class="sxs-lookup"><span data-stu-id="af02b-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="af02b-137">O aplicativo Web pode ser localizado rapidamente inserindo o *preparo* na caixa de pesquisa do portal:</span><span class="sxs-lookup"><span data-stu-id="af02b-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![termo de pesquisa do aplicativo Web de preparo](media/cicd/portal-search-box.png)

1. <span data-ttu-id="af02b-139">Clique em **central de implantação**.</span><span class="sxs-lookup"><span data-stu-id="af02b-139">Click **Deployment Center**.</span></span> <span data-ttu-id="af02b-140">Um novo painel é exibido.</span><span class="sxs-lookup"><span data-stu-id="af02b-140">A new panel appears.</span></span> <span data-ttu-id="af02b-141">Clique em **Desconectar** para remover a configuração de controle do código-fonte git local que foi adicionada no capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="af02b-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="af02b-142">Confirme a operação de remoção clicando no botão **Sim** .</span><span class="sxs-lookup"><span data-stu-id="af02b-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="af02b-143">Navegue até o *<myWebApp unique_number>* serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af02b-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="af02b-144">Como lembrete, a caixa de pesquisa do portal pode ser usada para localizar rapidamente o serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af02b-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="af02b-145">Clique em **central de implantação**.</span><span class="sxs-lookup"><span data-stu-id="af02b-145">Click **Deployment Center**.</span></span> <span data-ttu-id="af02b-146">Um novo painel é exibido.</span><span class="sxs-lookup"><span data-stu-id="af02b-146">A new panel appears.</span></span> <span data-ttu-id="af02b-147">Clique em **Desconectar** para remover a configuração de controle do código-fonte git local que foi adicionada no capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="af02b-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="af02b-148">Confirme a operação de remoção clicando no botão **Sim** .</span><span class="sxs-lookup"><span data-stu-id="af02b-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="af02b-149">Criar uma organização do Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="af02b-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="af02b-150">Abra um navegador e navegue até a [página de criação da organização do Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="af02b-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="af02b-151">Digite um nome exclusivo na caixa de texto **selecionar um nome memorizado** para formar a URL para acessar sua organização DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="af02b-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="af02b-152">Selecione o botão de opção **git** , pois o código está hospedado em um repositório github.</span><span class="sxs-lookup"><span data-stu-id="af02b-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="af02b-153">Clique no botão **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="af02b-153">Click the **Continue** button.</span></span> <span data-ttu-id="af02b-154">Após uma breve espera, uma conta e um projeto de equipe, chamados *MyFirstProject*, são criados.</span><span class="sxs-lookup"><span data-stu-id="af02b-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Página de criação da organização do Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="af02b-156">Abra o email de confirmação indicando que a organização e o projeto DevOps do Azure estão prontos para uso.</span><span class="sxs-lookup"><span data-stu-id="af02b-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="af02b-157">Clique no botão **iniciar o projeto** :</span><span class="sxs-lookup"><span data-stu-id="af02b-157">Click the **Start your project** button:</span></span>

    ![Botão iniciar o projeto](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="af02b-159">Um navegador é aberto para \* \<account_name\> . VisualStudio.com\*.</span><span class="sxs-lookup"><span data-stu-id="af02b-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="af02b-160">Clique no link *MyFirstProject* para começar a configurar o pipeline DevOps do projeto.</span><span class="sxs-lookup"><span data-stu-id="af02b-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="af02b-161">Configurar o pipeline de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="af02b-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="af02b-162">Há três etapas distintas a serem concluídas.</span><span class="sxs-lookup"><span data-stu-id="af02b-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="af02b-163">A conclusão das etapas nas três seções a seguir resulta em um pipeline DevOps operacional.</span><span class="sxs-lookup"><span data-stu-id="af02b-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="af02b-164">Conceder ao Azure DevOps acesso ao repositório GitHub</span><span class="sxs-lookup"><span data-stu-id="af02b-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="af02b-165">Expanda o **ou crie o código de um acordeão do repositório externo** .</span><span class="sxs-lookup"><span data-stu-id="af02b-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="af02b-166">Clique no botão **Configurar compilação** :</span><span class="sxs-lookup"><span data-stu-id="af02b-166">Click the **Setup Build** button:</span></span>

    ![Botão configurar compilação](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="af02b-168">Selecione a opção **GitHub** na seção **selecionar uma fonte** :</span><span class="sxs-lookup"><span data-stu-id="af02b-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Selecionar uma origem-GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="af02b-170">A autorização é necessária para que o Azure DevOps possa acessar seu repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="af02b-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="af02b-171">Insira *<GitHub_username> conexão do GitHub* na caixa de texto **nome da conexão** .</span><span class="sxs-lookup"><span data-stu-id="af02b-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="af02b-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="af02b-172">For example:</span></span>

    ![Nome da conexão do GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="af02b-174">Se a autenticação de dois fatores estiver habilitada em sua conta do GitHub, será necessário um token de acesso pessoal.</span><span class="sxs-lookup"><span data-stu-id="af02b-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="af02b-175">Nesse caso, clique no link **autorizar com um token de acesso pessoal do GitHub** .</span><span class="sxs-lookup"><span data-stu-id="af02b-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="af02b-176">Consulte as [instruções de criação do token de acesso pessoal do GitHub oficial](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obter ajuda.</span><span class="sxs-lookup"><span data-stu-id="af02b-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="af02b-177">Somente o escopo de permissões do *repositório* é necessário.</span><span class="sxs-lookup"><span data-stu-id="af02b-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="af02b-178">Caso contrário, clique no botão **autorizar usando OAuth** .</span><span class="sxs-lookup"><span data-stu-id="af02b-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="af02b-179">Quando solicitado, entre em sua conta do GitHub.</span><span class="sxs-lookup"><span data-stu-id="af02b-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="af02b-180">Em seguida, selecione autorizar para conceder acesso à sua organização DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="af02b-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="af02b-181">Se for bem-sucedido, um novo ponto de extremidade de serviço será criado.</span><span class="sxs-lookup"><span data-stu-id="af02b-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="af02b-182">Clique no botão de reticências ao lado do botão **repositório** .</span><span class="sxs-lookup"><span data-stu-id="af02b-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="af02b-183">Selecione o *<GitHub_username> repositório/Simple-Feed-Reader* na lista.</span><span class="sxs-lookup"><span data-stu-id="af02b-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="af02b-184">Clique no botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="af02b-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="af02b-185">Selecione a ramificação *mestre* do **Branch padrão para a lista suspensa de compilações manuais e agendadas** .</span><span class="sxs-lookup"><span data-stu-id="af02b-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="af02b-186">Clique no botão **Continuar**.</span><span class="sxs-lookup"><span data-stu-id="af02b-186">Click the **Continue** button.</span></span> <span data-ttu-id="af02b-187">A página seleção de modelo é exibida.</span><span class="sxs-lookup"><span data-stu-id="af02b-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="af02b-188">Criar a definição de compilação</span><span class="sxs-lookup"><span data-stu-id="af02b-188">Create the build definition</span></span>

1. <span data-ttu-id="af02b-189">Na página seleção de modelo, insira *ASP.NET Core* na caixa de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="af02b-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core Pesquisar na página de modelo](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="af02b-191">Os resultados da pesquisa de modelo são exibidos.</span><span class="sxs-lookup"><span data-stu-id="af02b-191">The template search results appear.</span></span> <span data-ttu-id="af02b-192">Passe o mouse sobre o modelo de **ASP.NET Core** e clique no botão **aplicar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="af02b-193">A guia **tarefas** da definição de compilação é exibida.</span><span class="sxs-lookup"><span data-stu-id="af02b-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="af02b-194">Clique na guia **gatilhos** .</span><span class="sxs-lookup"><span data-stu-id="af02b-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="af02b-195">Marque a caixa **habilitar integração contínua** .</span><span class="sxs-lookup"><span data-stu-id="af02b-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="af02b-196">Na seção **filtros de ramificação** , confirme se a lista suspensa **tipo** está definida como *incluir*.</span><span class="sxs-lookup"><span data-stu-id="af02b-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="af02b-197">Defina a lista suspensa de **especificações da ramificação** como *mestre*.</span><span class="sxs-lookup"><span data-stu-id="af02b-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Habilitar configurações de integração contínua](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="af02b-199">Essas configurações fazem com que uma compilação seja disparada quando qualquer alteração é enviada para o Branch *mestre* do repositório do github.</span><span class="sxs-lookup"><span data-stu-id="af02b-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="af02b-200">A integração contínua é testada na seção [confirmar alterações no GitHub e implantar automaticamente no Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .</span><span class="sxs-lookup"><span data-stu-id="af02b-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="af02b-201">Clique no botão **salvar & fila** e selecione a opção **salvar** :</span><span class="sxs-lookup"><span data-stu-id="af02b-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Botão Salvar](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="af02b-203">A caixa de diálogo modal a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="af02b-203">The following modal dialog appears:</span></span>

    ![Salvar definição de compilação – caixa de diálogo modal](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="af02b-205">Use a pasta padrão do *\\* e clique no botão **salvar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="af02b-206">Criar o pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="af02b-206">Create the release pipeline</span></span>

1. <span data-ttu-id="af02b-207">Clique na guia **versões** do seu projeto de equipe.</span><span class="sxs-lookup"><span data-stu-id="af02b-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="af02b-208">Clique no botão **novo pipeline** .</span><span class="sxs-lookup"><span data-stu-id="af02b-208">Click the **New pipeline** button.</span></span>

    ![Guia versões – botão nova definição](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="af02b-210">O painel seleção de modelo é exibido.</span><span class="sxs-lookup"><span data-stu-id="af02b-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="af02b-211">Na página seleção de modelo, insira *serviço de aplicativo* na caixa de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="af02b-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Caixa de pesquisa do modelo liberar pipeline](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="af02b-213">Os resultados da pesquisa de modelo são exibidos.</span><span class="sxs-lookup"><span data-stu-id="af02b-213">The template search results appear.</span></span> <span data-ttu-id="af02b-214">Passe o mouse sobre a **implantação do serviço de Azure app com** o modelo de slot e clique no botão **aplicar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="af02b-215">A guia **pipeline** do pipeline de liberação é exibida.</span><span class="sxs-lookup"><span data-stu-id="af02b-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Guia pipeline de lançamento de pipeline](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="af02b-217">Clique no botão **Adicionar** na caixa **artefatos** .</span><span class="sxs-lookup"><span data-stu-id="af02b-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="af02b-218">O painel **Adicionar artefato** é exibido:</span><span class="sxs-lookup"><span data-stu-id="af02b-218">The **Add artifact** panel appears:</span></span>

    ![Pipeline de liberação – adicionar painel de artefatos](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="af02b-220">Selecione o bloco **Build** na seção **tipo de origem** .</span><span class="sxs-lookup"><span data-stu-id="af02b-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="af02b-221">Esse tipo permite a vinculação do pipeline de liberação à definição de compilação.</span><span class="sxs-lookup"><span data-stu-id="af02b-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="af02b-222">Selecione *MyFirstProject* na lista suspensa **projeto** .</span><span class="sxs-lookup"><span data-stu-id="af02b-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="af02b-223">Selecione o nome de definição de compilação, *MyFirstProject-ASP.NET Core-CI*, na lista suspensa **origem (definição de compilação)** .</span><span class="sxs-lookup"><span data-stu-id="af02b-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="af02b-224">Selecione *mais recente* na lista suspensa **versão padrão** .</span><span class="sxs-lookup"><span data-stu-id="af02b-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="af02b-225">Essa opção cria os artefatos produzidos pela última execução da definição de compilação.</span><span class="sxs-lookup"><span data-stu-id="af02b-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="af02b-226">Substitua o texto na caixa de texto **alias de origem** por *drop*.</span><span class="sxs-lookup"><span data-stu-id="af02b-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="af02b-227">Clique no botão **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-227">Click the **Add** button.</span></span> <span data-ttu-id="af02b-228">A seção **artefatos** é atualizada para exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="af02b-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="af02b-229">Clique no ícone de raio para habilitar implantações contínuas:</span><span class="sxs-lookup"><span data-stu-id="af02b-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Artefatos de pipeline de liberação – ícone de raio](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="af02b-231">Com essa opção habilitada, uma implantação ocorre cada vez que uma nova compilação está disponível.</span><span class="sxs-lookup"><span data-stu-id="af02b-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="af02b-232">Um painel de **gatilho de implantação contínua** aparece à direita.</span><span class="sxs-lookup"><span data-stu-id="af02b-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="af02b-233">Clique no botão de alternância para habilitar o recurso.</span><span class="sxs-lookup"><span data-stu-id="af02b-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="af02b-234">Não é necessário habilitar o **gatilho de solicitação pull**.</span><span class="sxs-lookup"><span data-stu-id="af02b-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="af02b-235">Clique no menu suspenso **Adicionar** na seção **filtros de Branch de compilação** .</span><span class="sxs-lookup"><span data-stu-id="af02b-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="af02b-236">Escolha a opção de **ramificação padrão da definição de compilação** .</span><span class="sxs-lookup"><span data-stu-id="af02b-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="af02b-237">Esse filtro faz com que o lançamento seja disparado apenas para uma compilação do Branch *mestre* do repositório do github.</span><span class="sxs-lookup"><span data-stu-id="af02b-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="af02b-238">Clique no botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-238">Click the **Save** button.</span></span> <span data-ttu-id="af02b-239">Clique no botão **OK** na caixa de diálogo **salvar** modal resultante.</span><span class="sxs-lookup"><span data-stu-id="af02b-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="af02b-240">Clique na caixa **ambiente 1** .</span><span class="sxs-lookup"><span data-stu-id="af02b-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="af02b-241">Um painel de **ambiente** aparece à direita.</span><span class="sxs-lookup"><span data-stu-id="af02b-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="af02b-242">Altere o texto do *ambiente 1* na caixa de texto **nome do ambiente** para *produção*.</span><span class="sxs-lookup"><span data-stu-id="af02b-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Pipeline de liberação – caixa de texto nome do ambiente](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="af02b-244">Clique no link **1 fase, 2 tarefas** na caixa **produção** :</span><span class="sxs-lookup"><span data-stu-id="af02b-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Pipeline de lançamento – ambiente de produção link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="af02b-246">A guia **tarefas** do ambiente é exibida.</span><span class="sxs-lookup"><span data-stu-id="af02b-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="af02b-247">Clique na tarefa **implantar o serviço de Azure app para o slot** .</span><span class="sxs-lookup"><span data-stu-id="af02b-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="af02b-248">Suas configurações aparecem em um painel à direita.</span><span class="sxs-lookup"><span data-stu-id="af02b-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="af02b-249">Selecione a assinatura do Azure associada ao serviço de aplicativo na lista suspensa **assinatura do Azure** .</span><span class="sxs-lookup"><span data-stu-id="af02b-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="af02b-250">Depois de selecionado, clique no botão **autorizar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="af02b-251">Selecione *aplicativo Web* na lista suspensa **tipo de aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="af02b-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="af02b-252">Selecione *myWebApp/<unique_number/>* na lista suspensa **nome do serviço de aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="af02b-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="af02b-253">Selecione *AzureTutorial* na lista suspensa **grupo de recursos** .</span><span class="sxs-lookup"><span data-stu-id="af02b-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="af02b-254">Selecione *preparo* na lista suspensa **slot** .</span><span class="sxs-lookup"><span data-stu-id="af02b-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="af02b-255">Clique no botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="af02b-256">Passe o mouse sobre o nome do pipeline de liberação padrão.</span><span class="sxs-lookup"><span data-stu-id="af02b-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="af02b-257">Clique no ícone de lápis para editá-lo.</span><span class="sxs-lookup"><span data-stu-id="af02b-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="af02b-258">Use *MyFirstProject-ASP.NET Core-CD* como o nome.</span><span class="sxs-lookup"><span data-stu-id="af02b-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nome do pipeline de liberação](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="af02b-260">Clique no botão **Salvar** .</span><span class="sxs-lookup"><span data-stu-id="af02b-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="af02b-261">Confirmar alterações no GitHub e implantar automaticamente no Azure</span><span class="sxs-lookup"><span data-stu-id="af02b-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="af02b-262">Abra *SimpleFeedReader. sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="af02b-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="af02b-263">Em Gerenciador de Soluções, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="af02b-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="af02b-264">Alterar `<h2>Simple Feed Reader - V3</h2>` para `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="af02b-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="af02b-265">Pressione **Ctrl** + **Shift** + **B** para compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af02b-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="af02b-266">Confirme o arquivo para o repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="af02b-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="af02b-267">Use a página **alterações** na guia *Team Explorer* do Visual Studio ou execute o seguinte usando o Shell de comando do computador local:</span><span class="sxs-lookup"><span data-stu-id="af02b-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="af02b-268">Envie por push a alteração no Branch *mestre* para o remoto de *origem* do seu repositório github:</span><span class="sxs-lookup"><span data-stu-id="af02b-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="af02b-269">A confirmação aparece no Branch *mestre* do repositório do github:</span><span class="sxs-lookup"><span data-stu-id="af02b-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Confirmação do GitHub no Branch mestre](media/cicd/github-commit.png)

    <span data-ttu-id="af02b-271">A compilação é disparada, pois a integração contínua está habilitada na guia **gatilhos** da definição de compilação:</span><span class="sxs-lookup"><span data-stu-id="af02b-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![habilitar integração contínua](media/cicd/enable-ci.png)

1. <span data-ttu-id="af02b-273">Navegue até a guia **enfileirada** da **Azure Pipelines**  >  página de**Builds** Azure pipelines em Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="af02b-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="af02b-274">A compilação em fila mostra a ramificação e a confirmação que disparou a compilação:</span><span class="sxs-lookup"><span data-stu-id="af02b-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![compilação enfileirada](media/cicd/build-queued.png)

1. <span data-ttu-id="af02b-276">Depois que a compilação for realizada com sucesso, ocorrerá uma implantação do Azure.</span><span class="sxs-lookup"><span data-stu-id="af02b-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="af02b-277">Navegue até o aplicativo no navegador.</span><span class="sxs-lookup"><span data-stu-id="af02b-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="af02b-278">Observe que o texto "v4" aparece no título:</span><span class="sxs-lookup"><span data-stu-id="af02b-278">Notice that the "V4" text appears in the heading:</span></span>

    ![aplicativo atualizado](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="af02b-280">Examinar o pipeline de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="af02b-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="af02b-281">Definição da compilação</span><span class="sxs-lookup"><span data-stu-id="af02b-281">Build definition</span></span>

<span data-ttu-id="af02b-282">Uma definição de compilação foi criada com o nome *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="af02b-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="af02b-283">Após a conclusão, a compilação produz um arquivo *. zip* , incluindo os ativos a serem publicados.</span><span class="sxs-lookup"><span data-stu-id="af02b-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="af02b-284">O pipeline de lançamento implanta esses ativos no Azure.</span><span class="sxs-lookup"><span data-stu-id="af02b-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="af02b-285">A guia **tarefas** da definição de compilação lista as etapas individuais que estão sendo usadas.</span><span class="sxs-lookup"><span data-stu-id="af02b-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="af02b-286">Há cinco tarefas de compilação.</span><span class="sxs-lookup"><span data-stu-id="af02b-286">There are five build tasks.</span></span>

![tarefas de definição de compilação](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="af02b-288">**Restaurar** &mdash; Executa o `dotnet restore` comando para restaurar os pacotes NuGet do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af02b-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="af02b-289">O feed de pacote padrão usado é nuget.org.</span><span class="sxs-lookup"><span data-stu-id="af02b-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="af02b-290">**Criar** &mdash; Executa o `dotnet build --configuration release` comando para compilar o código do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af02b-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="af02b-291">Essa `--configuration` opção é usada para produzir uma versão otimizada do código, que é adequada para implantação em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="af02b-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="af02b-292">Modifique a variável *BuildConfiguration* na guia **variáveis** da definição de compilação se, por exemplo, uma configuração de depuração for necessária.</span><span class="sxs-lookup"><span data-stu-id="af02b-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="af02b-293">**Teste** &mdash; do Executa o `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` comando para executar os testes de unidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af02b-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="af02b-294">Os testes de unidade são executados em qualquer projeto C# correspondente ao `**/*Tests/*.csproj` padrão glob.</span><span class="sxs-lookup"><span data-stu-id="af02b-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="af02b-295">Os resultados de teste são salvos em um arquivo *. trx* no local especificado pela `--results-directory` opção.</span><span class="sxs-lookup"><span data-stu-id="af02b-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="af02b-296">Se algum teste falhar, a compilação falhará e não será implantada.</span><span class="sxs-lookup"><span data-stu-id="af02b-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="af02b-297">Para verificar se os testes de unidade funcionam, modifique *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* para quebrar propositadamente um dos testes.</span><span class="sxs-lookup"><span data-stu-id="af02b-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="af02b-298">Por exemplo, altere `Assert.True(result.Count > 0);` para `Assert.False(result.Count > 0);` no `Returns_News_Stories_Given_Valid_Uri` método.</span><span class="sxs-lookup"><span data-stu-id="af02b-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="af02b-299">Confirme e envie por push a alteração para o GitHub.</span><span class="sxs-lookup"><span data-stu-id="af02b-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="af02b-300">A compilação é disparada e falha.</span><span class="sxs-lookup"><span data-stu-id="af02b-300">The build is triggered and fails.</span></span> <span data-ttu-id="af02b-301">O status do pipeline de compilação muda para **com falha**.</span><span class="sxs-lookup"><span data-stu-id="af02b-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="af02b-302">Reverta a alteração, confirme e envie o push novamente.</span><span class="sxs-lookup"><span data-stu-id="af02b-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="af02b-303">A compilação é realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="af02b-303">The build succeeds.</span></span>

1. <span data-ttu-id="af02b-304">**Publicar** &mdash; Executa o `dotnet publish --configuration release --output <local_path_on_build_agent>` comando para produzir um arquivo *. zip* com os artefatos a serem implantados.</span><span class="sxs-lookup"><span data-stu-id="af02b-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="af02b-305">A `--output` opção especifica o local de publicação do arquivo *. zip* .</span><span class="sxs-lookup"><span data-stu-id="af02b-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="af02b-306">Esse local é especificado passando uma [variável predefinida](/azure/devops/pipelines/build/variables) chamada `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="af02b-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="af02b-307">Essa variável expande para um caminho local, como *c:\agent \_ work\1\a*, no agente de compilação.</span><span class="sxs-lookup"><span data-stu-id="af02b-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="af02b-308">**Publicar artefato** &mdash; Publica o arquivo *. zip* produzido pela tarefa de **publicação** .</span><span class="sxs-lookup"><span data-stu-id="af02b-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="af02b-309">A tarefa aceita o local do arquivo *. zip* como um parâmetro, que é a variável predefinida `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="af02b-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="af02b-310">O arquivo *. zip* é publicado como uma pasta chamada *drop*.</span><span class="sxs-lookup"><span data-stu-id="af02b-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="af02b-311">Clique no link **Resumo** da definição de compilação para exibir um histórico de compilações com a definição:</span><span class="sxs-lookup"><span data-stu-id="af02b-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Captura de tela mostrando histórico de definição de compilação](media/cicd/build-definition-summary.png)

<span data-ttu-id="af02b-313">Na página resultante, clique no link correspondente ao número de Build exclusivo:</span><span class="sxs-lookup"><span data-stu-id="af02b-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Captura de tela mostrando página de Resumo de definição de compilação](media/cicd/build-definition-completed.png)

<span data-ttu-id="af02b-315">Um resumo dessa compilação específica é exibido.</span><span class="sxs-lookup"><span data-stu-id="af02b-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="af02b-316">Clique na guia **artefatos** e observe que a pasta de *destino* produzida pela compilação está listada:</span><span class="sxs-lookup"><span data-stu-id="af02b-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Captura de tela mostrando artefatos de definição de compilação – remover pasta](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="af02b-318">Use **baixar** e **explorar** links para inspecionar os artefatos publicados.</span><span class="sxs-lookup"><span data-stu-id="af02b-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="af02b-319">Pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="af02b-319">Release pipeline</span></span>

<span data-ttu-id="af02b-320">Um pipeline de versão foi criado com o nome *MyFirstProject-ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="af02b-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Captura de tela mostrando visão geral do pipeline de lançamento](media/cicd/release-definition-overview.png)

<span data-ttu-id="af02b-322">Os dois principais componentes do pipeline de lançamento são os **artefatos** e os **ambientes**.</span><span class="sxs-lookup"><span data-stu-id="af02b-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="af02b-323">Clicar na caixa na seção **artefatos** revela o seguinte painel:</span><span class="sxs-lookup"><span data-stu-id="af02b-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Captura de tela mostrando artefatos de pipeline de liberação](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="af02b-325">O valor de **origem (definição de compilação)** representa a definição de compilação à qual esse pipeline de liberação está vinculado.</span><span class="sxs-lookup"><span data-stu-id="af02b-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="af02b-326">O arquivo *. zip* produzido por uma execução bem-sucedida da definição de compilação é fornecido ao ambiente de *produção* para implantação no Azure.</span><span class="sxs-lookup"><span data-stu-id="af02b-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="af02b-327">Clique no link da *fase 1, 2 tarefas* na caixa ambiente de *produção* para exibir as tarefas do pipeline de liberação:</span><span class="sxs-lookup"><span data-stu-id="af02b-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Captura de tela mostrando tarefas do pipeline de liberação](media/cicd/release-definition-tasks.png)

<span data-ttu-id="af02b-329">O pipeline de lançamento consiste em duas tarefas: *implantar Azure app serviço para o slot* e *gerenciar Azure app troca de slot de serviço*.</span><span class="sxs-lookup"><span data-stu-id="af02b-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="af02b-330">Clicar na primeira tarefa revela a seguinte configuração de tarefa:</span><span class="sxs-lookup"><span data-stu-id="af02b-330">Clicking the first task reveals the following task configuration:</span></span>

![Captura de tela mostrando tarefa de implantação de pipeline de liberação](media/cicd/release-definition-task1.png)

<span data-ttu-id="af02b-332">A assinatura do Azure, o tipo de serviço, o nome do aplicativo Web, o grupo de recursos e o slot de implantação são definidos na tarefa de implantação.</span><span class="sxs-lookup"><span data-stu-id="af02b-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="af02b-333">A caixa de texto **pacote ou pasta** contém o caminho do arquivo *. zip* a ser extraído e implantado no slot de *preparo* do aplicativo Web \*myWebApp \<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="af02b-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="af02b-334">Clicar na tarefa de permuta de slot revela a seguinte configuração de tarefa:</span><span class="sxs-lookup"><span data-stu-id="af02b-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Captura de tela mostrando a tarefa de permuta do slot do pipeline de liberação](media/cicd/release-definition-task2.png)

<span data-ttu-id="af02b-336">Os detalhes de assinatura, grupo de recursos, tipo de serviço, nome do aplicativo Web e slot de implantação são fornecidos.</span><span class="sxs-lookup"><span data-stu-id="af02b-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="af02b-337">A caixa de seleção **alternar com produção** está marcada.</span><span class="sxs-lookup"><span data-stu-id="af02b-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="af02b-338">Consequentemente, os bits implantados no slot de *preparo* são trocados no ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="af02b-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="af02b-339">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="af02b-339">Additional reading</span></span>

* [<span data-ttu-id="af02b-340">Criar seu primeiro pipeline com o Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="af02b-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="af02b-341">Compilar e projeto do .NET Core</span><span class="sxs-lookup"><span data-stu-id="af02b-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="af02b-342">Implantar um aplicativo Web com Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="af02b-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
