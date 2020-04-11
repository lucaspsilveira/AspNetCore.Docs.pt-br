---
title: Garanta um Blazor aplicativo hospedado ASP.NET WebAssembly do Core Com o Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123462"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="dd006-102">Garanta um Blazor aplicativo hospedado ASP.NET WebAssembly do Core Com o Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="dd006-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="dd006-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dd006-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="dd006-104">Este artigo descreve como Blazor criar um aplicativo autônomo do WebAssembly que usa [o AaD (AAD) Active Directory (AAD)](/azure/active-directory-b2c/overview) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="dd006-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="dd006-105">Registre aplicativos no AAD B2C e crie solução</span><span class="sxs-lookup"><span data-stu-id="dd006-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="dd006-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="dd006-106">Create a tenant</span></span>

<span data-ttu-id="dd006-107">Siga a orientação no [Tutorial: Crie um inquilino B2C do Azure Active Directory](/azure/active-directory-b2c/tutorial-create-tenant) para criar um inquilino AAD B2C e registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="dd006-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="dd006-108">Aad b2C exemplo (por exemplo, `https://contoso.b2clogin.com/`que inclui a barra de arrasto)</span><span class="sxs-lookup"><span data-stu-id="dd006-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="dd006-109">Domínio do inquilino AAD B2C (por exemplo, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="dd006-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="dd006-110">Registre um aplicativo de API de servidor</span><span class="sxs-lookup"><span data-stu-id="dd006-110">Register a server API app</span></span>

<span data-ttu-id="dd006-111">Siga a orientação no [Tutorial: Registre um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar um aplicativo AAD para o *aplicativo API do servidor* na área de inscrições do **Azure Active Directory** > **App registrations** do portal Azure:</span><span class="sxs-lookup"><span data-stu-id="dd006-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="dd006-112">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="dd006-112">Select **New registration**.</span></span>
1. <span data-ttu-id="dd006-113">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="dd006-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="dd006-114">Para **tipos de conta suportados,** selecione Contas em qualquer diretório organizacional ou qualquer provedor de **identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="dd006-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="dd006-115">(multi-inquilino) para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="dd006-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="dd006-116">O *aplicativo API do servidor* não requer um URI de **redirecionamento** neste cenário, portanto, deixe o set de desada para **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="dd006-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="dd006-117">Confirme se **as permissões** > **de autorização de entrada de entrada para permissões openid e offline_access** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="dd006-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="dd006-118">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="dd006-118">Select **Register**.</span></span>

<span data-ttu-id="dd006-119">Em **Expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="dd006-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="dd006-120">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="dd006-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="dd006-121">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="dd006-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="dd006-122">Forneça um **nome de** `API.Access`escopo (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="dd006-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="dd006-123">Forneça **um nome de exibição de consentimento de admin** (por exemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="dd006-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="dd006-124">Fornecer **uma descrição de consentimento do Admin** (por exemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="dd006-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="dd006-125">Confirme se o **Estado** está definido **como Ativado**.</span><span class="sxs-lookup"><span data-stu-id="dd006-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="dd006-126">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="dd006-126">Select **Add scope**.</span></span>

<span data-ttu-id="dd006-127">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="dd006-127">Record the following information:</span></span>

* <span data-ttu-id="dd006-128">*Aplicativo de API do servidor* ID do aplicativo (ID do `11111111-1111-1111-1111-111111111111`cliente) (por exemplo, )</span><span class="sxs-lookup"><span data-stu-id="dd006-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="dd006-129">App ID URI (por exemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="dd006-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="dd006-130">ID do diretório (ID do `222222222-2222-2222-2222-222222222222`inquilino) (por exemplo, )</span><span class="sxs-lookup"><span data-stu-id="dd006-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="dd006-131">*Aplicativo de API do servidor* App ID URI (por exemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`o portal Azure pode padrão o valor para o ID do cliente)</span><span class="sxs-lookup"><span data-stu-id="dd006-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="dd006-132">Escopo padrão (por `API.Access`exemplo, )</span><span class="sxs-lookup"><span data-stu-id="dd006-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="dd006-133">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="dd006-133">Register a client app</span></span>

<span data-ttu-id="dd006-134">Siga a orientação no [Tutorial: Registre um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo AAD para o *aplicativo Cliente* na área de registros do **Azure Active Directory** > **App do** portal Azure:</span><span class="sxs-lookup"><span data-stu-id="dd006-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="dd006-135">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="dd006-135">Select **New registration**.</span></span>
1. <span data-ttu-id="dd006-136">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Client AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="dd006-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="dd006-137">Para **tipos de conta suportados,** selecione Contas em qualquer diretório organizacional ou qualquer provedor de **identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="dd006-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="dd006-138">(multi-inquilino) para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="dd006-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="dd006-139">Deixe o **URI redirecionar** definido para **web**e forneça `https://localhost:5001/authentication/login-callback`um URI redirecionado de .</span><span class="sxs-lookup"><span data-stu-id="dd006-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="dd006-140">Confirme se **as permissões** > **de autorização de entrada de entrada para permissões openid e offline_access** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="dd006-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="dd006-141">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="dd006-141">Select **Register**.</span></span>

<span data-ttu-id="dd006-142">Em**configurações** > da plataforma **de autenticação** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="dd006-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="dd006-143">Confirme se o `https://localhost:5001/authentication/login-callback` **URI redirecionamento** está presente.</span><span class="sxs-lookup"><span data-stu-id="dd006-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="dd006-144">Para **conceder implicitamente,** selecione as caixas de seleção **para tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="dd006-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="dd006-145">Os demais padrões para o aplicativo são aceitáveis para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="dd006-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="dd006-146">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="dd006-146">Select the **Save** button.</span></span>

<span data-ttu-id="dd006-147">Em **permissões de API:**</span><span class="sxs-lookup"><span data-stu-id="dd006-147">In **API permissions**:</span></span>

1. <span data-ttu-id="dd006-148">Confirme se o aplicativo tem permissão **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="dd006-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="dd006-149">Selecione **Adicionar uma permissão** seguida por **Minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="dd006-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="dd006-150">Selecione o *aplicativo API* do servidor na coluna **Nome** (por exemplo, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="dd006-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="dd006-151">Abra a **lista de API.**</span><span class="sxs-lookup"><span data-stu-id="dd006-151">Open the **API** list.</span></span>
1. <span data-ttu-id="dd006-152">Habilite o acesso à `API.Access`API (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="dd006-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="dd006-153">Selecione **Adicionar Permissões**.</span><span class="sxs-lookup"><span data-stu-id="dd006-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="dd006-154">Selecione o **conteúdo de admin do Grant para o** botão {TENANT NAME}.</span><span class="sxs-lookup"><span data-stu-id="dd006-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="dd006-155">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="dd006-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="dd006-156">Em **home** > **Azure AD B2C** > **Fluxos de usuário**:</span><span class="sxs-lookup"><span data-stu-id="dd006-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="dd006-157">Criar um fluxo de usuário de inscrição e de entrada</span><span class="sxs-lookup"><span data-stu-id="dd006-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="dd006-158">No mínimo, selecione o **atributo** > usuário 'Nome de**exibição'** do aplicativo para preencher o `context.User.Identity.Name` `LoginDisplay` componente *(Compartilhado/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="dd006-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="dd006-159">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="dd006-159">Record the following information:</span></span>

* <span data-ttu-id="dd006-160">Registre o *ID* do aplicativo do aplicativo cliente `33333333-3333-3333-3333-333333333333`(ID do cliente) (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="dd006-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="dd006-161">Registre o nome de fluxo de usuário de inscrição e login `B2C_1_signupsignin`criado para o aplicativo (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="dd006-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="dd006-162">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dd006-162">Create the app</span></span>

<span data-ttu-id="dd006-163">Substitua os espaços reservados no seguinte comando com as informações gravadas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="dd006-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="dd006-164">Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="dd006-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="dd006-165">O nome da pasta também passa a fazer parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="dd006-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="dd006-166">Passe o APP ID `app-id-uri` URI para a opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo do cliente, que está descrito na seção [Escopos de token de acesso.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="dd006-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="dd006-167">Configuração do aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="dd006-167">Server app configuration</span></span>

<span data-ttu-id="dd006-168">*Esta seção diz respeito ao aplicativo **Server** da solução.*</span><span class="sxs-lookup"><span data-stu-id="dd006-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="dd006-169">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="dd006-169">Authentication package</span></span>

<span data-ttu-id="dd006-170">O suporte para autenticar e autorizar chamadas para ASP.NET ASCS da Web principal é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span><span class="sxs-lookup"><span data-stu-id="dd006-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="dd006-171">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="dd006-171">Authentication service support</span></span>

<span data-ttu-id="dd006-172">O `AddAuthentication` método configura serviços de autenticação dentro do aplicativo e configura o manipulador JWT Bearer como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="dd006-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="dd006-173">O `AddAzureADB2CBearer` método define os parâmetros específicos no manipulador JWT Bearer necessários para validar tokens emitidos pelo Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="dd006-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="dd006-174">`UseAuthentication`e `UseAuthorization` garantir que:</span><span class="sxs-lookup"><span data-stu-id="dd006-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="dd006-175">O aplicativo tenta analisar e validar tokens em solicitações recebidas.</span><span class="sxs-lookup"><span data-stu-id="dd006-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="dd006-176">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falha.</span><span class="sxs-lookup"><span data-stu-id="dd006-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="dd006-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="dd006-177">User.Identity.Name</span></span>

<span data-ttu-id="dd006-178">Por padrão, `User.Identity.Name` o não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="dd006-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="dd006-179">Para configurar o aplicativo para receber `name` o valor do tipo de solicitação, configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`o [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) da in :</span><span class="sxs-lookup"><span data-stu-id="dd006-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="dd006-180">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="dd006-180">App settings</span></span>

<span data-ttu-id="dd006-181">O arquivo *appsettings.json* contém as opções para configurar o manipulador portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="dd006-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="dd006-182">Controlador weatherforecast</span><span class="sxs-lookup"><span data-stu-id="dd006-182">WeatherForecast controller</span></span>

<span data-ttu-id="dd006-183">O controlador WeatherForecast *(Controllers/WeatherForecastController.cs)* expõe uma `[Authorize]` API protegida com o atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="dd006-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="dd006-184">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="dd006-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="dd006-185">O `[Authorize]` atributo neste controlador de API é a única coisa que protege esta API de acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="dd006-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="dd006-186">O `[Authorize]` atributo Blazor usado no aplicativo WebAssembly serve apenas como uma dica para o aplicativo de que o usuário deve ser autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="dd006-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="dd006-187">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="dd006-187">Client app configuration</span></span>

<span data-ttu-id="dd006-188">*Esta seção diz respeito ao aplicativo **Client** da solução.*</span><span class="sxs-lookup"><span data-stu-id="dd006-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="dd006-189">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="dd006-189">Authentication package</span></span>

<span data-ttu-id="dd006-190">Quando um aplicativo é criado para usar uma`IndividualB2C`Conta B2C Individual ( ), o aplicativo recebe automaticamente uma referência de pacote para a [Biblioteca de Autenticação microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="dd006-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="dd006-191">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="dd006-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="dd006-192">Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="dd006-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="dd006-193">Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.</span><span class="sxs-lookup"><span data-stu-id="dd006-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="dd006-194">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd006-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="dd006-195">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="dd006-195">Authentication service support</span></span>

<span data-ttu-id="dd006-196">O suporte para autenticar usuários é registrado `AddMsalAuthentication` no contêiner de `Microsoft.Authentication.WebAssembly.Msal` serviço com o método de extensão fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="dd006-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="dd006-197">Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="dd006-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="dd006-198">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="dd006-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="dd006-199">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd006-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="dd006-200">Os valores necessários para a configuração do aplicativo podem ser obtidos na configuração AAD do Portal Azure ao registrar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd006-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="dd006-201">Acessar escopos de token</span><span class="sxs-lookup"><span data-stu-id="dd006-201">Access token scopes</span></span>

<span data-ttu-id="dd006-202">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="dd006-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="dd006-203">Incluído por padrão no sinal na solicitação.</span><span class="sxs-lookup"><span data-stu-id="dd006-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="dd006-204">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="dd006-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="dd006-205">Todos os escopos devem pertencer ao mesmo aplicativo pelas regras do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="dd006-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="dd006-206">Podem ser adicionados escopos adicionais para aplicativos adicionais de API conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="dd006-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="dd006-207">Se o portal Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção não tratada** quando receber uma resposta não autorizada do *401 Da* API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="dd006-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="dd006-208">Por exemplo, o portal Azure pode fornecer um dos seguintes formatos uri de escopo:</span><span class="sxs-lookup"><span data-stu-id="dd006-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="dd006-209">Forneça o URI de escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="dd006-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="dd006-210">Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="dd006-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="dd006-211">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="dd006-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="dd006-212">Página de índice</span><span class="sxs-lookup"><span data-stu-id="dd006-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="dd006-213">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="dd006-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="dd006-214">Componente RedirecionarToLogin</span><span class="sxs-lookup"><span data-stu-id="dd006-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="dd006-215">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="dd006-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="dd006-216">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="dd006-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="dd006-217">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="dd006-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="dd006-218">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dd006-218">Run the app</span></span>

<span data-ttu-id="dd006-219">Execute o aplicativo do projeto Servidor.</span><span class="sxs-lookup"><span data-stu-id="dd006-219">Run the app from the Server project.</span></span> <span data-ttu-id="dd006-220">Ao usar o Visual Studio, selecione o projeto Servidor no **Solution Explorer** e selecione o botão **Executar** na barra de ferramentas ou iniciar o aplicativo no menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="dd006-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="dd006-221">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dd006-221">Additional resources</span></span>

* [<span data-ttu-id="dd006-222">Solicite tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="dd006-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="dd006-223">Tutorial - Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="dd006-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="dd006-224">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="dd006-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
