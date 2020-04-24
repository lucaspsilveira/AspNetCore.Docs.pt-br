---
title: Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com contas da Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111182"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="a8950-102">Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com contas da Microsoft</span><span class="sxs-lookup"><span data-stu-id="a8950-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="a8950-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a8950-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="a8950-104">As diretrizes neste artigo se aplicam a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="a8950-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="a8950-105">Este tópico será atualizado para cobrir a visualização 5 em sexta-feira, 24 de abril.</span><span class="sxs-lookup"><span data-stu-id="a8950-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="a8950-106">Para criar um Blazor aplicativo Webassembly autônomo que usa [contas da Microsoft com Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="a8950-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="a8950-107">Criar um locatário do AAD e um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="a8950-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="a8950-108">Registre um aplicativo do AAD na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="a8950-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="a8950-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-109">1\.</span></span> <span data-ttu-id="a8950-110">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="a8950-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="a8950-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-111">2\.</span></span> <span data-ttu-id="a8950-112">Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional**.</span><span class="sxs-lookup"><span data-stu-id="a8950-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="a8950-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-113">3\.</span></span> <span data-ttu-id="a8950-114">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI `https://localhost:5001/authentication/login-callback`de redirecionamento de.</span><span class="sxs-lookup"><span data-stu-id="a8950-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="a8950-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-115">4\.</span></span> <span data-ttu-id="a8950-116">Desabilite a caixa de seleção **permissões** > **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="a8950-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="a8950-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-117">5\.</span></span> <span data-ttu-id="a8950-118">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="a8950-118">Select **Register**.</span></span>

   <span data-ttu-id="a8950-119">Em **Authentication** > **Platform configurations**configurações > **da**plataforma de autenticação Web:</span><span class="sxs-lookup"><span data-stu-id="a8950-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="a8950-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-120">1\.</span></span> <span data-ttu-id="a8950-121">Confirme se o URI de `https://localhost:5001/authentication/login-callback` **redirecionamento** do está presente.</span><span class="sxs-lookup"><span data-stu-id="a8950-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="a8950-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-122">2\.</span></span> <span data-ttu-id="a8950-123">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="a8950-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="a8950-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-124">3\.</span></span> <span data-ttu-id="a8950-125">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="a8950-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="a8950-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="a8950-126">4\.</span></span> <span data-ttu-id="a8950-127">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="a8950-127">Select the **Save** button.</span></span>

   <span data-ttu-id="a8950-128">Registre a ID do aplicativo (ID do cliente) (por `11111111-1111-1111-1111-111111111111`exemplo,).</span><span class="sxs-lookup"><span data-stu-id="a8950-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="a8950-129">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="a8950-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="a8950-130">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="a8950-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a8950-131">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="a8950-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="a8950-132">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="a8950-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="a8950-133">Faça logon no aplicativo usando uma conta da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8950-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="a8950-134">Solicite tokens de acesso para APIs da Microsoft usando a mesma Blazor abordagem que para aplicativos autônomos desde que você tenha configurado o aplicativo corretamente.</span><span class="sxs-lookup"><span data-stu-id="a8950-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="a8950-135">Para obter mais informações, consulte [início rápido: configurar um aplicativo para expor APIs Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="a8950-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a8950-136">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="a8950-136">Authentication package</span></span>

<span data-ttu-id="a8950-137">Quando um aplicativo é criado para usar contas corporativas ou de`SingleOrg`estudante (), o aplicativo recebe automaticamente uma referência de pacote para a biblioteca`Microsoft.Authentication.WebAssembly.Msal`de autenticação da [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="a8950-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a8950-138">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="a8950-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a8950-139">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a8950-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a8950-140">Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="a8950-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a8950-141">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8950-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a8950-142">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="a8950-142">Authentication service support</span></span>

<span data-ttu-id="a8950-143">O suporte para autenticação de usuários é registrado no contêiner de serviço `AddMsalAuthentication` com o método de extensão `Microsoft.Authentication.WebAssembly.Msal` fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="a8950-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a8950-144">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="a8950-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a8950-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8950-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="a8950-146">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8950-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a8950-147">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração de contas da Microsoft quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8950-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="a8950-148">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="a8950-148">Access token scopes</span></span>

<span data-ttu-id="a8950-149">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="a8950-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a8950-150">Para provisionar um token como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão `MsalProviderOptions`do:</span><span class="sxs-lookup"><span data-stu-id="a8950-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a8950-151">Se o portal do Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção sem tratamento** quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="a8950-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a8950-152">Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:</span><span class="sxs-lookup"><span data-stu-id="a8950-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a8950-153">Forneça o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="a8950-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a8950-154">Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="a8950-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="a8950-155">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="a8950-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a8950-156">Página de índice</span><span class="sxs-lookup"><span data-stu-id="a8950-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a8950-157">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a8950-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a8950-158">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a8950-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a8950-159">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a8950-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a8950-160">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="a8950-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a8950-161">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a8950-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="a8950-162">Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="a8950-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="a8950-163">Início rápido: configurar um aplicativo para expor APIs Web</span><span class="sxs-lookup"><span data-stu-id="a8950-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
