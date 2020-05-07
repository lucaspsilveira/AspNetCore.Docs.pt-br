---
title: Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: b451df023bdea3e76725d5d1301f3c3f44ea5d99
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876199"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="ae114-102">Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="ae114-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="ae114-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ae114-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="ae114-104">Este artigo descreve como criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="ae114-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="ae114-105">Registrar aplicativos em AAD B2C e criar solução</span><span class="sxs-lookup"><span data-stu-id="ae114-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="ae114-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="ae114-106">Create a tenant</span></span>

<span data-ttu-id="ae114-107">Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C e registrar as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="ae114-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="ae114-108">Instância de AAD B2C (por exemplo `https://contoso.b2clogin.com/`,, que inclui a barra à direita)</span><span class="sxs-lookup"><span data-stu-id="ae114-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="ae114-109">AAD B2C domínio de locatário (por exemplo `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="ae114-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="ae114-110">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="ae114-110">Register a server API app</span></span>

<span data-ttu-id="ae114-111">Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar um aplicativo do AAD para o *aplicativo de API do servidor* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="ae114-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="ae114-112">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="ae114-112">Select **New registration**.</span></span>
1. <span data-ttu-id="ae114-113">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="ae114-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ae114-114">Para **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="ae114-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="ae114-115">(multilocatário) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="ae114-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="ae114-116">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="ae114-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="ae114-117">Confirme se **as permissões** > **concedem a decento do administrador a OpenID e offline_access permissões** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="ae114-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ae114-118">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="ae114-118">Select **Register**.</span></span>

<span data-ttu-id="ae114-119">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="ae114-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="ae114-120">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="ae114-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="ae114-121">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="ae114-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="ae114-122">Forneça um **nome de escopo** (por exemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="ae114-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ae114-123">Forneça um **nome de exibição de consentimento do administrador** ( `Access API`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="ae114-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="ae114-124">Forneça uma **Descrição de consentimento do administrador** (por `Allows the app to access server app API endpoints.`exemplo,).</span><span class="sxs-lookup"><span data-stu-id="ae114-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="ae114-125">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="ae114-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="ae114-126">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="ae114-126">Select **Add scope**.</span></span>

<span data-ttu-id="ae114-127">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="ae114-127">Record the following information:</span></span>

* <span data-ttu-id="ae114-128">*Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por `11111111-1111-1111-1111-111111111111`exemplo,)</span><span class="sxs-lookup"><span data-stu-id="ae114-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="ae114-129">URI da ID do aplicativo (por `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`exemplo `api://11111111-1111-1111-1111-111111111111`,, ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="ae114-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="ae114-130">ID do diretório (ID do locatário) (por `222222222-2222-2222-2222-222222222222`exemplo,)</span><span class="sxs-lookup"><span data-stu-id="ae114-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="ae114-131">*Aplicativo de API do servidor* URI da ID do aplicativo (por `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`exemplo, o portal do Azure pode padronizar o valor para a ID do cliente)</span><span class="sxs-lookup"><span data-stu-id="ae114-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="ae114-132">Escopo padrão (por exemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="ae114-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="ae114-133">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="ae114-133">Register a client app</span></span>

<span data-ttu-id="ae114-134">Siga as orientações em [tutorial: registrar um aplicativo em Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *aplicativo cliente* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="ae114-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="ae114-135">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="ae114-135">Select **New registration**.</span></span>
1. <span data-ttu-id="ae114-136">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor AAD B2C cliente\*\*).</span><span class="sxs-lookup"><span data-stu-id="ae114-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="ae114-137">Para **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="ae114-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="ae114-138">(multilocatário) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="ae114-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="ae114-139">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI `https://localhost:5001/authentication/login-callback`de redirecionamento de.</span><span class="sxs-lookup"><span data-stu-id="ae114-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="ae114-140">Confirme se **as permissões** > **concedem a decento do administrador a OpenID e offline_access permissões** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="ae114-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ae114-141">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="ae114-141">Select **Register**.</span></span>

<span data-ttu-id="ae114-142">Em **Authentication** > **Platform configurations**configurações > **da**plataforma de autenticação Web:</span><span class="sxs-lookup"><span data-stu-id="ae114-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="ae114-143">Confirme se o URI de `https://localhost:5001/authentication/login-callback` **redirecionamento** do está presente.</span><span class="sxs-lookup"><span data-stu-id="ae114-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="ae114-144">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="ae114-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="ae114-145">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="ae114-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="ae114-146">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="ae114-146">Select the **Save** button.</span></span>

<span data-ttu-id="ae114-147">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="ae114-147">In **API permissions**:</span></span>

1. <span data-ttu-id="ae114-148">Confirme se o aplicativo tem a permissão **Microsoft Graph** > **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="ae114-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="ae114-149">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="ae114-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="ae114-150">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="ae114-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ae114-151">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="ae114-151">Open the **API** list.</span></span>
1. <span data-ttu-id="ae114-152">Habilite o acesso à API (por exemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="ae114-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ae114-153">Selecione **Adicionar Permissões**.</span><span class="sxs-lookup"><span data-stu-id="ae114-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="ae114-154">Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="ae114-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="ae114-155">Selecione **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="ae114-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="ae114-156">Em **casa** > **Azure ad B2C** > **fluxos de usuário**:</span><span class="sxs-lookup"><span data-stu-id="ae114-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="ae114-157">Criar um fluxo de usuário de inscrição e de entrada</span><span class="sxs-lookup"><span data-stu-id="ae114-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="ae114-158">No mínimo, selecione o atributo de usuário**nome de exibição** de **declarações** > do aplicativo `context.User.Identity.Name` para popular `LoginDisplay` o no componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ae114-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="ae114-159">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="ae114-159">Record the following information:</span></span>

* <span data-ttu-id="ae114-160">Registre a ID do aplicativo de aplicativo *cliente* (ID do cliente) ( `33333333-3333-3333-3333-333333333333`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="ae114-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="ae114-161">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="ae114-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="ae114-162">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ae114-162">Create the app</span></span>

<span data-ttu-id="ae114-163">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="ae114-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="ae114-164">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="ae114-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ae114-165">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="ae114-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="ae114-166">Passe o URI da ID do aplicativo `app-id-uri` para a opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="ae114-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="ae114-167">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="ae114-167">Server app configuration</span></span>

<span data-ttu-id="ae114-168">*Esta seção pertence ao aplicativo de **servidor** da solução.*</span><span class="sxs-lookup"><span data-stu-id="ae114-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ae114-169">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="ae114-169">Authentication package</span></span>

<span data-ttu-id="ae114-170">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span><span class="sxs-lookup"><span data-stu-id="ae114-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="ae114-171">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="ae114-171">Authentication service support</span></span>

<span data-ttu-id="ae114-172">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="ae114-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="ae114-173">O `AddAzureADB2CBearer` método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="ae114-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="ae114-174">`UseAuthentication`e `UseAuthorization` Verifique se:</span><span class="sxs-lookup"><span data-stu-id="ae114-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="ae114-175">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="ae114-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="ae114-176">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="ae114-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="ae114-177">Usuário. Identity. Nomes</span><span class="sxs-lookup"><span data-stu-id="ae114-177">User.Identity.Name</span></span>

<span data-ttu-id="ae114-178">Por padrão, o `User.Identity.Name` não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="ae114-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="ae114-179">Para configurar o aplicativo para receber o valor do tipo `name` de declaração, configure o [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ae114-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="ae114-180">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ae114-180">App settings</span></span>

<span data-ttu-id="ae114-181">O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="ae114-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="ae114-182">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="ae114-182">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="ae114-183">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="ae114-183">WeatherForecast controller</span></span>

<span data-ttu-id="ae114-184">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida `[Authorize]` com o atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="ae114-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="ae114-185">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="ae114-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="ae114-186">O `[Authorize]` atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="ae114-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="ae114-187">O `[Authorize]` atributo usado no Blazor aplicativo Webassembly serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="ae114-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="ae114-188">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="ae114-188">Client app configuration</span></span>

<span data-ttu-id="ae114-189">*Esta seção pertence ao aplicativo **cliente** da solução.*</span><span class="sxs-lookup"><span data-stu-id="ae114-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ae114-190">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="ae114-190">Authentication package</span></span>

<span data-ttu-id="ae114-191">Quando um aplicativo é criado para usar uma conta individual do B2C`IndividualB2C`(), o aplicativo recebe automaticamente uma referência de pacote para a biblioteca de`Microsoft.Authentication.WebAssembly.Msal`autenticação da [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="ae114-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="ae114-192">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="ae114-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ae114-193">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ae114-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="ae114-194">Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="ae114-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="ae114-195">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ae114-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="ae114-196">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="ae114-196">Authentication service support</span></span>

<span data-ttu-id="ae114-197">O suporte `HttpClient` para instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="ae114-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ae114-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae114-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="ae114-199">O suporte para autenticação de usuários é registrado no contêiner de serviço `AddMsalAuthentication` com o método de extensão `Microsoft.Authentication.WebAssembly.Msal` fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="ae114-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="ae114-200">Esse método configura todos os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="ae114-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ae114-201">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae114-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="ae114-202">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ae114-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="ae114-203">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ae114-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="ae114-204">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ae114-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="ae114-205">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="ae114-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="ae114-206">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="ae114-206">Access token scopes</span></span>

<span data-ttu-id="ae114-207">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="ae114-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="ae114-208">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="ae114-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="ae114-209">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="ae114-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="ae114-210">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ae114-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="ae114-211">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="ae114-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="ae114-212">Se o portal do Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção sem tratamento** quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="ae114-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="ae114-213">Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:</span><span class="sxs-lookup"><span data-stu-id="ae114-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="ae114-214">Forneça o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="ae114-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="ae114-215">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="ae114-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="ae114-216">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="ae114-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="ae114-217">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="ae114-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="ae114-218">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="ae114-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="ae114-219">Página de índice</span><span class="sxs-lookup"><span data-stu-id="ae114-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="ae114-220">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ae114-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="ae114-221">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="ae114-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="ae114-222">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="ae114-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="ae114-223">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="ae114-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="ae114-224">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="ae114-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="ae114-225">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ae114-225">Run the app</span></span>

<span data-ttu-id="ae114-226">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="ae114-226">Run the app from the Server project.</span></span> <span data-ttu-id="ae114-227">Ao usar o Visual Studio, selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="ae114-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ae114-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ae114-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="ae114-229">Tutorial - Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="ae114-229">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="ae114-230">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="ae114-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
