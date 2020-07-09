---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com a biblioteca de autenticação
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 02960e6c7d70be3ea1be3ed9e2280e5b5847c926
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147670"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="b6c71-102">Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com a biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="b6c71-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="b6c71-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b6c71-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b6c71-104">*Para Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos do AAD e do AAD B2C no nó Sumário.*</span><span class="sxs-lookup"><span data-stu-id="b6c71-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="b6c71-105">Para criar um Blazor WebAssembly aplicativo autônomo que usa [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a biblioteca do, siga as orientações para sua escolha de ferramentas.</span><span class="sxs-lookup"><span data-stu-id="b6c71-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6c71-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6c71-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6c71-107">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="b6c71-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="b6c71-108">Depois de escolher o modelo de \*\* Blazor WebAssembly aplicativo\*\* na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="b6c71-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="b6c71-109">Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="b6c71-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="b6c71-110">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b6c71-110">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="b6c71-111">Crie um novo Blazor WebAssembly projeto com um mecanismo de autenticação em uma pasta vazia.</span><span class="sxs-lookup"><span data-stu-id="b6c71-111">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="b6c71-112">Especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [Identity](xref:security/authentication/identity) sistema ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b6c71-112">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="b6c71-113">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="b6c71-113">Placeholder</span></span>  | <span data-ttu-id="b6c71-114">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6c71-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="b6c71-115">O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6c71-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="b6c71-116">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6c71-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b6c71-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b6c71-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b6c71-118">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="b6c71-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="b6c71-119">Na etapa **configurar seu novo Blazor WebAssembly aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="b6c71-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b6c71-120">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="b6c71-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="b6c71-121">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="b6c71-121">Authentication package</span></span>

<span data-ttu-id="b6c71-122">Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6c71-122">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="b6c71-123">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="b6c71-123">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b6c71-124">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b6c71-124">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="b6c71-125">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="b6c71-125">Authentication service support</span></span>

<span data-ttu-id="b6c71-126">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método de extensão fornecido pelo [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote.</span><span class="sxs-lookup"><span data-stu-id="b6c71-126">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="b6c71-127">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="b6c71-127">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b6c71-128">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b6c71-128">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="b6c71-129">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="b6c71-129">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="b6c71-130">O suporte de autenticação para aplicativos autônomos é oferecido usando o Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="b6c71-130">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="b6c71-131">O <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando o OIDC.</span><span class="sxs-lookup"><span data-stu-id="b6c71-131">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="b6c71-132">Os valores necessários para configurar o aplicativo podem ser obtidos do IP em conformidade com o OIDC.</span><span class="sxs-lookup"><span data-stu-id="b6c71-132">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="b6c71-133">Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.</span><span class="sxs-lookup"><span data-stu-id="b6c71-133">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="b6c71-134">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="b6c71-134">Access token scopes</span></span>

<span data-ttu-id="b6c71-135">O Blazor WebAssembly modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="b6c71-135">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b6c71-136">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token padrão do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="b6c71-136">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b6c71-137">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="b6c71-137">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b6c71-138">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="b6c71-138">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b6c71-139">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="b6c71-139">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b6c71-140">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="b6c71-140">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b6c71-141">Página de índice</span><span class="sxs-lookup"><span data-stu-id="b6c71-141">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="b6c71-142">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="b6c71-142">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b6c71-143">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b6c71-143">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b6c71-144">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b6c71-144">LoginDisplay component</span></span>

<span data-ttu-id="b6c71-145">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="b6c71-145">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="b6c71-146">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="b6c71-146">For authenticated users:</span></span>
  * <span data-ttu-id="b6c71-147">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="b6c71-147">Displays the current username.</span></span>
  * <span data-ttu-id="b6c71-148">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6c71-148">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="b6c71-149">Para usuários anônimos, o oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="b6c71-149">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="b6c71-150">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="b6c71-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b6c71-151">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b6c71-151">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="b6c71-152">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="b6c71-152">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
