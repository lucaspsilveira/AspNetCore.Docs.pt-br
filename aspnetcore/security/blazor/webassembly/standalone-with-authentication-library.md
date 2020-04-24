---
title: Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com a biblioteca de autenticação
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 25aa7761b9c1acc72081653422e80cb004500573
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138515"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="a9397-102">Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com a biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="a9397-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="a9397-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a9397-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="a9397-104">*Para Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos do AAD e do AAD B2C no nó Sumário.*</span><span class="sxs-lookup"><span data-stu-id="a9397-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="a9397-105">Para criar um Blazor aplicativo autônomo Webassembly que usa `Microsoft.AspNetCore.Components.WebAssembly.Authentication` a biblioteca, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="a9397-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="a9397-106">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="a9397-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a9397-107">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="a9397-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="a9397-108">No Visual Studio, [crie um Blazor aplicativo Webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="a9397-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="a9397-109">Defina a **autenticação** para **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="a9397-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a9397-110">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="a9397-110">Authentication package</span></span>

<span data-ttu-id="a9397-111">Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote `Microsoft.AspNetCore.Components.WebAssembly.Authentication` para o pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9397-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="a9397-112">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="a9397-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a9397-113">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a9397-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="a9397-114">Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="a9397-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a9397-115">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="a9397-115">Authentication service support</span></span>

<span data-ttu-id="a9397-116">O suporte para autenticação de usuários é registrado no contêiner de serviço `AddOidcAuthentication` com o método de extensão `Microsoft.AspNetCore.Components.WebAssembly.Authentication` fornecido pelo pacote.</span><span class="sxs-lookup"><span data-stu-id="a9397-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="a9397-117">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="a9397-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a9397-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a9397-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="a9397-119">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a9397-119">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="a9397-120">O suporte de autenticação para aplicativos autônomos é oferecido usando o Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="a9397-120">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="a9397-121">O `AddOidcAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando o OIDC.</span><span class="sxs-lookup"><span data-stu-id="a9397-121">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="a9397-122">Os valores necessários para configurar o aplicativo podem ser obtidos do IP em conformidade com o OIDC.</span><span class="sxs-lookup"><span data-stu-id="a9397-122">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="a9397-123">Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.</span><span class="sxs-lookup"><span data-stu-id="a9397-123">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="a9397-124">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="a9397-124">Access token scopes</span></span>

<span data-ttu-id="a9397-125">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="a9397-125">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a9397-126">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token padrão `OidcProviderOptions`do:</span><span class="sxs-lookup"><span data-stu-id="a9397-126">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a9397-127">Se o portal do Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção sem tratamento** quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="a9397-127">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a9397-128">Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:</span><span class="sxs-lookup"><span data-stu-id="a9397-128">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a9397-129">Forneça o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="a9397-129">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a9397-130">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="a9397-130">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a9397-131">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="a9397-131">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a9397-132">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="a9397-132">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="a9397-133">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="a9397-133">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a9397-134">Página de índice</span><span class="sxs-lookup"><span data-stu-id="a9397-134">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="a9397-135">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a9397-135">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a9397-136">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a9397-136">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a9397-137">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a9397-137">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a9397-138">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="a9397-138">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a9397-139">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a9397-139">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
