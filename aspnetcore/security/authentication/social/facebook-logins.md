---
title: Configuração de login externo do Facebook no ASP.NET Core
author: rick-anderson
description: Tutorial com exemplos de código demonstrando a integração da autenticação do usuário da conta do Facebook em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277295"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configuração de login externo do Facebook no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Este tutorial com exemplos de código mostra como permitir que seus usuários entrem com sua conta do Facebook usando uma amostra ASP.NET projeto Core 3.0 criado na [página anterior](xref:security/authentication/social/index). Começamos criando um ID do Aplicativo do Facebook seguindo as [etapas oficiais](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Crie o aplicativo no Facebook

* Adicione o pacote [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet ao projeto.

* Navegue até a página do [aplicativo Facebook Developers](https://developers.facebook.com/apps/) e faça login. Se você ainda não tem uma conta no Facebook, use o link **Inscreva-se para** o Facebook na página de login para criar uma.  Depois de ter uma conta no Facebook, siga as instruções para se registrar como desenvolvedor do Facebook.

* No menu **Meus aplicativos,** selecione **Criar aplicativo** para criar um novo ID do aplicativo.

   ![Portal do Facebook para desenvolvedores aberto no Microsoft Edge](index/_static/FBMyApps.png)

* Preencha o formulário e toque no botão **Criar ID do aplicativo.**

  ![Crie um novo formulário de ID de aplicativo](index/_static/FBNewAppId.png)

* No novo cartão do App, **selecione Adicionar um Produto**.  No cartão de login do **Facebook,** clique **em Configurar** 

  ![Página de configuração do produto](index/_static/FBProductSetup.png)

* O **assistente Quickstart** é lançado com **Escolha uma Plataforma** como primeira página. Contorne o assistente por enquanto clicando no link **Configurações de** login do **FaceBook** no menu no canto inferior esquerdo:

  ![Pular início rápido](index/_static/FBSkipQuickStart.png)

* Você é apresentado com a página **Configurações do Cliente OAuth:**

  ![Página configurações do Cliente OAuth](index/_static/FBOAuthSetup.png)

* Insira seu URI de desenvolvimento com */signin-facebook* anexado ao campo `https://localhost:44320/signin-facebook` **Valid OAuth Redirecionar URIs** (por exemplo: ). A autenticação do Facebook configurada mais tarde neste tutorial lidará automaticamente com solicitações na rota */signin-facebook* para implementar o fluxo OAuth.

> [!NOTE]
> O URI */signin-facebook* é definido como o callback padrão do provedor de autenticação do Facebook. Você pode alterar o URI de retorno de chamada padrão enquanto configura o middleware de autenticação do Facebook através da propriedade [Herdada RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* Clique em **Salvar alterações**.

* Clique **em Configurações** > **Link** básico na navegação à esquerda.

  Nesta página, anote seu `App ID` e `App Secret`seu . Você adicionará ambos ao seu aplicativo ASP.NET Core na próxima seção:

* Ao implantar o site, você precisa revisitar a página de configuração **do Login** do Facebook e registrar um novo URI público.

## <a name="store-the-facebook-app-id-and-secret"></a>Armazene o ID e o segredo do aplicativo do Facebook

Armazene configurações sensíveis, como o ID do aplicativo do Facebook e valores secretos com [o Secret Manager](xref:security/app-secrets). Para esta amostra, use as seguintes etapas:

1. Inicialize o projeto para armazenamento secreto de acordo com as instruções em [Habilitar o armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações sensíveis na loja `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret`secreta local com as chaves secretas e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Configurar a autenticação do Facebook

Adicione o serviço `ConfigureServices` do Facebook no método no arquivo *Startup.cs:*

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Faça login com o Facebook

* Execute o aplicativo e **selecione Fazer login**. 
* Em **Use outro serviço para fazer login,** selecione o Facebook.
* Você é redirecionado para o **Facebook** para autenticação.
* Digite suas credenciais do Facebook.
* Você é redirecionado de volta para o seu site onde você pode definir o seu e-mail.

Agora você está logado usando suas credenciais do Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagir para cancelar autorizar o login externo

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>pode fornecer um caminho de redirecionamento para o agente de usuário quando o usuário não aprova a demanda de autorização solicitada.

O código a `AccessDeniedPath` `"/AccessDeniedPathInfo"`seguir define o :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Recomendamos `AccessDeniedPath` que a página contenha as seguintes informações:

*  A autenticação remota foi cancelada.
* Este aplicativo requer autenticação.
* Para tentar fazer login novamente, selecione o link Login.

### <a name="test-accessdeniedpath"></a>Acesso ao testeNegadoPath

* Navegue até [facebook.com](https://www.facebook.com/)
* Se você está conectado, você deve assinar.
* Execute o aplicativo e selecione o login do Facebook.
* Selecione **Não agora**. Você é redirecionado para `AccessDeniedPath` a página especificada.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a referência da API do [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração suportadas pela autenticação do Facebook. As opções de configuração podem ser usadas para:

* Solicite diferentes informações sobre o usuário.
* Adicione argumentos de seqüência de consulta para personalizar a experiência de login.

## <a name="troubleshooting"></a>Solução de problemas

* **somente ASP.NET Core 2.x:** Se a identidade não estiver `services.AddIdentity` `ConfigureServices`configurada por chamada, tentar autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você terá falha da operação de banco de dados A durante o processamento do erro *de solicitação.* Toque **em Aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Facebook. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo web Do `AppSecret` Azure, você deve redefinir o portal do desenvolvedor do Facebook.

* Defina `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` as configurações do aplicativo no portal Azure. O sistema de configuração está configurado para ler chaves das variáveis do ambiente.
