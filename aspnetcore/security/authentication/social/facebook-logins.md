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
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="c9fb7-103">Configuração de login externo do Facebook no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9fb7-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="c9fb7-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c9fb7-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="c9fb7-105">Este tutorial com exemplos de código mostra como permitir que seus usuários entrem com sua conta do Facebook usando uma amostra ASP.NET projeto Core 3.0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c9fb7-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="c9fb7-106">Começamos criando um ID do Aplicativo do Facebook seguindo as [etapas oficiais](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="c9fb7-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="c9fb7-107">Crie o aplicativo no Facebook</span><span class="sxs-lookup"><span data-stu-id="c9fb7-107">Create the app in Facebook</span></span>

* <span data-ttu-id="c9fb7-108">Adicione o pacote [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="c9fb7-109">Navegue até a página do [aplicativo Facebook Developers](https://developers.facebook.com/apps/) e faça login.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="c9fb7-110">Se você ainda não tem uma conta no Facebook, use o link **Inscreva-se para** o Facebook na página de login para criar uma.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="c9fb7-111">Depois de ter uma conta no Facebook, siga as instruções para se registrar como desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="c9fb7-112">No menu **Meus aplicativos,** selecione **Criar aplicativo** para criar um novo ID do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portal do Facebook para desenvolvedores aberto no Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="c9fb7-114">Preencha o formulário e toque no botão **Criar ID do aplicativo.**</span><span class="sxs-lookup"><span data-stu-id="c9fb7-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Crie um novo formulário de ID de aplicativo](index/_static/FBNewAppId.png)

* <span data-ttu-id="c9fb7-116">No novo cartão do App, **selecione Adicionar um Produto**.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="c9fb7-117">No cartão de login do **Facebook,** clique **em Configurar**</span><span class="sxs-lookup"><span data-stu-id="c9fb7-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Página de configuração do produto](index/_static/FBProductSetup.png)

* <span data-ttu-id="c9fb7-119">O **assistente Quickstart** é lançado com **Escolha uma Plataforma** como primeira página.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="c9fb7-120">Contorne o assistente por enquanto clicando no link **Configurações de** login do **FaceBook** no menu no canto inferior esquerdo:</span><span class="sxs-lookup"><span data-stu-id="c9fb7-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Pular início rápido](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="c9fb7-122">Você é apresentado com a página **Configurações do Cliente OAuth:**</span><span class="sxs-lookup"><span data-stu-id="c9fb7-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Página configurações do Cliente OAuth](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="c9fb7-124">Insira seu URI de desenvolvimento com */signin-facebook* anexado ao campo `https://localhost:44320/signin-facebook` **Valid OAuth Redirecionar URIs** (por exemplo: ).</span><span class="sxs-lookup"><span data-stu-id="c9fb7-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="c9fb7-125">A autenticação do Facebook configurada mais tarde neste tutorial lidará automaticamente com solicitações na rota */signin-facebook* para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="c9fb7-126">O URI */signin-facebook* é definido como o callback padrão do provedor de autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="c9fb7-127">Você pode alterar o URI de retorno de chamada padrão enquanto configura o middleware de autenticação do Facebook através da propriedade [Herdada RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="c9fb7-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="c9fb7-128">Clique em **Salvar alterações**.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="c9fb7-129">Clique **em Configurações** > **Link** básico na navegação à esquerda.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="c9fb7-130">Nesta página, anote seu `App ID` e `App Secret`seu .</span><span class="sxs-lookup"><span data-stu-id="c9fb7-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="c9fb7-131">Você adicionará ambos ao seu aplicativo ASP.NET Core na próxima seção:</span><span class="sxs-lookup"><span data-stu-id="c9fb7-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="c9fb7-132">Ao implantar o site, você precisa revisitar a página de configuração **do Login** do Facebook e registrar um novo URI público.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="c9fb7-133">Armazene o ID e o segredo do aplicativo do Facebook</span><span class="sxs-lookup"><span data-stu-id="c9fb7-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="c9fb7-134">Armazene configurações sensíveis, como o ID do aplicativo do Facebook e valores secretos com [o Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c9fb7-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c9fb7-135">Para esta amostra, use as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="c9fb7-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="c9fb7-136">Inicialize o projeto para armazenamento secreto de acordo com as instruções em [Habilitar o armazenamento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="c9fb7-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="c9fb7-137">Armazene as configurações sensíveis na loja `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret`secreta local com as chaves secretas e:</span><span class="sxs-lookup"><span data-stu-id="c9fb7-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="c9fb7-138">Configurar a autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="c9fb7-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="c9fb7-139">Adicione o serviço `ConfigureServices` do Facebook no método no arquivo *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="c9fb7-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="c9fb7-140">Faça login com o Facebook</span><span class="sxs-lookup"><span data-stu-id="c9fb7-140">Sign in with Facebook</span></span>

* <span data-ttu-id="c9fb7-141">Execute o aplicativo e **selecione Fazer login**.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="c9fb7-142">Em **Use outro serviço para fazer login,** selecione o Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="c9fb7-143">Você é redirecionado para o **Facebook** para autenticação.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="c9fb7-144">Digite suas credenciais do Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="c9fb7-145">Você é redirecionado de volta para o seu site onde você pode definir o seu e-mail.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="c9fb7-146">Agora você está logado usando suas credenciais do Facebook:</span><span class="sxs-lookup"><span data-stu-id="c9fb7-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="c9fb7-147">Reagir para cancelar autorizar o login externo</span><span class="sxs-lookup"><span data-stu-id="c9fb7-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="c9fb7-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>pode fornecer um caminho de redirecionamento para o agente de usuário quando o usuário não aprova a demanda de autorização solicitada.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="c9fb7-149">O código a `AccessDeniedPath` `"/AccessDeniedPathInfo"`seguir define o :</span><span class="sxs-lookup"><span data-stu-id="c9fb7-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="c9fb7-150">Recomendamos `AccessDeniedPath` que a página contenha as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="c9fb7-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="c9fb7-151">A autenticação remota foi cancelada.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="c9fb7-152">Este aplicativo requer autenticação.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-152">This app requires authentication.</span></span>
* <span data-ttu-id="c9fb7-153">Para tentar fazer login novamente, selecione o link Login.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="c9fb7-154">Acesso ao testeNegadoPath</span><span class="sxs-lookup"><span data-stu-id="c9fb7-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="c9fb7-155">Navegue até [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="c9fb7-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="c9fb7-156">Se você está conectado, você deve assinar.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="c9fb7-157">Execute o aplicativo e selecione o login do Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="c9fb7-158">Selecione **Não agora**.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-158">Select **Not now**.</span></span> <span data-ttu-id="c9fb7-159">Você é redirecionado para `AccessDeniedPath` a página especificada.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="c9fb7-160">Consulte a referência da API do [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração suportadas pela autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="c9fb7-161">As opções de configuração podem ser usadas para:</span><span class="sxs-lookup"><span data-stu-id="c9fb7-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="c9fb7-162">Solicite diferentes informações sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-162">Request different information about the user.</span></span>
* <span data-ttu-id="c9fb7-163">Adicione argumentos de seqüência de consulta para personalizar a experiência de login.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c9fb7-164">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="c9fb7-164">Troubleshooting</span></span>

* <span data-ttu-id="c9fb7-165">**somente ASP.NET Core 2.x:** Se a identidade não estiver `services.AddIdentity` `ConfigureServices`configurada por chamada, tentar autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c9fb7-166">O modelo de projeto usado neste tutorial garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="c9fb7-167">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você terá falha da operação de banco de dados A durante o processamento do erro *de solicitação.*</span><span class="sxs-lookup"><span data-stu-id="c9fb7-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c9fb7-168">Toque **em Aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c9fb7-169">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c9fb7-169">Next steps</span></span>

* <span data-ttu-id="c9fb7-170">Este artigo mostrou como você pode autenticar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="c9fb7-171">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c9fb7-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c9fb7-172">Depois de publicar seu site no aplicativo web Do `AppSecret` Azure, você deve redefinir o portal do desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="c9fb7-173">Defina `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` as configurações do aplicativo no portal Azure.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c9fb7-174">O sistema de configuração está configurado para ler chaves das variáveis do ambiente.</span><span class="sxs-lookup"><span data-stu-id="c9fb7-174">The configuration system is set up to read keys from environment variables.</span></span>
