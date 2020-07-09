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
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com a biblioteca de autenticação

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

*Para Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos do AAD e do AAD B2C no nó Sumário.*

Para criar um Blazor WebAssembly aplicativo autônomo que usa [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) a biblioteca do, siga as orientações para sua escolha de ferramentas.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:

1. Depois de escolher o modelo de ** Blazor WebAssembly aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.

1. Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/CLI do .NET Core](#tab/visual-studio-code+netcore-cli)

Crie um novo Blazor WebAssembly projeto com um mecanismo de autenticação em uma pasta vazia. Especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [Identity](xref:security/authentication/identity) sistema ASP.NET Core:

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| Espaço reservado  | Exemplo        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.

Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:

1. Na etapa **configurar seu novo Blazor WebAssembly aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .

1. O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [Identity](xref:security/authentication/identity) .

---

## <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote no arquivo de projeto do aplicativo. O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método de extensão fornecido pelo [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote. Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

O suporte de autenticação para aplicativos autônomos é oferecido usando o Open ID Connect (OIDC). O <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando o OIDC. Os valores necessários para configurar o aplicativo podem ser obtidos do IP em conformidade com o OIDC. Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.

## <a name="access-token-scopes"></a>Escopos de token de acesso

O Blazor WebAssembly modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura. Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token padrão do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :

* [Solicitar tokens de acesso adicionais](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anexar tokens a solicitações de saída](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:

* Para usuários autenticados:
  * Exibe o nome de usuário atual.
  * Oferece um botão para fazer logoff do aplicativo.
* Para usuários anônimos, o oferece a opção de fazer logon.

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

## <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
