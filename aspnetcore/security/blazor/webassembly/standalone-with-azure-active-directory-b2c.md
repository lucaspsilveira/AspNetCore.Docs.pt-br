---
title: Proteja um Blazor aplicativo autônomo ASP.NET WebAssembly do Core WebAssembly com o Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123439"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="b796a-102">Proteja um Blazor aplicativo autônomo ASP.NET WebAssembly do Core WebAssembly com o Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="b796a-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="b796a-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b796a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="b796a-104">Para criar Blazor um aplicativo autônomo do WebAssembly que usa [o AaD (AAD) Active Directory (AAD)](/azure/active-directory-b2c/overview) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="b796a-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="b796a-105">Siga as orientações nos seguintes tópicos para criar um inquilino e cadastrar um aplicativo web no Portal Azure:</span><span class="sxs-lookup"><span data-stu-id="b796a-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="b796a-106">Criar um &ndash; [inquilino AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="b796a-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="b796a-107">1\.</span><span class="sxs-lookup"><span data-stu-id="b796a-107">1\.</span></span> <span data-ttu-id="b796a-108">Aad b2C exemplo (por exemplo, `https://contoso.b2clogin.com/`que inclui a barra de arrasto)</span><span class="sxs-lookup"><span data-stu-id="b796a-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="b796a-109">2\.</span><span class="sxs-lookup"><span data-stu-id="b796a-109">2\.</span></span> <span data-ttu-id="b796a-110">Domínio do inquilino AAD B2C (por exemplo, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="b796a-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="b796a-111">[Registre um aplicativo](/azure/active-directory-b2c/tutorial-register-applications) &ndash; web Faça as seguintes seleções durante o registro do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b796a-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="b796a-112">1\.</span><span class="sxs-lookup"><span data-stu-id="b796a-112">1\.</span></span> <span data-ttu-id="b796a-113">Defina **o Web App / Web API** como **Sim**.</span><span class="sxs-lookup"><span data-stu-id="b796a-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="b796a-114">2\.</span><span class="sxs-lookup"><span data-stu-id="b796a-114">2\.</span></span> <span data-ttu-id="b796a-115">Definir **Permitir fluxo implícito** para **Sim**.</span><span class="sxs-lookup"><span data-stu-id="b796a-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="b796a-116">3\.</span><span class="sxs-lookup"><span data-stu-id="b796a-116">3\.</span></span> <span data-ttu-id="b796a-117">Adicione uma URL `https://localhost:5001/authentication/login-callback`de **resposta** de .</span><span class="sxs-lookup"><span data-stu-id="b796a-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="b796a-118">Registre o ID do aplicativo (ID `11111111-1111-1111-1111-111111111111`do cliente) (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="b796a-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="b796a-119">[Criar fluxos](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; de usuário Crie um fluxo de usuário de login e login.</span><span class="sxs-lookup"><span data-stu-id="b796a-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="b796a-120">No mínimo, selecione o **atributo** > usuário 'Nome de**exibição'** do aplicativo para preencher o `context.User.Identity.Name` `LoginDisplay` componente *(Compartilhado/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="b796a-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="b796a-121">Registre o nome de fluxo de usuário de inscrição e login `B2C_1_signupsignin`criado para o aplicativo (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="b796a-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="b796a-122">Substitua os espaços reservados no seguinte comando com as informações gravadas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b796a-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="b796a-123">Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="b796a-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b796a-124">O nome da pasta também passa a fazer parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="b796a-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b796a-125">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="b796a-125">Authentication package</span></span>

<span data-ttu-id="b796a-126">Quando um aplicativo é criado para usar uma`IndividualB2C`Conta B2C Individual ( ), o aplicativo recebe automaticamente uma referência de pacote para a [Biblioteca de Autenticação microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="b796a-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="b796a-127">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="b796a-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b796a-128">Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b796a-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="b796a-129">Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.</span><span class="sxs-lookup"><span data-stu-id="b796a-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="b796a-130">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b796a-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b796a-131">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="b796a-131">Authentication service support</span></span>

<span data-ttu-id="b796a-132">O suporte para autenticar usuários é registrado `AddMsalAuthentication` no contêiner de `Microsoft.Authentication.WebAssembly.Msal` serviço com o método de extensão fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="b796a-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="b796a-133">Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="b796a-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b796a-134">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="b796a-134">*Program.cs*:</span></span>

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

<span data-ttu-id="b796a-135">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b796a-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b796a-136">Os valores necessários para a configuração do aplicativo podem ser obtidos na configuração AAD do Portal Azure ao registrar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b796a-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="b796a-137">Acessar escopos de token</span><span class="sxs-lookup"><span data-stu-id="b796a-137">Access token scopes</span></span>

<span data-ttu-id="b796a-138">O Blazor modelo WebAssembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="b796a-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b796a-139">Para provisionar um token como parte do fluxo de login, adicione o `MsalProviderOptions`escopo aos escopos de token de acesso padrão do :</span><span class="sxs-lookup"><span data-stu-id="b796a-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="b796a-140">Se o portal Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção não tratada** quando receber uma resposta não autorizada do *401 Da* API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="b796a-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="b796a-141">Por exemplo, o portal Azure pode fornecer um dos seguintes formatos uri de escopo:</span><span class="sxs-lookup"><span data-stu-id="b796a-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="b796a-142">Forneça o URI de escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="b796a-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="b796a-143">Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="b796a-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="b796a-144">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="b796a-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b796a-145">Página de índice</span><span class="sxs-lookup"><span data-stu-id="b796a-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="b796a-146">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="b796a-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b796a-147">Componente RedirecionarToLogin</span><span class="sxs-lookup"><span data-stu-id="b796a-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b796a-148">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b796a-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b796a-149">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="b796a-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b796a-150">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b796a-150">Additional resources</span></span>

* [<span data-ttu-id="b796a-151">Solicite tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="b796a-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="b796a-152">Tutorial - Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="b796a-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="b796a-153">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="b796a-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
