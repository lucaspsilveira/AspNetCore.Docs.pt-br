---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com Azure Active Directory B2C
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 3b477b45ae70c6ad66578fbf0ed18589cecbec8d
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147739"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="75c0f-102">Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="75c0f-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="75c0f-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75c0f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="75c0f-104">Para criar um Blazor WebAssembly aplicativo autônomo que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="75c0f-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="75c0f-105">Siga as orientações nos tópicos a seguir para criar um locatário e registrar um aplicativo Web no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="75c0f-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="75c0f-106">Criar um locatário AAD B2C</span><span class="sxs-lookup"><span data-stu-id="75c0f-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="75c0f-107">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="75c0f-107">Record the following information:</span></span>

* <span data-ttu-id="75c0f-108">AAD B2C instância (por exemplo, `https://contoso.b2clogin.com/` , que inclui a barra à direita): a instância é o esquema e o host de um registro de aplicativo do Azure B2C, que pode ser encontrado abrindo a janela **pontos de extremidade** na página **registros de aplicativo** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="75c0f-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="75c0f-109">AAD B2C domínio primário/Publicador/locatário (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="75c0f-109">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="75c0f-110">Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *aplicativo cliente* e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="75c0f-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="75c0f-111">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="75c0f-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="75c0f-112">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor AAD B2C autônomo\*\*).</span><span class="sxs-lookup"><span data-stu-id="75c0f-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="75c0f-113">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="75c0f-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="75c0f-114">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="75c0f-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="75c0f-115">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="75c0f-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="75c0f-116">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75c0f-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="75c0f-117">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="75c0f-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="75c0f-118">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="75c0f-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="75c0f-119">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="75c0f-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="75c0f-120">Confirme se **as permissões**  >  **concedem consentimento de administrador para OpenID e offline_access permissões** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="75c0f-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="75c0f-121">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="75c0f-121">Select **Register**.</span></span>

<span data-ttu-id="75c0f-122">Registre a ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="75c0f-122">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="75c0f-123">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="75c0f-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="75c0f-124">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="75c0f-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="75c0f-125">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="75c0f-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="75c0f-126">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="75c0f-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="75c0f-127">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="75c0f-127">Select the **Save** button.</span></span>

<span data-ttu-id="75c0f-128">Em **casa**  >  **Azure ad B2C**  >  **fluxos de usuário**:</span><span class="sxs-lookup"><span data-stu-id="75c0f-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="75c0f-129">Criar um fluxo de usuário de inscrição e de entrada</span><span class="sxs-lookup"><span data-stu-id="75c0f-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="75c0f-130">No mínimo, selecione o atributo de usuário nome de exibição de **declarações do aplicativo**  >  **Display Name** para popular o `context.User.Identity.Name` no `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="75c0f-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="75c0f-131">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="75c0f-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="75c0f-132">Em uma pasta vazia, substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="75c0f-132">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="75c0f-133">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="75c0f-133">Placeholder</span></span>                   | <span data-ttu-id="75c0f-134">Nome do portal do Azure</span><span class="sxs-lookup"><span data-stu-id="75c0f-134">Azure portal name</span></span>               | <span data-ttu-id="75c0f-135">Exemplo</span><span class="sxs-lookup"><span data-stu-id="75c0f-135">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="75c0f-136">Instância</span><span class="sxs-lookup"><span data-stu-id="75c0f-136">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="75c0f-137">ID do aplicativo (cliente)</span><span class="sxs-lookup"><span data-stu-id="75c0f-137">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="75c0f-138">Fluxo de usuário de inscrição/entrada</span><span class="sxs-lookup"><span data-stu-id="75c0f-138">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="75c0f-139">Domínio primário/Publicador/locatário</span><span class="sxs-lookup"><span data-stu-id="75c0f-139">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="75c0f-140">O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75c0f-140">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="75c0f-141">No portal do Azure, o **Authentication**  >  URI de redirecionamento da Web de**configurações da plataforma**de autenticação do aplicativo  >  **Web**  >  **Redirect URI** é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="75c0f-141">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="75c0f-142">Se o aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="75c0f-142">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="75c0f-143">Se a porta não foi configurada anteriormente com a porta conhecida do aplicativo, retorne ao registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="75c0f-143">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="75c0f-144">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="75c0f-144">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="75c0f-145">Faça logon no aplicativo usando uma conta de usuário do AAD.</span><span class="sxs-lookup"><span data-stu-id="75c0f-145">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="75c0f-146">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="75c0f-146">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="75c0f-147">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="75c0f-147">For more information, see:</span></span>
  * [<span data-ttu-id="75c0f-148">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="75c0f-148">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="75c0f-149">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="75c0f-149">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="75c0f-150">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="75c0f-150">Authentication package</span></span>

<span data-ttu-id="75c0f-151">Quando um aplicativo é criado para usar uma conta individual do B2C ( `IndividualB2C` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="75c0f-151">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="75c0f-152">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="75c0f-152">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="75c0f-153">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="75c0f-153">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="75c0f-154">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75c0f-154">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="75c0f-155">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="75c0f-155">Authentication service support</span></span>

<span data-ttu-id="75c0f-156">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote.</span><span class="sxs-lookup"><span data-stu-id="75c0f-156">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="75c0f-157">Esse método configura todos os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="75c0f-157">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="75c0f-158">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="75c0f-158">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="75c0f-159">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75c0f-159">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="75c0f-160">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75c0f-160">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="75c0f-161">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="75c0f-161">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="75c0f-162">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="75c0f-162">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="75c0f-163">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="75c0f-163">Access token scopes</span></span>

<span data-ttu-id="75c0f-164">O Blazor WebAssembly modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="75c0f-164">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="75c0f-165">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="75c0f-165">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="75c0f-166">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="75c0f-166">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="75c0f-167">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="75c0f-167">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="75c0f-168">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="75c0f-168">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="75c0f-169">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="75c0f-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="75c0f-170">Página de índice</span><span class="sxs-lookup"><span data-stu-id="75c0f-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="75c0f-171">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="75c0f-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="75c0f-172">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="75c0f-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="75c0f-173">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="75c0f-173">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="75c0f-174">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="75c0f-174">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="75c0f-175">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="75c0f-175">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="75c0f-176">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="75c0f-176">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="75c0f-177">Tutorial - Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="75c0f-177">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="75c0f-178">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="75c0f-178">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
