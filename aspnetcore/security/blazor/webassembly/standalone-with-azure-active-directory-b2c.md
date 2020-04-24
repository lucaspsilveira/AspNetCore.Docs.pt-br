---
title: Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111156"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="023eb-102">Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="023eb-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="023eb-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="023eb-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="023eb-104">As diretrizes neste artigo se aplicam a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="023eb-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="023eb-105">Este tópico será atualizado para cobrir a visualização 5 em sexta-feira, 24 de abril.</span><span class="sxs-lookup"><span data-stu-id="023eb-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="023eb-106">Para criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="023eb-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="023eb-107">Siga as orientações nos tópicos a seguir para criar um locatário e registrar um aplicativo Web no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="023eb-107">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="023eb-108">[Crie um locatário](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; AAD B2C Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="023eb-108">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="023eb-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="023eb-109">1\.</span></span> <span data-ttu-id="023eb-110">Instância de AAD B2C (por exemplo `https://contoso.b2clogin.com/`,, que inclui a barra à direita)</span><span class="sxs-lookup"><span data-stu-id="023eb-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="023eb-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="023eb-111">2\.</span></span> <span data-ttu-id="023eb-112">AAD B2C domínio de locatário (por exemplo `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="023eb-112">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="023eb-113">[Registrar um aplicativo](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web faça as seguintes seleções durante o registro do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="023eb-113">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="023eb-114">1 \.</span><span class="sxs-lookup"><span data-stu-id="023eb-114">1\.</span></span> <span data-ttu-id="023eb-115">Defina **aplicativo Web/API Web** como **Sim**.</span><span class="sxs-lookup"><span data-stu-id="023eb-115">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="023eb-116">2 \.</span><span class="sxs-lookup"><span data-stu-id="023eb-116">2\.</span></span> <span data-ttu-id="023eb-117">Defina **permitir fluxo implícito** como **Sim**.</span><span class="sxs-lookup"><span data-stu-id="023eb-117">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="023eb-118">3 \.</span><span class="sxs-lookup"><span data-stu-id="023eb-118">3\.</span></span> <span data-ttu-id="023eb-119">Adicione uma **URL** de resposta `https://localhost:5001/authentication/login-callback`de.</span><span class="sxs-lookup"><span data-stu-id="023eb-119">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="023eb-120">Registre a ID do aplicativo (ID do cliente) (por `11111111-1111-1111-1111-111111111111`exemplo,).</span><span class="sxs-lookup"><span data-stu-id="023eb-120">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="023eb-121">[Criar fluxos](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; de usuário criar um fluxo de usuário de inscrição e entrada.</span><span class="sxs-lookup"><span data-stu-id="023eb-121">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="023eb-122">No mínimo, selecione o atributo de usuário**nome de exibição** de **declarações** > do aplicativo `context.User.Identity.Name` para popular `LoginDisplay` o no componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="023eb-122">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="023eb-123">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="023eb-123">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="023eb-124">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="023eb-124">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="023eb-125">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="023eb-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="023eb-126">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="023eb-126">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="023eb-127">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="023eb-127">Authentication package</span></span>

<span data-ttu-id="023eb-128">Quando um aplicativo é criado para usar uma conta individual do B2C`IndividualB2C`(), o aplicativo recebe automaticamente uma referência de pacote para a biblioteca de`Microsoft.Authentication.WebAssembly.Msal`autenticação da [Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="023eb-128">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="023eb-129">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="023eb-129">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="023eb-130">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="023eb-130">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="023eb-131">Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="023eb-131">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="023eb-132">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023eb-132">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="023eb-133">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="023eb-133">Authentication service support</span></span>

<span data-ttu-id="023eb-134">O suporte para autenticação de usuários é registrado no contêiner de serviço `AddMsalAuthentication` com o método de extensão `Microsoft.Authentication.WebAssembly.Msal` fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="023eb-134">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="023eb-135">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="023eb-135">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="023eb-136">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="023eb-136">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="023eb-137">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023eb-137">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="023eb-138">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023eb-138">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="023eb-139">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="023eb-139">Access token scopes</span></span>

<span data-ttu-id="023eb-140">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="023eb-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="023eb-141">Para provisionar um token como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão `MsalProviderOptions`do:</span><span class="sxs-lookup"><span data-stu-id="023eb-141">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="023eb-142">Se o portal do Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção sem tratamento** quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="023eb-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="023eb-143">Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:</span><span class="sxs-lookup"><span data-stu-id="023eb-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="023eb-144">Forneça o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="023eb-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="023eb-145">Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="023eb-145">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="023eb-146">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="023eb-146">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="023eb-147">Página de índice</span><span class="sxs-lookup"><span data-stu-id="023eb-147">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="023eb-148">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="023eb-148">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="023eb-149">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="023eb-149">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="023eb-150">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="023eb-150">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="023eb-151">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="023eb-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="023eb-152">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="023eb-152">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="023eb-153">Tutorial - Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="023eb-153">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="023eb-154">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="023eb-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
