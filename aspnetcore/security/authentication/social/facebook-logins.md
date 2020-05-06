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
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="1489b-103">Configuração de logon externo do Facebook no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1489b-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="1489b-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1489b-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="1489b-105">Este tutorial com exemplos de código mostra como permitir que os usuários entrem com sua conta do Facebook usando um projeto de exemplo ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1489b-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="1489b-106">Começamos criando uma ID de aplicativo do Facebook seguindo as [etapas oficiais](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="1489b-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="1489b-107">Criar o aplicativo no Facebook</span><span class="sxs-lookup"><span data-stu-id="1489b-107">Create the app in Facebook</span></span>

* <span data-ttu-id="1489b-108">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1489b-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="1489b-109">Navegue até a página do [aplicativo de desenvolvedores do Facebook](https://developers.facebook.com/apps/) e entre.</span><span class="sxs-lookup"><span data-stu-id="1489b-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="1489b-110">Se você ainda não tiver uma conta do Facebook, use o link **inscrever-** se no Facebook na página de logon para criar uma.</span><span class="sxs-lookup"><span data-stu-id="1489b-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="1489b-111">Depois de ter uma conta do Facebook, siga as instruções para se registrar como um desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="1489b-112">No menu **meus aplicativos** , selecione **criar aplicativo** para criar uma nova ID de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1489b-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portal do Facebook for Developers aberto no Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="1489b-114">Preencha o formulário e toque no botão **criar ID do aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="1489b-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Criar um novo formulário de ID do aplicativo](index/_static/FBNewAppId.png)

* <span data-ttu-id="1489b-116">Na nova placa de aplicativo, selecione **Adicionar um produto**.</span><span class="sxs-lookup"><span data-stu-id="1489b-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="1489b-117">No cartão de **logon do Facebook** , clique em **Configurar**</span><span class="sxs-lookup"><span data-stu-id="1489b-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Página configuração do produto](index/_static/FBProductSetup.png)

* <span data-ttu-id="1489b-119">O assistente de **início rápido** é iniciado com **escolher uma plataforma** como a primeira página.</span><span class="sxs-lookup"><span data-stu-id="1489b-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="1489b-120">Ignore o assistente por enquanto clicando no link **FaceBook Login** **configurações** de logon do Facebook no menu na parte inferior esquerda:</span><span class="sxs-lookup"><span data-stu-id="1489b-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Ignorar Início Rápido](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="1489b-122">Você verá a página **configurações de OAuth do cliente** :</span><span class="sxs-lookup"><span data-stu-id="1489b-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Página de configurações do OAuth do cliente](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="1489b-124">Insira seu URI de desenvolvimento com */SignIn-Facebook* anexados ao campo **URIs de redirecionamento OAuth válido** ( `https://localhost:44320/signin-facebook`por exemplo:).</span><span class="sxs-lookup"><span data-stu-id="1489b-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="1489b-125">A autenticação do Facebook configurada posteriormente neste tutorial tratará automaticamente as solicitações na rota */SignIn-Facebook* para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="1489b-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="1489b-126">O URI */SignIn-Facebook* é definido como o retorno de chamada padrão do provedor de autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="1489b-127">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Facebook por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="1489b-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="1489b-128">Clique em **Salvar Alterações**.</span><span class="sxs-lookup"><span data-stu-id="1489b-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="1489b-129">Clique no link **configurações** > **básico** no painel de navegação esquerdo.</span><span class="sxs-lookup"><span data-stu-id="1489b-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="1489b-130">Nesta página, anote seu `App ID` e seu. `App Secret`</span><span class="sxs-lookup"><span data-stu-id="1489b-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="1489b-131">Você adicionará ambos ao seu aplicativo ASP.NET Core na próxima seção:</span><span class="sxs-lookup"><span data-stu-id="1489b-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="1489b-132">Ao implantar o site, você precisa revisitar a página de configuração de **logon do Facebook** e registrar um novo URI público.</span><span class="sxs-lookup"><span data-stu-id="1489b-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="1489b-133">Armazenar a ID e o segredo do aplicativo do Facebook</span><span class="sxs-lookup"><span data-stu-id="1489b-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="1489b-134">Armazene configurações confidenciais, como a ID do aplicativo do Facebook e valores secretos com o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1489b-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1489b-135">Para este exemplo, use as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="1489b-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="1489b-136">Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="1489b-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="1489b-137">Armazene as configurações confidenciais no repositório de segredo local com as chaves `Authentication:Facebook:AppId` secretas e `Authentication:Facebook:AppSecret`:</span><span class="sxs-lookup"><span data-stu-id="1489b-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="1489b-138">Configurar a autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="1489b-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="1489b-139">Adicione o serviço do Facebook no `ConfigureServices` método no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="1489b-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="1489b-140">Entrar com o Facebook</span><span class="sxs-lookup"><span data-stu-id="1489b-140">Sign in with Facebook</span></span>

* <span data-ttu-id="1489b-141">Execute o aplicativo e selecione **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="1489b-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="1489b-142">Em **usar outro serviço para fazer logon.** selecione Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="1489b-143">Você será redirecionado para o **Facebook** para autenticação.</span><span class="sxs-lookup"><span data-stu-id="1489b-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="1489b-144">Insira suas credenciais do Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="1489b-145">Você será Redirecionado de volta para o site onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="1489b-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="1489b-146">Agora você está conectado usando suas credenciais do Facebook:</span><span class="sxs-lookup"><span data-stu-id="1489b-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="1489b-147">Reagir a cancelar autorizar entrada externa</span><span class="sxs-lookup"><span data-stu-id="1489b-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="1489b-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>pode fornecer um caminho de redirecionamento para o agente do usuário quando o usuário não aprova a demanda de autorização solicitada.</span><span class="sxs-lookup"><span data-stu-id="1489b-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="1489b-149">O código a seguir define `AccessDeniedPath` o `"/AccessDeniedPathInfo"`para:</span><span class="sxs-lookup"><span data-stu-id="1489b-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="1489b-150">Recomendamos que `AccessDeniedPath` a página contenha as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="1489b-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="1489b-151">A autenticação remota foi cancelada.</span><span class="sxs-lookup"><span data-stu-id="1489b-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="1489b-152">Este aplicativo requer autenticação.</span><span class="sxs-lookup"><span data-stu-id="1489b-152">This app requires authentication.</span></span>
* <span data-ttu-id="1489b-153">Para tentar entrar novamente, selecione o link de logon.</span><span class="sxs-lookup"><span data-stu-id="1489b-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="1489b-154">Testar AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="1489b-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="1489b-155">Navegue até [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="1489b-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="1489b-156">Se você estiver conectado, deverá sair.</span><span class="sxs-lookup"><span data-stu-id="1489b-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="1489b-157">Execute o aplicativo e selecione entrada no Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="1489b-158">Selecione **não agora**.</span><span class="sxs-lookup"><span data-stu-id="1489b-158">Select **Not now**.</span></span> <span data-ttu-id="1489b-159">Você será redirecionado para a página `AccessDeniedPath` especificada.</span><span class="sxs-lookup"><span data-stu-id="1489b-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="1489b-160">Consulte a referência da API do [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração com suporte na autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="1489b-161">As opções de configuração podem ser usadas para:</span><span class="sxs-lookup"><span data-stu-id="1489b-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="1489b-162">Solicite informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="1489b-162">Request different information about the user.</span></span>
* <span data-ttu-id="1489b-163">Adicione argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.</span><span class="sxs-lookup"><span data-stu-id="1489b-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1489b-164">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="1489b-164">Troubleshooting</span></span>

* <span data-ttu-id="1489b-165">**Somente ASP.NET Core 2. x:** Se Identity não estiver configurado chamando `services.AddIdentity` em `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="1489b-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="1489b-166">O modelo de projeto usado neste tutorial garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="1489b-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="1489b-167">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* .</span><span class="sxs-lookup"><span data-stu-id="1489b-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="1489b-168">Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="1489b-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1489b-169">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="1489b-169">Next steps</span></span>

* <span data-ttu-id="1489b-170">Este artigo mostrou como você pode autenticar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="1489b-171">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1489b-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="1489b-172">Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="1489b-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="1489b-173">Defina `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="1489b-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="1489b-174">O sistema de configuração é configurado para ler chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1489b-174">The configuration system is set up to read keys from environment variables.</span></span>
