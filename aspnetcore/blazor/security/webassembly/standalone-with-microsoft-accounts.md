---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com contas da Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 58dd791718a820e1a326db309afcd173d73c667b
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147703"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="2e13b-102">Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com contas da Microsoft</span><span class="sxs-lookup"><span data-stu-id="2e13b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="2e13b-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2e13b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2e13b-104">Para criar um Blazor WebAssembly aplicativo autônomo que usa [contas da Microsoft com o Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="2e13b-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="2e13b-105">Criar um locatário do AAD e um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="2e13b-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="2e13b-106">Registre um aplicativo do AAD na área **Azure Active Directory**  >  **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="2e13b-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="2e13b-107">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor contas da Microsoft do AAD autônomo\*\*).</span><span class="sxs-lookup"><span data-stu-id="2e13b-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="2e13b-108">Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional**.</span><span class="sxs-lookup"><span data-stu-id="2e13b-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="2e13b-109">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="2e13b-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2e13b-110">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="2e13b-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2e13b-111">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2e13b-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2e13b-112">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="2e13b-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2e13b-113">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="2e13b-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2e13b-114">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="2e13b-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2e13b-115">Desabilite a caixa de seleção **permissões**  >  **conceder consentimento de administrador para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="2e13b-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="2e13b-116">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="2e13b-116">Select **Register**.</span></span>

<span data-ttu-id="2e13b-117">Registre a ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="2e13b-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="2e13b-118">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="2e13b-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="2e13b-119">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="2e13b-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2e13b-120">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="2e13b-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="2e13b-121">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="2e13b-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2e13b-122">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="2e13b-122">Select the **Save** button.</span></span>

<span data-ttu-id="2e13b-123">Criar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2e13b-123">Create the app.</span></span> <span data-ttu-id="2e13b-124">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="2e13b-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="2e13b-125">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="2e13b-125">Placeholder</span></span>   | <span data-ttu-id="2e13b-126">Nome do portal do Azure</span><span class="sxs-lookup"><span data-stu-id="2e13b-126">Azure portal name</span></span>       | <span data-ttu-id="2e13b-127">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2e13b-127">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="2e13b-128">ID do aplicativo (cliente)</span><span class="sxs-lookup"><span data-stu-id="2e13b-128">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="2e13b-129">O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2e13b-129">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="2e13b-130">No portal do Azure, o **Authentication**  >  URI de redirecionamento da Web de**configurações da plataforma**de autenticação do aplicativo  >  **Web**  >  **Redirect URI** é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="2e13b-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="2e13b-131">Se o aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="2e13b-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="2e13b-132">Se a porta não foi configurada anteriormente com a porta conhecida do aplicativo, retorne ao registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="2e13b-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="2e13b-133">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="2e13b-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="2e13b-134">Faça logon no aplicativo usando um conta Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2e13b-134">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="2e13b-135">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2e13b-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="2e13b-136">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="2e13b-136">For more information, see:</span></span>
  * [<span data-ttu-id="2e13b-137">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="2e13b-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="2e13b-138">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="2e13b-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="2e13b-139">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="2e13b-139">Authentication package</span></span>

<span data-ttu-id="2e13b-140">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="2e13b-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="2e13b-141">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="2e13b-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2e13b-142">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2e13b-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="2e13b-143">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2e13b-143">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="2e13b-144">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="2e13b-144">Authentication service support</span></span>

<span data-ttu-id="2e13b-145">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote.</span><span class="sxs-lookup"><span data-stu-id="2e13b-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="2e13b-146">Esse método configura todos os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="2e13b-146">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2e13b-147">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="2e13b-147">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="2e13b-148">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2e13b-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2e13b-149">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2e13b-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="2e13b-150">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="2e13b-150">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="2e13b-151">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="2e13b-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="2e13b-152">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="2e13b-152">Access token scopes</span></span>

<span data-ttu-id="2e13b-153">O Blazor WebAssembly modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="2e13b-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="2e13b-154">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="2e13b-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="2e13b-155">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="2e13b-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2e13b-156">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="2e13b-156">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2e13b-157">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="2e13b-157">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="2e13b-158">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="2e13b-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="2e13b-159">Página de índice</span><span class="sxs-lookup"><span data-stu-id="2e13b-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="2e13b-160">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2e13b-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="2e13b-161">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2e13b-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="2e13b-162">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2e13b-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="2e13b-163">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="2e13b-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2e13b-164">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2e13b-164">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="2e13b-165">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="2e13b-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="2e13b-166">Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="2e13b-166">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="2e13b-167">Início Rápido: Configurar um aplicativo para expor APIs Web</span><span class="sxs-lookup"><span data-stu-id="2e13b-167">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
