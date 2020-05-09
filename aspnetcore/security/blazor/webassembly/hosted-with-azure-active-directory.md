---
title: Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: aaae2b755d6d6e74db0cb7676820d01964c2add4
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976799"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="e6435-102">Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="e6435-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="e6435-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e6435-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="e6435-104">Este artigo descreve como criar um [ Blazor aplicativo hospedado Webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="e6435-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e6435-105">Registrar aplicativos em AAD B2C e criar solução</span><span class="sxs-lookup"><span data-stu-id="e6435-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e6435-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="e6435-106">Create a tenant</span></span>

<span data-ttu-id="e6435-107">Siga as orientações em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um locatário no AAD.</span><span class="sxs-lookup"><span data-stu-id="e6435-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e6435-108">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="e6435-108">Register a server API app</span></span>

<span data-ttu-id="e6435-109">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo de API do servidor* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="e6435-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e6435-110">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="e6435-110">Select **New registration**.</span></span>
1. <span data-ttu-id="e6435-111">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="e6435-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="e6435-112">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="e6435-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="e6435-113">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="e6435-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="e6435-114">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="e6435-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e6435-115">Desabilite a caixa de seleção **permissões** > **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="e6435-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e6435-116">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="e6435-116">Select **Register**.</span></span>

<span data-ttu-id="e6435-117">Em **permissões de API**, remova a permissão **Microsoft Graph** > **User. Read** , pois o aplicativo não requer acesso ao perfil de entrada ou UER.</span><span class="sxs-lookup"><span data-stu-id="e6435-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="e6435-118">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="e6435-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="e6435-119">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="e6435-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e6435-120">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="e6435-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e6435-121">Forneça um **nome de escopo** (por exemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="e6435-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e6435-122">Forneça um **nome de exibição de consentimento do administrador** ( `Access API`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="e6435-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e6435-123">Forneça uma **Descrição de consentimento do administrador** (por `Allows the app to access server app API endpoints.`exemplo,).</span><span class="sxs-lookup"><span data-stu-id="e6435-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e6435-124">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="e6435-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e6435-125">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="e6435-125">Select **Add scope**.</span></span>

<span data-ttu-id="e6435-126">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="e6435-126">Record the following information:</span></span>

* <span data-ttu-id="e6435-127">*Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por `11111111-1111-1111-1111-111111111111`exemplo,)</span><span class="sxs-lookup"><span data-stu-id="e6435-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e6435-128">URI da ID do aplicativo (por `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`exemplo `api://11111111-1111-1111-1111-111111111111`,, ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="e6435-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="e6435-129">ID do diretório (ID do locatário) (por `222222222-2222-2222-2222-222222222222`exemplo,)</span><span class="sxs-lookup"><span data-stu-id="e6435-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="e6435-130">Domínio de locatário do AAD (por `contoso.onmicrosoft.com`exemplo,)</span><span class="sxs-lookup"><span data-stu-id="e6435-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="e6435-131">Escopo padrão (por exemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="e6435-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e6435-132">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="e6435-132">Register a client app</span></span>

<span data-ttu-id="e6435-133">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo cliente* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="e6435-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e6435-134">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="e6435-134">Select **New registration**.</span></span>
1. <span data-ttu-id="e6435-135">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="e6435-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="e6435-136">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="e6435-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="e6435-137">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="e6435-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="e6435-138">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI `https://localhost:5001/authentication/login-callback`de redirecionamento de.</span><span class="sxs-lookup"><span data-stu-id="e6435-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="e6435-139">Desabilite a caixa de seleção **permissões** > **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="e6435-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e6435-140">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="e6435-140">Select **Register**.</span></span>

<span data-ttu-id="e6435-141">Em **Authentication** > **Platform configurations**configurações > **da**plataforma de autenticação Web:</span><span class="sxs-lookup"><span data-stu-id="e6435-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e6435-142">Confirme se o URI de `https://localhost:5001/authentication/login-callback` **redirecionamento** do está presente.</span><span class="sxs-lookup"><span data-stu-id="e6435-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e6435-143">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="e6435-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e6435-144">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="e6435-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e6435-145">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="e6435-145">Select the **Save** button.</span></span>

<span data-ttu-id="e6435-146">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="e6435-146">In **API permissions**:</span></span>

1. <span data-ttu-id="e6435-147">Confirme se o aplicativo tem a permissão **Microsoft Graph** > **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="e6435-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="e6435-148">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="e6435-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e6435-149">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="e6435-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="e6435-150">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="e6435-150">Open the **API** list.</span></span>
1. <span data-ttu-id="e6435-151">Habilite o acesso à API (por exemplo `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="e6435-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e6435-152">Selecione **Adicionar Permissões**.</span><span class="sxs-lookup"><span data-stu-id="e6435-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e6435-153">Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="e6435-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="e6435-154">Selecione **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="e6435-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e6435-155">Registre a ID do aplicativo de aplicativo *cliente* (ID do cliente) ( `33333333-3333-3333-3333-333333333333`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="e6435-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e6435-156">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6435-156">Create the app</span></span>

<span data-ttu-id="e6435-157">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="e6435-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e6435-158">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="e6435-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e6435-159">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="e6435-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e6435-160">Passe o URI da ID do aplicativo `app-id-uri` para a opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="e6435-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e6435-161">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="e6435-161">Server app configuration</span></span>

<span data-ttu-id="e6435-162">*Esta seção pertence ao aplicativo de **servidor** da solução.*</span><span class="sxs-lookup"><span data-stu-id="e6435-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e6435-163">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="e6435-163">Authentication package</span></span>

<span data-ttu-id="e6435-164">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="e6435-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e6435-165">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="e6435-165">Authentication service support</span></span>

<span data-ttu-id="e6435-166">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="e6435-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e6435-167">O `AddAzureADBearer` método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="e6435-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="e6435-168">`UseAuthentication`e `UseAuthorization` Verifique se:</span><span class="sxs-lookup"><span data-stu-id="e6435-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="e6435-169">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="e6435-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e6435-170">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="e6435-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="e6435-171">Usuário. Identity. Nomes</span><span class="sxs-lookup"><span data-stu-id="e6435-171">User.Identity.Name</span></span>

<span data-ttu-id="e6435-172">Por padrão, a API do aplicativo de servidor `User.Identity.Name` popula com o valor do `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de declaração (por exemplo `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`,).</span><span class="sxs-lookup"><span data-stu-id="e6435-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="e6435-173">Para configurar o aplicativo para receber o valor do tipo `name` de declaração, configure o [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e6435-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="e6435-174">Configurações de aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6435-174">App settings</span></span>

<span data-ttu-id="e6435-175">O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="e6435-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="e6435-176">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="e6435-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="e6435-177">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="e6435-177">WeatherForecast controller</span></span>

<span data-ttu-id="e6435-178">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida `[Authorize]` com o atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="e6435-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="e6435-179">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="e6435-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="e6435-180">O `[Authorize]` atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="e6435-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e6435-181">O `[Authorize]` atributo usado no Blazor aplicativo Webassembly serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="e6435-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="e6435-182">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="e6435-182">Client app configuration</span></span>

<span data-ttu-id="e6435-183">*Esta seção pertence ao aplicativo **cliente** da solução.*</span><span class="sxs-lookup"><span data-stu-id="e6435-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e6435-184">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="e6435-184">Authentication package</span></span>

<span data-ttu-id="e6435-185">Quando um aplicativo é criado para usar contas corporativas ou de`SingleOrg`estudante (), o aplicativo recebe automaticamente uma referência de pacote para a biblioteca`Microsoft.Authentication.WebAssembly.Msal`de autenticação da [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="e6435-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e6435-186">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="e6435-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e6435-187">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e6435-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e6435-188">Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="e6435-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e6435-189">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6435-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e6435-190">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="e6435-190">Authentication service support</span></span>

<span data-ttu-id="e6435-191">O suporte `HttpClient` para instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="e6435-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="e6435-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e6435-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="e6435-193">O suporte para autenticação de usuários é registrado no contêiner de serviço `AddMsalAuthentication` com o método de extensão `Microsoft.Authentication.WebAssembly.Msal` fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="e6435-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e6435-194">Esse método configura todos os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="e6435-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e6435-195">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e6435-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="e6435-196">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6435-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e6435-197">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6435-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="e6435-198">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="e6435-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="e6435-199">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="e6435-199">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="e6435-200">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="e6435-200">Access token scopes</span></span>

<span data-ttu-id="e6435-201">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="e6435-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e6435-202">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="e6435-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e6435-203">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="e6435-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e6435-204">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e6435-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e6435-205">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="e6435-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e6435-206">Se o portal do Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção sem tratamento** quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="e6435-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e6435-207">Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:</span><span class="sxs-lookup"><span data-stu-id="e6435-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e6435-208">Forneça o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="e6435-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e6435-209">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="e6435-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e6435-210">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="e6435-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e6435-211">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="e6435-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="e6435-212">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="e6435-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e6435-213">Página de índice</span><span class="sxs-lookup"><span data-stu-id="e6435-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="e6435-214">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6435-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e6435-215">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e6435-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e6435-216">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e6435-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e6435-217">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="e6435-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e6435-218">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="e6435-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e6435-219">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e6435-219">Run the app</span></span>

<span data-ttu-id="e6435-220">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="e6435-220">Run the app from the Server project.</span></span> <span data-ttu-id="e6435-221">Ao usar o Visual Studio, selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="e6435-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e6435-222">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e6435-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="e6435-223">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="e6435-223">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
