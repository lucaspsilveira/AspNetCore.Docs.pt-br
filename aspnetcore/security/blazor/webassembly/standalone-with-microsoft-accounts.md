---
title: Proteja um Blazor aplicativo autônomo ASP.NET WebAssembly do Core Com contas Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977074"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="f6c1e-102">Proteja um Blazor aplicativo autônomo ASP.NET WebAssembly do Core Com contas Microsoft</span><span class="sxs-lookup"><span data-stu-id="f6c1e-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="f6c1e-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f6c1e-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="f6c1e-104">Para criar Blazor um aplicativo autônomo do WebAssembly que usa [contas Microsoft com AAD (AAD) active director (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="f6c1e-105">Crie um inquilino AAD e um aplicativo web</span><span class="sxs-lookup"><span data-stu-id="f6c1e-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="f6c1e-106">Registre um aplicativo AAD na área de inscrições do **Azure Active Directory** > **App** do portal Azure:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="f6c1e-107">1\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-107">1\.</span></span> <span data-ttu-id="f6c1e-108">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="f6c1e-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="f6c1e-109">2\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-109">2\.</span></span> <span data-ttu-id="f6c1e-110">Em **tipos de conta suportados,** selecione Contas em qualquer diretório **organizacional**.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="f6c1e-111">3\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-111">3\.</span></span> <span data-ttu-id="f6c1e-112">Deixe o **URI redirecionar** definido para **web**e forneça `https://localhost:5001/authentication/login-callback`um URI redirecionado de .</span><span class="sxs-lookup"><span data-stu-id="f6c1e-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="f6c1e-113">4\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-113">4\.</span></span> <span data-ttu-id="f6c1e-114">Desabilite a caixa de seleção de **permissões** > grant para**openid e offline_access permissões.**</span><span class="sxs-lookup"><span data-stu-id="f6c1e-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="f6c1e-115">5\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-115">5\.</span></span> <span data-ttu-id="f6c1e-116">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-116">Select **Register**.</span></span>

   <span data-ttu-id="f6c1e-117">Em**configurações** > da plataforma **de autenticação** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="f6c1e-118">1\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-118">1\.</span></span> <span data-ttu-id="f6c1e-119">Confirme se o `https://localhost:5001/authentication/login-callback` **URI redirecionamento** está presente.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="f6c1e-120">2\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-120">2\.</span></span> <span data-ttu-id="f6c1e-121">Para **conceder implicitamente,** selecione as caixas de seleção **para tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="f6c1e-122">3\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-122">3\.</span></span> <span data-ttu-id="f6c1e-123">Os demais padrões para o aplicativo são aceitáveis para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="f6c1e-124">4\.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-124">4\.</span></span> <span data-ttu-id="f6c1e-125">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-125">Select the **Save** button.</span></span>

   <span data-ttu-id="f6c1e-126">Registre o ID do aplicativo (ID `11111111-1111-1111-1111-111111111111`do cliente) (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="f6c1e-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="f6c1e-127">Substitua os espaços reservados no seguinte comando com as informações gravadas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="f6c1e-128">Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="f6c1e-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f6c1e-129">O nome da pasta também passa a fazer parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="f6c1e-130">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="f6c1e-131">Faça login no aplicativo usando uma conta microsoft.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="f6c1e-132">Solicite tokens de acesso para APIs da Blazor Microsoft usando a mesma abordagem que para aplicativos autônomos, desde que você tenha configurado o aplicativo corretamente.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="f6c1e-133">Para obter mais informações, consulte [Quickstart: Configure um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="f6c1e-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="f6c1e-134">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="f6c1e-134">Authentication package</span></span>

<span data-ttu-id="f6c1e-135">Quando um aplicativo é criado para usar`SingleOrg`contas de trabalho ou escola ( ), o`Microsoft.Authentication.WebAssembly.Msal`aplicativo recebe automaticamente uma referência de pacote para a [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="f6c1e-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f6c1e-136">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f6c1e-137">Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="f6c1e-138">Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="f6c1e-139">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f6c1e-140">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="f6c1e-140">Authentication service support</span></span>

<span data-ttu-id="f6c1e-141">O suporte para autenticar usuários é registrado `AddMsalAuthentication` no contêiner de `Microsoft.Authentication.WebAssembly.Msal` serviço com o método de extensão fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f6c1e-142">Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="f6c1e-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f6c1e-143">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="f6c1e-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="f6c1e-144">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f6c1e-145">Os valores necessários para a configuração do aplicativo podem ser obtidos na configuração de Contas Microsoft ao registrar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="f6c1e-146">Acessar escopos de token</span><span class="sxs-lookup"><span data-stu-id="f6c1e-146">Access token scopes</span></span>

<span data-ttu-id="f6c1e-147">O Blazor modelo WebAssembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f6c1e-148">Para provisionar um token como parte do fluxo de login, adicione o `MsalProviderOptions`escopo aos escopos de token de acesso padrão do :</span><span class="sxs-lookup"><span data-stu-id="f6c1e-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="f6c1e-149">Se o portal Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção não tratada** quando receber uma resposta não autorizada do *401 Da* API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="f6c1e-150">Por exemplo, o portal Azure pode fornecer um dos seguintes formatos uri de escopo:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="f6c1e-151">Forneça o URI de escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="f6c1e-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="f6c1e-152">Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="f6c1e-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="f6c1e-153">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="f6c1e-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f6c1e-154">Página de índice</span><span class="sxs-lookup"><span data-stu-id="f6c1e-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="f6c1e-155">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f6c1e-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f6c1e-156">Componente RedirecionarToLogin</span><span class="sxs-lookup"><span data-stu-id="f6c1e-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f6c1e-157">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="f6c1e-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="f6c1e-158">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="f6c1e-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f6c1e-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f6c1e-159">Additional resources</span></span>

* [<span data-ttu-id="f6c1e-160">Solicite tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="f6c1e-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f6c1e-161">Quickstart: Registre um aplicativo com a plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f6c1e-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="f6c1e-162">Quickstart: Configure um aplicativo para expor APIs da Web</span><span class="sxs-lookup"><span data-stu-id="f6c1e-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
