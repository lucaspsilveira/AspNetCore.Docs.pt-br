---
title: Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com Azure Active Directory
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: adc102edb457059f4d9686a48103483547b667fd
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976851"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="70458-102">Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="70458-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="70458-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70458-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="70458-104">Para criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="70458-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="70458-105">[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="70458-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="70458-106">Registre um aplicativo do AAD na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="70458-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="70458-107">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="70458-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="70458-108">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="70458-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="70458-109">Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="70458-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="70458-110">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI `https://localhost:5001/authentication/login-callback`de redirecionamento de.</span><span class="sxs-lookup"><span data-stu-id="70458-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="70458-111">Desabilite a caixa de seleção **permissões** > **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="70458-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="70458-112">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="70458-112">Select **Register**.</span></span>

<span data-ttu-id="70458-113">Em **Authentication** > **Platform configurations**configurações > **da**plataforma de autenticação Web:</span><span class="sxs-lookup"><span data-stu-id="70458-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="70458-114">Confirme se o URI de `https://localhost:5001/authentication/login-callback` **redirecionamento** do está presente.</span><span class="sxs-lookup"><span data-stu-id="70458-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="70458-115">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="70458-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="70458-116">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="70458-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="70458-117">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="70458-117">Select the **Save** button.</span></span>

<span data-ttu-id="70458-118">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="70458-118">Record the following information:</span></span>

* <span data-ttu-id="70458-119">ID do aplicativo (ID do cliente) (por `11111111-1111-1111-1111-111111111111`exemplo,)</span><span class="sxs-lookup"><span data-stu-id="70458-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="70458-120">ID do diretório (ID do locatário) (por `22222222-2222-2222-2222-222222222222`exemplo,)</span><span class="sxs-lookup"><span data-stu-id="70458-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="70458-121">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="70458-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="70458-122">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="70458-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="70458-123">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="70458-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="70458-124">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="70458-124">Authentication package</span></span>

<span data-ttu-id="70458-125">Quando um aplicativo é criado para usar contas corporativas ou de`SingleOrg`estudante (), o aplicativo recebe automaticamente uma referência de pacote para a biblioteca`Microsoft.Authentication.WebAssembly.Msal`de autenticação da [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="70458-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="70458-126">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="70458-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="70458-127">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="70458-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="70458-128">Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="70458-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="70458-129">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70458-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="70458-130">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="70458-130">Authentication service support</span></span>

<span data-ttu-id="70458-131">O suporte para autenticação de usuários é registrado no contêiner de serviço `AddMsalAuthentication` com o método de extensão `Microsoft.Authentication.WebAssembly.Msal` fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="70458-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="70458-132">Esse método configura todos os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="70458-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="70458-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="70458-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="70458-134">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70458-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="70458-135">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração de contas da Microsoft quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70458-135">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="70458-136">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="70458-136">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="70458-137">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="70458-137">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="70458-138">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="70458-138">Access token scopes</span></span>

<span data-ttu-id="70458-139">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="70458-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="70458-140">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso `MsalProviderOptions`padrão do:</span><span class="sxs-lookup"><span data-stu-id="70458-140">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="70458-141">Se o portal do Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção sem tratamento** quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="70458-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="70458-142">Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:</span><span class="sxs-lookup"><span data-stu-id="70458-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="70458-143">Forneça o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="70458-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="70458-144">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="70458-144">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="70458-145">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="70458-145">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="70458-146">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="70458-146">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="70458-147">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="70458-147">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="70458-148">Página de índice</span><span class="sxs-lookup"><span data-stu-id="70458-148">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="70458-149">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="70458-149">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="70458-150">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="70458-150">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="70458-151">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="70458-151">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="70458-152">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="70458-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="70458-153">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="70458-153">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="70458-154">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="70458-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
