---
title: Proteja um Blazor aplicativo autônomo ASP.NET WebAssembly do Core Com o Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976984"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="87487-102">Proteja um Blazor aplicativo autônomo ASP.NET WebAssembly do Core Com o Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="87487-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="87487-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="87487-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="87487-104">Para criar Blazor um aplicativo autônomo do WebAssembly que usa [o AAD (AAD) active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="87487-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="87487-105">[Crie um inquilino AAD e um aplicativo web:](/azure/active-directory/develop/v2-overview)</span><span class="sxs-lookup"><span data-stu-id="87487-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="87487-106">Registre um aplicativo AAD na área de inscrições do **Azure Active Directory** > **App** do portal Azure:</span><span class="sxs-lookup"><span data-stu-id="87487-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="87487-107">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="87487-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="87487-108">Escolha um **tipo de conta suportada**.</span><span class="sxs-lookup"><span data-stu-id="87487-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="87487-109">Você pode selecionar **Contas neste diretório organizacional apenas** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="87487-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="87487-110">Deixe o **URI redirecionar** definido para **web**e forneça `https://localhost:5001/authentication/login-callback`um URI redirecionado de .</span><span class="sxs-lookup"><span data-stu-id="87487-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="87487-111">Desabilite a caixa de seleção de **permissões** > grant para**openid e offline_access permissões.**</span><span class="sxs-lookup"><span data-stu-id="87487-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="87487-112">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="87487-112">Select **Register**.</span></span>

<span data-ttu-id="87487-113">Em**configurações** > da plataforma **de autenticação** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="87487-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="87487-114">Confirme se o `https://localhost:5001/authentication/login-callback` **URI redirecionamento** está presente.</span><span class="sxs-lookup"><span data-stu-id="87487-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="87487-115">Para **conceder implicitamente,** selecione as caixas de seleção **para tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="87487-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="87487-116">Os demais padrões para o aplicativo são aceitáveis para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="87487-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="87487-117">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="87487-117">Select the **Save** button.</span></span>

<span data-ttu-id="87487-118">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="87487-118">Record the following information:</span></span>

* <span data-ttu-id="87487-119">ID do aplicativo (ID do `11111111-1111-1111-1111-111111111111`cliente) (por exemplo, )</span><span class="sxs-lookup"><span data-stu-id="87487-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="87487-120">ID do diretório (ID do `22222222-2222-2222-2222-222222222222`inquilino) (por exemplo, )</span><span class="sxs-lookup"><span data-stu-id="87487-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="87487-121">Substitua os espaços reservados no seguinte comando com as informações gravadas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="87487-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="87487-122">Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="87487-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="87487-123">O nome da pasta também passa a fazer parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="87487-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="87487-124">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="87487-124">Authentication package</span></span>

<span data-ttu-id="87487-125">Quando um aplicativo é criado para usar`SingleOrg`contas de trabalho ou escola ( ), o`Microsoft.Authentication.WebAssembly.Msal`aplicativo recebe automaticamente uma referência de pacote para a [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="87487-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="87487-126">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="87487-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="87487-127">Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="87487-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="87487-128">Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.</span><span class="sxs-lookup"><span data-stu-id="87487-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="87487-129">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="87487-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="87487-130">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="87487-130">Authentication service support</span></span>

<span data-ttu-id="87487-131">O suporte para autenticar usuários é registrado `AddMsalAuthentication` no contêiner de `Microsoft.Authentication.WebAssembly.Msal` serviço com o método de extensão fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="87487-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="87487-132">Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="87487-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="87487-133">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="87487-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="87487-134">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="87487-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="87487-135">Os valores necessários para a configuração do aplicativo podem ser obtidos na configuração AAD do Portal Azure ao registrar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="87487-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="87487-136">Acessar escopos de token</span><span class="sxs-lookup"><span data-stu-id="87487-136">Access token scopes</span></span>

<span data-ttu-id="87487-137">O Blazor modelo WebAssembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="87487-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="87487-138">Para provisionar um token como parte do fluxo de login, adicione o `MsalProviderOptions`escopo aos escopos de token de acesso padrão do :</span><span class="sxs-lookup"><span data-stu-id="87487-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="87487-139">Se o portal Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção não tratada** quando receber uma resposta não autorizada do *401 Da* API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="87487-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="87487-140">Por exemplo, o portal Azure pode fornecer um dos seguintes formatos uri de escopo:</span><span class="sxs-lookup"><span data-stu-id="87487-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="87487-141">Forneça o URI de escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="87487-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="87487-142">Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="87487-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="87487-143">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="87487-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="87487-144">Página de índice</span><span class="sxs-lookup"><span data-stu-id="87487-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="87487-145">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="87487-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="87487-146">Componente RedirecionarToLogin</span><span class="sxs-lookup"><span data-stu-id="87487-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="87487-147">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="87487-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="87487-148">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="87487-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="87487-149">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="87487-149">Additional resources</span></span>

* [<span data-ttu-id="87487-150">Solicite tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="87487-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="87487-151">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="87487-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
