---
title: Proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: b85129d8b56f2106636b47534630f8139e100ae9
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851205"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="fc5ea-102">Proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="fc5ea-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="fc5ea-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc5ea-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fc5ea-104">Para criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="fc5ea-105">[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="fc5ea-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="fc5ea-106">Registre um aplicativo do AAD na área **Azure Active Directory**  >  **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fc5ea-107">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor AAD autônomo\*\*).</span><span class="sxs-lookup"><span data-stu-id="fc5ea-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="fc5ea-108">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="fc5ea-109">Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="fc5ea-110">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="fc5ea-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="fc5ea-111">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="fc5ea-112">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="fc5ea-113">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="fc5ea-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="fc5ea-114">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="fc5ea-115">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="fc5ea-116">Desabilite a caixa de seleção **permissões**  >  **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="fc5ea-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="fc5ea-117">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-117">Select **Register**.</span></span>

<span data-ttu-id="fc5ea-118">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-118">Record the following information:</span></span>

* <span data-ttu-id="fc5ea-119">ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="fc5ea-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="fc5ea-120">ID do diretório (ID do locatário) (por exemplo, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="fc5ea-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="fc5ea-121">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="fc5ea-122">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="fc5ea-123">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="fc5ea-124">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="fc5ea-125">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-125">Select the **Save** button.</span></span>

<span data-ttu-id="fc5ea-126">Criar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-126">Create the app.</span></span> <span data-ttu-id="fc5ea-127">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="fc5ea-128">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="fc5ea-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fc5ea-129">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="fc5ea-130">No portal do Azure, o **Authentication**  >  URI de redirecionamento da Web de**configurações da plataforma**de autenticação do aplicativo  >  **Web**  >  **Redirect URI** é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="fc5ea-131">Se o aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="fc5ea-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="fc5ea-132">Se a porta não foi configurada anteriormente com a porta conhecida do aplicativo, retorne ao registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="fc5ea-133">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="fc5ea-134">Faça logon no aplicativo usando uma conta de usuário do AAD.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="fc5ea-135">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="fc5ea-136">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-136">For more information, see:</span></span>
  * [<span data-ttu-id="fc5ea-137">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="fc5ea-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="fc5ea-138">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="fc5ea-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fc5ea-139">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="fc5ea-139">Authentication package</span></span>

<span data-ttu-id="fc5ea-140">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. Webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="fc5ea-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="fc5ea-141">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fc5ea-142">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="fc5ea-143">O pacote [Microsoft. Authentication. Webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) transitivamente adiciona o pacote [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-143">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fc5ea-144">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="fc5ea-144">Authentication service support</span></span>

<span data-ttu-id="fc5ea-145">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo pacote [Microsoft. Authentication. Webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="fc5ea-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="fc5ea-146">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="fc5ea-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fc5ea-147">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-147">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="fc5ea-148">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fc5ea-149">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="fc5ea-150">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fc5ea-150">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="fc5ea-151">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="fc5ea-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="fc5ea-152">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="fc5ea-152">Access token scopes</span></span>

<span data-ttu-id="fc5ea-153">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="fc5ea-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fc5ea-154">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="fc5ea-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="fc5ea-155">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="fc5ea-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="fc5ea-156">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="fc5ea-156">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="fc5ea-157">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="fc5ea-157">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="fc5ea-158">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="fc5ea-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="fc5ea-159">Página de índice</span><span class="sxs-lookup"><span data-stu-id="fc5ea-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="fc5ea-160">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="fc5ea-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fc5ea-161">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="fc5ea-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fc5ea-162">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="fc5ea-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fc5ea-163">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="fc5ea-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fc5ea-164">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fc5ea-164">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="fc5ea-165">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="fc5ea-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="fc5ea-166">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="fc5ea-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
