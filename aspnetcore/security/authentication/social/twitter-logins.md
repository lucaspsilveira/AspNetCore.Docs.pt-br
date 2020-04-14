---
title: Configuração de login externo do Twitter com ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação do usuário da conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277282"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="c0651-103">Configuração de login externo do Twitter com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0651-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="c0651-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0651-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c0651-105">Esta amostra mostra como permitir que os usuários [entrem com sua conta no Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando uma amostra ASP.NET projeto Core 3.0 criado na página [anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c0651-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="c0651-106">Crie o aplicativo no Twitter</span><span class="sxs-lookup"><span data-stu-id="c0651-106">Create the app in Twitter</span></span>

* <span data-ttu-id="c0651-107">Adicione o pacote [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="c0651-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="c0651-108">Navegue [https://apps.twitter.com/](https://apps.twitter.com/) e faça login.</span><span class="sxs-lookup"><span data-stu-id="c0651-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="c0651-109">Se você ainda não tem uma conta no Twitter, use o link **['Cadastre-se' agora](https://twitter.com/signup)** para criar uma.</span><span class="sxs-lookup"><span data-stu-id="c0651-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="c0651-110">Selecione **Criar um aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="c0651-110">Select **Create an app**.</span></span> <span data-ttu-id="c0651-111">Preencha o **nome do aplicativo,** **a descrição do aplicativo** e o URI do **site** público (isso pode ser temporário até que você registre o nome de domínio):</span><span class="sxs-lookup"><span data-stu-id="c0651-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="c0651-112">Verifique a caixa ao lado **de Ativar entrar no Twitter**</span><span class="sxs-lookup"><span data-stu-id="c0651-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="c0651-113">Microsoft.AspNetCore.Identity exige que os usuários tenham um endereço de e-mail por padrão.</span><span class="sxs-lookup"><span data-stu-id="c0651-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="c0651-114">Vá para a guia **Permissões,** clique no botão **Editar** e verifique a caixa ao lado de **Solicitar endereço de e-mail dos usuários**.</span><span class="sxs-lookup"><span data-stu-id="c0651-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="c0651-115">Insira seu `/signin-twitter` URI de desenvolvimento com anexo no campo `https://webapp128.azurewebsites.net/signin-twitter` **URLs de retorno** de chamada (por exemplo: ).</span><span class="sxs-lookup"><span data-stu-id="c0651-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="c0651-116">O esquema de autenticação do Twitter configurado posteriormente nesta amostra lidará automaticamente com solicitações na `/signin-twitter` rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="c0651-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="c0651-117">O segmento `/signin-twitter` URI é definido como o retorno de chamada padrão do provedor de autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="c0651-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="c0651-118">Você pode alterar o URI de retorno de chamada padrão enquanto configura o middleware de autenticação do Twitter através da propriedade [Herdada RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="c0651-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="c0651-119">Preencha o restante do formulário e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c0651-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="c0651-120">Novos detalhes do aplicativo são exibidos:</span><span class="sxs-lookup"><span data-stu-id="c0651-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="c0651-121">Armazene a chave e o segredo da API do consumidor do Twitter</span><span class="sxs-lookup"><span data-stu-id="c0651-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="c0651-122">Armazene configurações sensíveis, como a chave de API do consumidor do Twitter e secreta com [o Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c0651-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c0651-123">Para esta amostra, use as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="c0651-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="c0651-124">Inicialize o projeto para armazenamento secreto de acordo com as instruções em [Habilitar o armazenamento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="c0651-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="c0651-125">Armazene as configurações sensíveis na loja `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret`secreta local com as chaves de segredos e:</span><span class="sxs-lookup"><span data-stu-id="c0651-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="c0651-126">Esses tokens podem ser encontrados na guia **Chaves e Tokens de Acesso** depois de criar um novo aplicativo do Twitter:</span><span class="sxs-lookup"><span data-stu-id="c0651-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="c0651-127">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="c0651-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="c0651-128">Adicione o serviço `ConfigureServices` do Twitter no método em *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="c0651-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="c0651-129">Consulte a referência da API [do TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obter mais informações sobre as opções de configuração suportadas pela autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="c0651-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="c0651-130">Isso pode ser usado para solicitar diferentes informações sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="c0651-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="c0651-131">Faça login com o Twitter</span><span class="sxs-lookup"><span data-stu-id="c0651-131">Sign in with Twitter</span></span>

<span data-ttu-id="c0651-132">Execute o aplicativo e **selecione Fazer login**.</span><span class="sxs-lookup"><span data-stu-id="c0651-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="c0651-133">Uma opção para fazer login no Twitter aparece:</span><span class="sxs-lookup"><span data-stu-id="c0651-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="c0651-134">Clicar no **Twitter** redireciona para o Twitter para autenticação:</span><span class="sxs-lookup"><span data-stu-id="c0651-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="c0651-135">Depois de inserir suas credenciais do Twitter, você é redirecionado de volta para o site onde você pode definir seu e-mail.</span><span class="sxs-lookup"><span data-stu-id="c0651-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="c0651-136">Agora você está logado usando suas credenciais do Twitter:</span><span class="sxs-lookup"><span data-stu-id="c0651-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="c0651-137">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="c0651-137">Troubleshooting</span></span>

* <span data-ttu-id="c0651-138">**somente ASP.NET Core 2.x:** Se a identidade não estiver `services.AddIdentity` `ConfigureServices`configurada por chamada, tentar autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="c0651-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c0651-139">O modelo de projeto usado nesta amostra garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="c0651-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="c0651-140">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você terá falha da operação de banco de dados A durante o processamento do erro *de solicitação.*</span><span class="sxs-lookup"><span data-stu-id="c0651-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c0651-141">Toque **em Aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="c0651-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c0651-142">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c0651-142">Next steps</span></span>

* <span data-ttu-id="c0651-143">Este artigo mostrou como você pode autenticar com o Twitter.</span><span class="sxs-lookup"><span data-stu-id="c0651-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="c0651-144">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c0651-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c0651-145">Depois de publicar seu site no aplicativo web Do `ConsumerSecret` Azure, você deve redefinir o portal do desenvolvedor do Twitter.</span><span class="sxs-lookup"><span data-stu-id="c0651-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="c0651-146">Defina `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` as configurações do aplicativo no portal Azure.</span><span class="sxs-lookup"><span data-stu-id="c0651-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c0651-147">O sistema de configuração está configurado para ler chaves das variáveis do ambiente.</span><span class="sxs-lookup"><span data-stu-id="c0651-147">The configuration system is set up to read keys from environment variables.</span></span>
