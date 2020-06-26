---
title: Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio
author: rick-anderson
description: Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: d805d57fd1e2d83d0148900993e4bf6108a13028
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408403"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio

De [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Consulte [publicar um aplicativo Web para Azure app serviço usando Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) se você estiver trabalhando no MacOS.

Para solucionar um problema de implantação do Serviço de Aplicativo, confira <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configuração

* Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma. 

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Na página inicial do Visual Studio, selecione **Arquivo > Novo > Projeto...**

![Menu Arquivo](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Complete a caixa de diálogo **Novo Projeto**:

* Selecione **Aplicativo Web ASP.NET Core**.
* Selecione **Avançar**.

![Caixa de diálogo Novo Projeto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Na caixa de diálogo **Novo Aplicativo Web ASP.NET Core**:

* Selecione **aplicativo Web**.
* Selecione **alterar** em autenticação.

![Caixa de diálogo novo ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

A caixa de diálogo **Mudar Autenticação** é exibida. 

* Selecione **Contas de Usuário Individuais**.
* Selecione **OK** para retornar para o **novo aplicativo Web ASP.NET Core**e, em seguida, selecione **criar**.

![Caixa de diálogo Nova autenticação da Web do ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

O Visual Studio cria a solução.

## <a name="run-the-app"></a>Executar o aplicativo

* Pressione CTRL+F5 para executar o projeto.
* Teste o link de **privacidade** .

![Aplicativo Web aberto no Microsoft Edge no localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrar um usuário

* Selecione **Registrar** e registre um novo usuário. Você pode usar um endereço de email fictício. Ao enviar, a página exibirá o seguinte erro:

    *"Uma operação de banco de dados falhou ao processar a solicitação. A aplicação de migrações existentes para o contexto do BD de aplicativos pode resolver esse problema. "*
* Selecione **Aplicar Migrações** e, depois que a página for atualizada, atualize a página.

![uma operação de banco de dados falhou ao processar a solicitação. A aplicação de migrações existentes ao contexto do BD do Aplicativo pode resolver esse problema.](publish-to-azure-webapp-using-vs/_static/mig.png)

O aplicativo exibe o email usado para registrar o novo usuário e um link de **logoff** .

![Aplicativo Web aberto no Microsoft Edge. O link Registrar é substituído pelo texto Olá, user1@example.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

Clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **publicar...**.

![Menu contextual aberto com o link Publicar realçado](publish-to-azure-webapp-using-vs/_static/pub.png)

Na caixa de diálogo **Publicar**:

* Selecione **Azure**.
* Selecione **Avançar**.

![Caixa de diálogo Publicar](publish-to-azure-webapp-using-vs/_static/maas1.png)

Na caixa de diálogo **Publicar**:

* Selecione **serviço de Azure app (Linux)**.
* Selecione **Avançar**.

![Diálogo de publicação: selecione serviço do Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

Na caixa de diálogo **publicar** , selecione **criar um novo serviço de Azure app...**

![Diálogo de publicação: selecione instância de serviço do Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

A caixa de diálogo **Criar serviço de aplicativo** é exibida:

* Os campos de entrada **Nome do Aplicativo**, **Grupo de Recursos** e **Plano do Serviço de Aplicativo** serão populados. Você pode manter esses nomes ou alterá-los.
* Selecione **Criar**.

![Criar caixa de diálogo do Serviço de Aplicativo](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Após a conclusão da criação, a caixa de diálogo é fechada automaticamente e a caixa de diálogo **publicar** fica em foco novamente:

* A nova instância que acabou de ser criada é selecionada automaticamente.
* Selecione **Concluir**.

![Diálogo de publicação: selecione instância do serviço de aplicativo](publish-to-azure-webapp-using-vs/_static/select_as.png)

Em seguida, você verá a página **Resumo do perfil de publicação** . O Visual Studio detectou que este aplicativo requer um banco de dados SQL Server e está solicitando que você o configure. Selecione **Configurar**.

![Página de resumo do perfil de publicação: configurar dependência de SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

A caixa de diálogo **Configurar dependência** é exibida:

* Selecione **banco de dados SQL do Azure**.
* Selecione **Avançar**.

![Caixa de diálogo Configurar dependência de SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

Na caixa de diálogo **configurar banco de dados SQL do Azure** , selecione **criar um banco de dados SQL**

![Caixa de diálogo Configurar banco de dados SQL do Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

O **criar banco de dados SQL do Azure** é exibido:

* Os campos **nome do banco de dados**, grupo de **recursos**, servidor de banco de dados e plano **do** serviço de **aplicativo** são preenchidos. Você pode manter esses valores ou alterá-los.
* Insira o **nome de usuário do administrador de banco de dados** e a senha do administrador do **banco** de dados para o servidor de **banco de dados** selecionado (Observe que a conta usada deve ter as permissões necessárias para criar o novo banco de dados SQL do
* Selecione **Criar**.

![Caixa de diálogo novo banco de dados SQL do Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Após a conclusão da criação, a caixa de diálogo será fechada automaticamente e a caixa de diálogo **configurar banco de dados SQL do Azure** será focalizada novamente:

* A nova instância que acabou de ser criada é selecionada automaticamente.
* Selecione **Avançar**.

![Caixa de diálogo Configurar banco de dados SQL do Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

Na próxima etapa da caixa de diálogo **configurar banco de dados SQL do Azure** :

* Insira os campos **nome de usuário da conexão de banco de dados** e senha de conexão de banco de **dados** Estes são os detalhes que seu aplicativo usará para se conectar ao banco de dados em tempo de execução. A prática recomendada é evitar o uso dos mesmos detalhes que o nome de usuário do administrador & senha usada na etapa anterior.
* Selecione **Concluir**.

![Caixa de diálogo Configurar banco de dados SQL do Azure, detalhes da cadeia de conexão](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

Na página **Resumo do perfil de publicação** , selecione **configurações**:

![Página de resumo do perfil de publicação: Editar configurações](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

Na página **Configurações** da caixa de diálogo **Publicar**:

* Expanda **bancos de dados** e marque **usar esta cadeia de conexão em tempo de execução**.
* Expanda **Entity Framework migrações** e marque **aplicar esta migração ao publicar**.

* Selecione **Salvar**. O Visual Studio retorna para a caixa de diálogo **Publicar**. 

![Caixa de diálogo Publicar: painel Configurações](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Clique em **Publicar**. O Visual Studio publica seu aplicativo no Azure. Quando a implantação for concluída, o aplicativo será aberto em um navegador.

![Caixa de diálogo Publicar: painel Configurações](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Atualizar o aplicativo

* Edite a página *pages/index. cshtml* Razor e altere seu conteúdo. Por exemplo, você pode modificar o parágrafo para dizer "Hello ASP.NET Core!":

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* Selecione **publicar** na página de **Resumo do perfil de publicação** novamente.

![Página de resumo do perfil de publicação](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* Depois que o aplicativo for publicado, verifique se as alterações feitas estão disponíveis no Azure.

![Verifique se a tarefa está concluída](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Limpar

Quando você concluir o teste do aplicativo, acesse o [portal do Azure](https://portal.azure.com/) e exclua o aplicativo.

* Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado.

![Portal do Azure: Grupos de Recursos no menu da barra lateral](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Na página **Grupos de recursos**, selecione **Excluir**.

![Portal do Azure: página Grupos de Recursos](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Insira o nome do grupo de recursos e selecione **Excluir**. O aplicativo e todos os outros recursos criados neste tutorial agora foram excluídos do Azure.

### <a name="next-steps"></a>Próximas etapas

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Recursos adicionais

* Para o Visual Studio Code, confira [Perfis de publicação](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-overview)
* [Grupos de recursos do Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Banco de Dados SQL do Azure](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
