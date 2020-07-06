---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 3f5fa42aea0af8880f9598f2e923b4e62481a797
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944309"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="26314-102">Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="26314-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="26314-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="26314-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="26314-104">Este artigo descreve como criar um Blazor WebAssembly aplicativo autônomo que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="26314-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="26314-105">Registrar aplicativos em AAD B2C e criar solução</span><span class="sxs-lookup"><span data-stu-id="26314-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="26314-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="26314-106">Create a tenant</span></span>

<span data-ttu-id="26314-107">Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="26314-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="26314-108">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="26314-108">Record the following information:</span></span>

* <span data-ttu-id="26314-109">Instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/` , que inclui a barra à direita)</span><span class="sxs-lookup"><span data-stu-id="26314-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="26314-110">AAD B2C domínio de locatário (por exemplo, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="26314-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="26314-111">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="26314-111">Register a server API app</span></span>

<span data-ttu-id="26314-112">Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar um aplicativo do AAD para o *aplicativo de API do servidor* e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="26314-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="26314-113">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="26314-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="26314-114">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="26314-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="26314-115">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="26314-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="26314-116">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="26314-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="26314-117">Confirme se **as permissões**  >  **concedem consentimento de administrador para OpenID e offline_access permissões** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="26314-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="26314-118">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="26314-118">Select **Register**.</span></span>

<span data-ttu-id="26314-119">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="26314-119">Record the following information:</span></span>

* <span data-ttu-id="26314-120">*Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="26314-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="26314-121">ID do diretório (ID do locatário) (por exemplo, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="26314-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="26314-122">Domínio de locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="26314-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="26314-123">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="26314-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="26314-124">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="26314-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="26314-125">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="26314-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="26314-126">Forneça um **nome de escopo** (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="26314-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="26314-127">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="26314-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="26314-128">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="26314-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="26314-129">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="26314-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="26314-130">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="26314-130">Select **Add scope**.</span></span>

<span data-ttu-id="26314-131">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="26314-131">Record the following information:</span></span>

* <span data-ttu-id="26314-132">URI da ID do aplicativo (por exemplo,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="26314-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="26314-133">Escopo padrão (por exemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="26314-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="26314-134">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="26314-134">Register a client app</span></span>

<span data-ttu-id="26314-135">Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *aplicativo cliente* e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="26314-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="26314-136">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="26314-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="26314-137">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor AAD B2C cliente\*\*).</span><span class="sxs-lookup"><span data-stu-id="26314-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="26314-138">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="26314-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="26314-139">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="26314-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="26314-140">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="26314-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="26314-141">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26314-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="26314-142">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo do servidor no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="26314-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="26314-143">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="26314-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="26314-144">Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="26314-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="26314-145">Confirme se **as permissões**  >  **concedem consentimento de administrador para OpenID e offline_access permissões** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="26314-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="26314-146">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="26314-146">Select **Register**.</span></span>

<span data-ttu-id="26314-147">Registre a ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="26314-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="26314-148">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="26314-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="26314-149">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="26314-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="26314-150">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="26314-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="26314-151">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="26314-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="26314-152">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="26314-152">Select the **Save** button.</span></span>

<span data-ttu-id="26314-153">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="26314-153">In **API permissions**:</span></span>

1. <span data-ttu-id="26314-154">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="26314-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="26314-155">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="26314-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="26314-156">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="26314-156">Open the **API** list.</span></span>
1. <span data-ttu-id="26314-157">Habilite o acesso à API (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="26314-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="26314-158">Selecione **Adicionar Permissões**.</span><span class="sxs-lookup"><span data-stu-id="26314-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="26314-159">Selecione o botão **conceder consentimento de administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="26314-159">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="26314-160">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="26314-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="26314-161">Em **casa**  >  **Azure ad B2C**  >  **fluxos de usuário**:</span><span class="sxs-lookup"><span data-stu-id="26314-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="26314-162">Criar um fluxo de usuário de inscrição e de entrada</span><span class="sxs-lookup"><span data-stu-id="26314-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="26314-163">No mínimo, selecione o atributo de usuário nome de exibição de **declarações do aplicativo**  >  **Display Name** para popular o `context.User.Identity.Name` no `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="26314-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="26314-164">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="26314-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="26314-165">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="26314-165">Create the app</span></span>

<span data-ttu-id="26314-166">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="26314-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="26314-167">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="26314-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="26314-168">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="26314-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="26314-169">Passe o URI da ID do aplicativo para a `app-id-uri` opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="26314-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="26314-170">Além disso, o escopo configurado pelo modelo hospedado Blazor pode ter o host de URI de ID de aplicativo repetido.</span><span class="sxs-lookup"><span data-stu-id="26314-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="26314-171">Confirme se o escopo configurado para a `DefaultAccessTokenScopes` coleção está correto em `Program.Main` ( `Program.cs` ) do *aplicativo cliente*.</span><span class="sxs-lookup"><span data-stu-id="26314-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="26314-172">No portal do Azure, o URI de redirecionamento da Web de configurações da plataforma de autenticação *do aplicativo cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="26314-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="26314-173">Se o *aplicativo cliente* for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades *do aplicativo do servidor* no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="26314-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="26314-174">Se a porta não foi configurada anteriormente com a porta conhecida *do aplicativo cliente* , retorne ao registro *do aplicativo cliente* no portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="26314-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="26314-175">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="26314-175">Server app configuration</span></span>

<span data-ttu-id="26314-176">*Esta seção pertence ao aplicativo da solução **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="26314-176">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="26314-177">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="26314-177">Authentication package</span></span>

<span data-ttu-id="26314-178">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) pacote:</span><span class="sxs-lookup"><span data-stu-id="26314-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="26314-179">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="26314-179">Authentication service support</span></span>

<span data-ttu-id="26314-180">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="26314-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="26314-181">O <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="26314-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="26314-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Verifique se:</span><span class="sxs-lookup"><span data-stu-id="26314-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="26314-183">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="26314-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="26314-184">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="26314-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="26314-185">Usuário. Identity . Nomes</span><span class="sxs-lookup"><span data-stu-id="26314-185">User.Identity.Name</span></span>

<span data-ttu-id="26314-186">Por padrão, o `User.Identity.Name` não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="26314-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="26314-187">Para configurar o aplicativo para receber o valor do `name` tipo de declaração, configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26314-187">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="26314-188">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="26314-188">App settings</span></span>

<span data-ttu-id="26314-189">O `appsettings.json` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="26314-189">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="26314-190">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="26314-190">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="26314-191">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="26314-191">WeatherForecast controller</span></span>

<span data-ttu-id="26314-192">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="26314-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="26314-193">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="26314-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="26314-194">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="26314-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="26314-195">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo usado no Blazor WebAssembly aplicativo serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="26314-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="26314-196">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="26314-196">Client app configuration</span></span>

<span data-ttu-id="26314-197">*Esta seção pertence ao aplicativo da solução **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="26314-197">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="26314-198">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="26314-198">Authentication package</span></span>

<span data-ttu-id="26314-199">Quando um aplicativo é criado para usar uma conta individual do B2C ( `IndividualB2C` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="26314-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="26314-200">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="26314-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="26314-201">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="26314-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="26314-202">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26314-202">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="26314-203">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="26314-203">Authentication service support</span></span>

<span data-ttu-id="26314-204">O suporte para <xref:System.Net.Http.HttpClient> instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="26314-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="26314-205">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="26314-205">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="26314-206">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="26314-206">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="26314-207">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote.</span><span class="sxs-lookup"><span data-stu-id="26314-207">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="26314-208">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="26314-208">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="26314-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="26314-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="26314-210">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26314-210">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="26314-211">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26314-211">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="26314-212">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="26314-212">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="26314-213">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="26314-213">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="26314-214">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="26314-214">Access token scopes</span></span>

<span data-ttu-id="26314-215">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="26314-215">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="26314-216">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="26314-216">Included by default in the sign in request.</span></span>
* <span data-ttu-id="26314-217">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="26314-217">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="26314-218">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="26314-218">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="26314-219">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="26314-219">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="26314-220">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="26314-220">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="26314-221">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="26314-221">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="26314-222">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="26314-222">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="26314-223">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="26314-223">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="26314-224">Página de índice</span><span class="sxs-lookup"><span data-stu-id="26314-224">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="26314-225">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="26314-225">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="26314-226">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="26314-226">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="26314-227">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="26314-227">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="26314-228">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="26314-228">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="26314-229">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="26314-229">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="26314-230">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="26314-230">Run the app</span></span>

<span data-ttu-id="26314-231">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="26314-231">Run the app from the Server project.</span></span> <span data-ttu-id="26314-232">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="26314-232">When using Visual Studio, either:</span></span>

* <span data-ttu-id="26314-233">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="26314-233">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="26314-234">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="26314-234">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="26314-235">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="26314-235">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="26314-236">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="26314-236">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="26314-237">Tutorial - Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="26314-237">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="26314-238">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="26314-238">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)