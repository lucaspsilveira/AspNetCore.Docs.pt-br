---
title: Proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com a biblioteca de autenticação
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
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: efd63c2fc533175b034b8311c2b7a6b9e6f4375b
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851091"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="0bc25-102">Proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com a biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="0bc25-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="0bc25-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0bc25-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0bc25-104">*Para Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos do AAD e do AAD B2C no nó Sumário.*</span><span class="sxs-lookup"><span data-stu-id="0bc25-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="0bc25-105">Para criar um Blazor aplicativo Webassembly autônomo que usa a biblioteca [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) , execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="0bc25-105">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="0bc25-106">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="0bc25-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="0bc25-107">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="0bc25-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="0bc25-108">No Visual Studio, [crie um Blazor aplicativo Webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="0bc25-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="0bc25-109">Defina a **autenticação** para **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="0bc25-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="0bc25-110">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="0bc25-110">Authentication package</span></span>

<span data-ttu-id="0bc25-111">Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote para o pacote [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0bc25-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="0bc25-112">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="0bc25-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0bc25-113">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0bc25-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="0bc25-114">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="0bc25-114">Authentication service support</span></span>

<span data-ttu-id="0bc25-115">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método de extensão fornecido pelo pacote [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="0bc25-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="0bc25-116">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="0bc25-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0bc25-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0bc25-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="0bc25-118">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0bc25-118">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="0bc25-119">O suporte de autenticação para aplicativos autônomos é oferecido usando o Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="0bc25-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="0bc25-120">O <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando o OIDC.</span><span class="sxs-lookup"><span data-stu-id="0bc25-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="0bc25-121">Os valores necessários para configurar o aplicativo podem ser obtidos do IP em conformidade com o OIDC.</span><span class="sxs-lookup"><span data-stu-id="0bc25-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="0bc25-122">Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.</span><span class="sxs-lookup"><span data-stu-id="0bc25-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="0bc25-123">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="0bc25-123">Access token scopes</span></span>

<span data-ttu-id="0bc25-124">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="0bc25-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="0bc25-125">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token padrão do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="0bc25-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="0bc25-126">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="0bc25-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="0bc25-127">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="0bc25-127">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="0bc25-128">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="0bc25-128">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="0bc25-129">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="0bc25-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="0bc25-130">Página de índice</span><span class="sxs-lookup"><span data-stu-id="0bc25-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="0bc25-131">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0bc25-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="0bc25-132">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="0bc25-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="0bc25-133">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="0bc25-133">LoginDisplay component</span></span>

<span data-ttu-id="0bc25-134">O `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) é renderizado no `MainLayout` componente (*Shared/MainLayout. Razor*) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="0bc25-134">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="0bc25-135">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="0bc25-135">For authenticated users:</span></span>
  * <span data-ttu-id="0bc25-136">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="0bc25-136">Displays the current username.</span></span>
  * <span data-ttu-id="0bc25-137">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0bc25-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="0bc25-138">Para usuários anônimos, o oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="0bc25-138">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="0bc25-139">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="0bc25-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0bc25-140">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0bc25-140">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="0bc25-141">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="0bc25-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
