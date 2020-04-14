---
title: Configuração de login externo da Conta Microsoft com ASP.NET Core
author: rick-anderson
description: Esta amostra demonstra a integração da autenticação do usuário da conta Microsoft em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277243"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="f3495-103">Configuração de login externo da Conta Microsoft com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3495-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="f3495-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3495-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3495-105">Esta amostra mostra como permitir que os usuários entrem com sua conta Microsoft usando o projeto ASP.NET Core 3.0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f3495-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="f3495-106">Crie o aplicativo no Microsoft Developer Portal</span><span class="sxs-lookup"><span data-stu-id="f3495-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="f3495-107">Adicione o pacote [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="f3495-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="f3495-108">Navegue até o [portal Azure -](https://go.microsoft.com/fwlink/?linkid=2083908) Página de registros de aplicativos e crie ou faça login em uma conta da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="f3495-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="f3495-109">Se você não tiver uma conta Microsoft, selecione **Criar uma**.</span><span class="sxs-lookup"><span data-stu-id="f3495-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="f3495-110">Após fazer login, você é redirecionado para a página de registros do **Aplicativo:**</span><span class="sxs-lookup"><span data-stu-id="f3495-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="f3495-111">Selecione **Novo registro**</span><span class="sxs-lookup"><span data-stu-id="f3495-111">Select **New registration**</span></span>
* <span data-ttu-id="f3495-112">Digite um **nome**.</span><span class="sxs-lookup"><span data-stu-id="f3495-112">Enter a **Name**.</span></span>
* <span data-ttu-id="f3495-113">Selecione uma opção para **tipos de conta suportados**.</span><span class="sxs-lookup"><span data-stu-id="f3495-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="f3495-114">Em **Redirecionar URI,** insira sua URL de desenvolvimento com `/signin-microsoft` anexo.</span><span class="sxs-lookup"><span data-stu-id="f3495-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="f3495-115">Por exemplo, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="f3495-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="f3495-116">O esquema de autenticação da Microsoft configurado posteriormente nesta amostra lidará automaticamente com solicitações na `/signin-microsoft` rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="f3495-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="f3495-117">Selecionar **Registro**</span><span class="sxs-lookup"><span data-stu-id="f3495-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="f3495-118">Criar segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="f3495-118">Create client secret</span></span>

* <span data-ttu-id="f3495-119">No painel esquerdo, selecione **Certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="f3495-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="f3495-120">Em **segredos do Cliente,** selecione **Novo segredo do cliente**</span><span class="sxs-lookup"><span data-stu-id="f3495-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="f3495-121">Adicione uma descrição para o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f3495-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="f3495-122">Selecione o botão **Adicionar.**</span><span class="sxs-lookup"><span data-stu-id="f3495-122">Select the **Add** button.</span></span>

* <span data-ttu-id="f3495-123">Em **segredos do cliente,** copie o valor do segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f3495-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="f3495-124">O segmento `/signin-microsoft` URI é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f3495-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="f3495-125">Você pode alterar o URI de retorno de chamada padrão enquanto configura o middleware de autenticação da Microsoft através da propriedade [Herdada RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="f3495-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="f3495-126">Armazene o ID do cliente da Microsoft e o segredo</span><span class="sxs-lookup"><span data-stu-id="f3495-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="f3495-127">Armazene configurações sensíveis, como o ID do cliente da Microsoft e valores secretos com [o Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f3495-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="f3495-128">Para esta amostra, use as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="f3495-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="f3495-129">Inicialize o projeto para armazenamento secreto de acordo com as instruções em [Habilitar o armazenamento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="f3495-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="f3495-130">Armazene as configurações sensíveis na loja `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret`secreta local com as chaves secretas e:</span><span class="sxs-lookup"><span data-stu-id="f3495-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="f3495-131">Configurar a autenticação da conta microsoft</span><span class="sxs-lookup"><span data-stu-id="f3495-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="f3495-132">Adicione o serviço da `Startup.ConfigureServices`Conta Microsoft ao:</span><span class="sxs-lookup"><span data-stu-id="f3495-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="f3495-133">Para obter mais informações sobre as opções de configuração suportadas pela autenticação da Conta Microsoft, consulte a referência da API do [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="f3495-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="f3495-134">Isso pode ser usado para solicitar diferentes informações sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="f3495-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="f3495-135">Faça login com a Conta Microsoft</span><span class="sxs-lookup"><span data-stu-id="f3495-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="f3495-136">Execute o aplicativo e clique **em Fazer login**.</span><span class="sxs-lookup"><span data-stu-id="f3495-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="f3495-137">Uma opção para fazer login com a Microsoft aparece.</span><span class="sxs-lookup"><span data-stu-id="f3495-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="f3495-138">Quando você clica na Microsoft, você é redirecionado para a Microsoft para autenticação.</span><span class="sxs-lookup"><span data-stu-id="f3495-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="f3495-139">Depois de fazer login com sua Conta Microsoft, você será solicitado a permitir que o aplicativo acesse suas informações:</span><span class="sxs-lookup"><span data-stu-id="f3495-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="f3495-140">Toque **em Sim** e você será redirecionado de volta para o site onde você pode definir seu e-mail.</span><span class="sxs-lookup"><span data-stu-id="f3495-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="f3495-141">Agora você está logado usando suas credenciais da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="f3495-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="f3495-142">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="f3495-142">Troubleshooting</span></span>

* <span data-ttu-id="f3495-143">Se o provedor da Conta Microsoft redirecionar você para uma página de erro, observe `#` os parâmetros de seqüência de consulta de erro e de descrição seguindo diretamente a (hashtag) no Uri.</span><span class="sxs-lookup"><span data-stu-id="f3495-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="f3495-144">Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o seu aplicativo Uri não corresponder a nenhuma das **URIs de redirecionamento** especificadas para a plataforma **Web.**</span><span class="sxs-lookup"><span data-stu-id="f3495-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="f3495-145">Se a identidade não estiver `services.AddIdentity` `ConfigureServices`configurada por chamada, tentar autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="f3495-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="f3495-146">O modelo de projeto usado nesta amostra garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="f3495-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="f3495-147">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você terá falha da operação de banco de dados A durante o processamento do erro *de solicitação.*</span><span class="sxs-lookup"><span data-stu-id="f3495-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="f3495-148">Toque **em Aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="f3495-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f3495-149">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f3495-149">Next steps</span></span>

* <span data-ttu-id="f3495-150">Este artigo mostrou como você pode autenticar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f3495-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="f3495-151">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f3495-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="f3495-152">Depois de publicar seu site no aplicativo web Do Azure, crie um novo cliente no Portal de Desenvolvedores da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f3495-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="f3495-153">Defina `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` as configurações do aplicativo no portal Azure.</span><span class="sxs-lookup"><span data-stu-id="f3495-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="f3495-154">O sistema de configuração está configurado para ler chaves das variáveis do ambiente.</span><span class="sxs-lookup"><span data-stu-id="f3495-154">The configuration system is set up to read keys from environment variables.</span></span>
