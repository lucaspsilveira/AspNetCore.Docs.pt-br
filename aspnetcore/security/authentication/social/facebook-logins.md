---
title: Configuração de logon externo do Facebook no ASP.NET Core
author: rick-anderson
description: Tutorial com exemplos de código que demonstram a integração da autenticação de usuário da conta do Facebook em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: 11ddc7314a694446d488da6ef1b2e3423bf7241a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777027"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configuração de logon externo do Facebook no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Este tutorial com exemplos de código mostra como permitir que os usuários entrem com sua conta do Facebook usando um projeto de exemplo ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index). Começamos criando uma ID de aplicativo do Facebook seguindo as [etapas oficiais](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Criar o aplicativo no Facebook

* Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) ao projeto.

* Navegue até a página do [aplicativo de desenvolvedores do Facebook](https://developers.facebook.com/apps/) e entre. Se você ainda não tiver uma conta do Facebook, use o link **inscrever-** se no Facebook na página de logon para criar uma.  Depois de ter uma conta do Facebook, siga as instruções para se registrar como um desenvolvedor do Facebook.

* No menu **meus aplicativos** , selecione **criar aplicativo** para criar uma nova ID de aplicativo.

   ![Portal do Facebook for Developers aberto no Microsoft Edge](index/_static/FBMyApps.png)

* Preencha o formulário e toque no botão **criar ID do aplicativo** .

  ![Criar um novo formulário de ID do aplicativo](index/_static/FBNewAppId.png)

* Na nova placa de aplicativo, selecione **Adicionar um produto**.  No cartão de **logon do Facebook** , clique em **Configurar** 

  ![Página configuração do produto](index/_static/FBProductSetup.png)

* O assistente de **início rápido** é iniciado com **escolher uma plataforma** como a primeira página. Ignore o assistente por enquanto clicando no link **FaceBook Login** **configurações** de logon do Facebook no menu na parte inferior esquerda:

  ![Ignorar Início Rápido](index/_static/FBSkipQuickStart.png)

* Você verá a página **configurações de OAuth do cliente** :

  ![Página de configurações do OAuth do cliente](index/_static/FBOAuthSetup.png)

* Insira seu URI de desenvolvimento com */SignIn-Facebook* anexados ao campo **URIs de redirecionamento OAuth válido** ( `https://localhost:44320/signin-facebook`por exemplo:). A autenticação do Facebook configurada posteriormente neste tutorial tratará automaticamente as solicitações na rota */SignIn-Facebook* para implementar o fluxo OAuth.

> [!NOTE]
> O URI */SignIn-Facebook* é definido como o retorno de chamada padrão do provedor de autenticação do Facebook. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Facebook por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Clique em **Salvar Alterações**.

* Clique no link **configurações** > **básico** no painel de navegação esquerdo.

  Nesta página, anote seu `App ID` e seu. `App Secret` Você adicionará ambos ao seu aplicativo ASP.NET Core na próxima seção:

* Ao implantar o site, você precisa revisitar a página de configuração de **logon do Facebook** e registrar um novo URI público.

## <a name="store-the-facebook-app-id-and-secret"></a>Armazenar a ID e o segredo do aplicativo do Facebook

Armazene configurações confidenciais, como a ID do aplicativo do Facebook e valores secretos com o [Gerenciador de segredo](xref:security/app-secrets). Para este exemplo, use as seguintes etapas:

1. Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações confidenciais no repositório de segredo local com as chaves `Authentication:Facebook:AppId` secretas e `Authentication:Facebook:AppSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Configurar a autenticação do Facebook

Adicione o serviço do Facebook no `ConfigureServices` método no arquivo *Startup.cs* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Entrar com o Facebook

* Execute o aplicativo e selecione **fazer logon**. 
* Em **usar outro serviço para fazer logon.** selecione Facebook.
* Você será redirecionado para o **Facebook** para autenticação.
* Insira suas credenciais do Facebook.
* Você será Redirecionado de volta para o site onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagir a cancelar autorizar entrada externa

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>pode fornecer um caminho de redirecionamento para o agente do usuário quando o usuário não aprova a demanda de autorização solicitada.

O código a seguir define `AccessDeniedPath` o `"/AccessDeniedPathInfo"`para:

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Recomendamos que `AccessDeniedPath` a página contenha as seguintes informações:

*  A autenticação remota foi cancelada.
* Este aplicativo requer autenticação.
* Para tentar entrar novamente, selecione o link de logon.

### <a name="test-accessdeniedpath"></a>Testar AccessDeniedPath

* Navegue até [Facebook.com](https://www.facebook.com/)
* Se você estiver conectado, deverá sair.
* Execute o aplicativo e selecione entrada no Facebook.
* Selecione **não agora**. Você será redirecionado para a página `AccessDeniedPath` especificada.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a referência da API do [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração com suporte na autenticação do Facebook. As opções de configuração podem ser usadas para:

* Solicite informações diferentes sobre o usuário.
* Adicione argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.

## <a name="troubleshooting"></a>Solução de problemas

* **Somente ASP.NET Core 2. x:** Se Identity não estiver configurado chamando `services.AddIdentity` em `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* . Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Facebook. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.

* Defina `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure. O sistema de configuração é configurado para ler chaves de variáveis de ambiente.
