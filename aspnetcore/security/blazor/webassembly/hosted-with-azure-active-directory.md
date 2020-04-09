---
title: Proteja um Blazor aplicativo hospedado ASP.NET Core WebAssembly com o Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8fec9f585f42469665cf29069674a199e1626629
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977126"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="838f0-102">Proteja um Blazor aplicativo hospedado ASP.NET Core WebAssembly com o Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="838f0-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="838f0-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="838f0-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="838f0-104">Este artigo descreve como criar um [ Blazor aplicativo hospedado no WebAssembly](xref:blazor/hosting-models#blazor-webassembly) que usa [o AaD (AAD) active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="838f0-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="838f0-105">Registre aplicativos no AAD B2C e crie solução</span><span class="sxs-lookup"><span data-stu-id="838f0-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="838f0-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="838f0-106">Create a tenant</span></span>

<span data-ttu-id="838f0-107">Siga a orientação em [Quickstart: Configure um inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um inquilino no AAD.</span><span class="sxs-lookup"><span data-stu-id="838f0-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="838f0-108">Registre um aplicativo de API de servidor</span><span class="sxs-lookup"><span data-stu-id="838f0-108">Register a server API app</span></span>

<span data-ttu-id="838f0-109">Siga a orientação no [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](/azure/active-directory/develop/quickstart-register-app) e tópicos AAD subsequentes do Azure para registrar um aplicativo AAD para o aplicativo *API do servidor* na área de registros do**Aplicativo** de Diretório > Ativo do azure: **Azure Active Directory**</span><span class="sxs-lookup"><span data-stu-id="838f0-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="838f0-110">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="838f0-110">Select **New registration**.</span></span>
1. <span data-ttu-id="838f0-111">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="838f0-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="838f0-112">Escolha um **tipo de conta suportada**.</span><span class="sxs-lookup"><span data-stu-id="838f0-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="838f0-113">Você pode selecionar **Contas neste diretório organizacional apenas** (inquilino único) para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="838f0-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="838f0-114">O *aplicativo API do servidor* não requer um URI de **redirecionamento** neste cenário, portanto, deixe o set de desada para **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="838f0-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="838f0-115">Desabilite a caixa de seleção de **permissões** > grant para**openid e offline_access permissões.**</span><span class="sxs-lookup"><span data-stu-id="838f0-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="838f0-116">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="838f0-116">Select **Register**.</span></span>

<span data-ttu-id="838f0-117">Em **permissões de API,** remova a permissão **Microsoft Graph** > **User.Read,** pois o aplicativo não requer acesso a login ou perfil uer.</span><span class="sxs-lookup"><span data-stu-id="838f0-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="838f0-118">Em **Expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="838f0-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="838f0-119">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="838f0-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="838f0-120">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="838f0-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="838f0-121">Forneça um **nome de** `API.Access`escopo (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="838f0-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="838f0-122">Forneça **um nome de exibição de consentimento de admin** (por exemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="838f0-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="838f0-123">Fornecer **uma descrição de consentimento do Admin** (por exemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="838f0-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="838f0-124">Confirme se o **Estado** está definido **como Ativado**.</span><span class="sxs-lookup"><span data-stu-id="838f0-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="838f0-125">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="838f0-125">Select **Add scope**.</span></span>

<span data-ttu-id="838f0-126">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="838f0-126">Record the following information:</span></span>

* <span data-ttu-id="838f0-127">*Aplicativo de API do servidor* ID do aplicativo (ID do `11111111-1111-1111-1111-111111111111`cliente) (por exemplo, )</span><span class="sxs-lookup"><span data-stu-id="838f0-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="838f0-128">App ID URI (por exemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="838f0-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="838f0-129">ID do diretório (ID do `222222222-2222-2222-2222-222222222222`inquilino) (por exemplo, )</span><span class="sxs-lookup"><span data-stu-id="838f0-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="838f0-130">Domínio do inquilino AAD `contoso.onmicrosoft.com`(por exemplo, )</span><span class="sxs-lookup"><span data-stu-id="838f0-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="838f0-131">Escopo padrão (por `API.Access`exemplo, )</span><span class="sxs-lookup"><span data-stu-id="838f0-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="838f0-132">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="838f0-132">Register a client app</span></span>

<span data-ttu-id="838f0-133">Siga a orientação no [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](/azure/active-directory/develop/quickstart-register-app) e tópicos AAD subsequentes do Azure para registrar um aplicativo AAD para o aplicativo *Cliente* na área de registros do**Aplicativo** de Diretório >  **Ativo do Azure**do portal Azure:</span><span class="sxs-lookup"><span data-stu-id="838f0-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="838f0-134">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="838f0-134">Select **New registration**.</span></span>
1. <span data-ttu-id="838f0-135">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="838f0-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="838f0-136">Escolha um **tipo de conta suportada**.</span><span class="sxs-lookup"><span data-stu-id="838f0-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="838f0-137">Você pode selecionar **Contas neste diretório organizacional apenas** (inquilino único) para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="838f0-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="838f0-138">Deixe o **URI redirecionar** definido para **web**e forneça `https://localhost:5001/authentication/login-callback`um URI redirecionado de .</span><span class="sxs-lookup"><span data-stu-id="838f0-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="838f0-139">Desabilite a caixa de seleção de **permissões** > grant para**openid e offline_access permissões.**</span><span class="sxs-lookup"><span data-stu-id="838f0-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="838f0-140">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="838f0-140">Select **Register**.</span></span>

<span data-ttu-id="838f0-141">Em**configurações** > da plataforma **de autenticação** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="838f0-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="838f0-142">Confirme se o `https://localhost:5001/authentication/login-callback` **URI redirecionamento** está presente.</span><span class="sxs-lookup"><span data-stu-id="838f0-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="838f0-143">Para **conceder implicitamente,** selecione as caixas de seleção **para tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="838f0-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="838f0-144">Os demais padrões para o aplicativo são aceitáveis para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="838f0-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="838f0-145">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="838f0-145">Select the **Save** button.</span></span>

<span data-ttu-id="838f0-146">Em **permissões de API:**</span><span class="sxs-lookup"><span data-stu-id="838f0-146">In **API permissions**:</span></span>

1. <span data-ttu-id="838f0-147">Confirme se o aplicativo tem permissão **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="838f0-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="838f0-148">Selecione **Adicionar uma permissão** seguida por **Minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="838f0-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="838f0-149">Selecione o *aplicativo API* do servidor na coluna **Nome** (por exemplo, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="838f0-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="838f0-150">Abra a **lista de API.**</span><span class="sxs-lookup"><span data-stu-id="838f0-150">Open the **API** list.</span></span>
1. <span data-ttu-id="838f0-151">Habilite o acesso à `API.Access`API (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="838f0-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="838f0-152">Selecione **Adicionar Permissões**.</span><span class="sxs-lookup"><span data-stu-id="838f0-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="838f0-153">Selecione o **conteúdo de admin do Grant para o** botão {TENANT NAME}.</span><span class="sxs-lookup"><span data-stu-id="838f0-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="838f0-154">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="838f0-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="838f0-155">Registre o *ID* do aplicativo do aplicativo cliente `33333333-3333-3333-3333-333333333333`(ID do cliente) (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="838f0-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="838f0-156">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="838f0-156">Create the app</span></span>

<span data-ttu-id="838f0-157">Substitua os espaços reservados no seguinte comando com as informações gravadas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="838f0-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="838f0-158">Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="838f0-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="838f0-159">O nome da pasta também passa a fazer parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="838f0-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="838f0-160">Passe o APP ID `app-id-uri` URI para a opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo do cliente, que está descrito na seção [Escopos de token de acesso.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="838f0-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="838f0-161">Configuração do aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="838f0-161">Server app configuration</span></span>

<span data-ttu-id="838f0-162">*Esta seção diz respeito ao aplicativo **Server** da solução.*</span><span class="sxs-lookup"><span data-stu-id="838f0-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="838f0-163">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="838f0-163">Authentication package</span></span>

<span data-ttu-id="838f0-164">O suporte para autenticar e autorizar chamadas para ASP.NET ASCS da Web principal é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="838f0-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="838f0-165">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="838f0-165">Authentication service support</span></span>

<span data-ttu-id="838f0-166">O `AddAuthentication` método configura serviços de autenticação dentro do aplicativo e configura o manipulador JWT Bearer como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="838f0-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="838f0-167">O `AddAzureADBearer` método define os parâmetros específicos no manipulador JWT Bearer necessários para validar tokens emitidos pelo Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="838f0-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="838f0-168">`UseAuthentication`e `UseAuthorization` garantir que:</span><span class="sxs-lookup"><span data-stu-id="838f0-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="838f0-169">O aplicativo tenta analisar e validar tokens em solicitações recebidas.</span><span class="sxs-lookup"><span data-stu-id="838f0-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="838f0-170">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falha.</span><span class="sxs-lookup"><span data-stu-id="838f0-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="838f0-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="838f0-171">User.Identity.Name</span></span>

<span data-ttu-id="838f0-172">Por padrão, a API `User.Identity.Name` do aplicativo Servidor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` é preenchida com `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`o valor do tipo de solicitação (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="838f0-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="838f0-173">Para configurar o aplicativo para receber `name` o valor do tipo de solicitação, configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`o [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) da in :</span><span class="sxs-lookup"><span data-stu-id="838f0-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="838f0-174">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="838f0-174">App settings</span></span>

<span data-ttu-id="838f0-175">O arquivo *appsettings.json* contém as opções para configurar o manipulador portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="838f0-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="838f0-176">Controlador weatherforecast</span><span class="sxs-lookup"><span data-stu-id="838f0-176">WeatherForecast controller</span></span>

<span data-ttu-id="838f0-177">O controlador WeatherForecast *(Controllers/WeatherForecastController.cs)* expõe uma `[Authorize]` API protegida com o atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="838f0-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="838f0-178">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="838f0-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="838f0-179">O `[Authorize]` atributo neste controlador de API é a única coisa que protege esta API de acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="838f0-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="838f0-180">O `[Authorize]` atributo Blazor usado no aplicativo WebAssembly serve apenas como uma dica para o aplicativo de que o usuário deve ser autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="838f0-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="838f0-181">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="838f0-181">Client app configuration</span></span>

<span data-ttu-id="838f0-182">*Esta seção diz respeito ao aplicativo **Client** da solução.*</span><span class="sxs-lookup"><span data-stu-id="838f0-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="838f0-183">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="838f0-183">Authentication package</span></span>

<span data-ttu-id="838f0-184">Quando um aplicativo é criado para usar`SingleOrg`contas de trabalho ou escola ( ), o`Microsoft.Authentication.WebAssembly.Msal`aplicativo recebe automaticamente uma referência de pacote para a [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="838f0-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="838f0-185">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="838f0-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="838f0-186">Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="838f0-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="838f0-187">Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.</span><span class="sxs-lookup"><span data-stu-id="838f0-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="838f0-188">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="838f0-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="838f0-189">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="838f0-189">Authentication service support</span></span>

<span data-ttu-id="838f0-190">O suporte para autenticar usuários é registrado `AddMsalAuthentication` no contêiner de `Microsoft.Authentication.WebAssembly.Msal` serviço com o método de extensão fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="838f0-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="838f0-191">Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="838f0-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="838f0-192">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="838f0-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="838f0-193">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="838f0-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="838f0-194">Os valores necessários para a configuração do aplicativo podem ser obtidos na configuração AAD do Portal Azure ao registrar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="838f0-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="838f0-195">Acessar escopos de token</span><span class="sxs-lookup"><span data-stu-id="838f0-195">Access token scopes</span></span>

<span data-ttu-id="838f0-196">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="838f0-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="838f0-197">Incluído por padrão no sinal na solicitação.</span><span class="sxs-lookup"><span data-stu-id="838f0-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="838f0-198">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="838f0-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="838f0-199">Todos os escopos devem pertencer ao mesmo aplicativo pelas regras do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="838f0-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="838f0-200">Podem ser adicionados escopos adicionais para aplicativos adicionais de API conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="838f0-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="838f0-201">Se o portal Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção não tratada** quando receber uma resposta não autorizada do *401 Da* API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="838f0-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="838f0-202">Por exemplo, o portal Azure pode fornecer um dos seguintes formatos uri de escopo:</span><span class="sxs-lookup"><span data-stu-id="838f0-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="838f0-203">Forneça o URI de escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="838f0-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="838f0-204">Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="838f0-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="838f0-205">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="838f0-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="838f0-206">Página de índice</span><span class="sxs-lookup"><span data-stu-id="838f0-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="838f0-207">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="838f0-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="838f0-208">Componente RedirecionarToLogin</span><span class="sxs-lookup"><span data-stu-id="838f0-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="838f0-209">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="838f0-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="838f0-210">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="838f0-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="838f0-211">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="838f0-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="838f0-212">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="838f0-212">Run the app</span></span>

<span data-ttu-id="838f0-213">Execute o aplicativo do projeto Servidor.</span><span class="sxs-lookup"><span data-stu-id="838f0-213">Run the app from the Server project.</span></span> <span data-ttu-id="838f0-214">Ao usar o Visual Studio, selecione o projeto Servidor no **Solution Explorer** e selecione o botão **Executar** na barra de ferramentas ou iniciar o aplicativo no menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="838f0-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="838f0-215">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="838f0-215">Additional resources</span></span>

* [<span data-ttu-id="838f0-216">Solicite tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="838f0-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="838f0-217">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="838f0-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
