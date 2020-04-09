---
title: Proteja um Blazor aplicativo autônomo do WebAssembly ASP.NET com a biblioteca de autenticação
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977035"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Proteja um Blazor aplicativo autônomo do WebAssembly ASP.NET com a biblioteca de autenticação

Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Para o Azure Active Directory (AAD) e o Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos AAD e AAD B2C nesta tabela de nó de conteúdo.*

Para criar Blazor um aplicativo autônomo `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do WebAssembly que usa biblioteca, execute o seguinte comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ). O nome da pasta também passa a fazer parte do nome do projeto.

No Visual Studio, [crie um Blazor aplicativo WebAssembly](xref:blazor/get-started). Definir **autenticação** **para contas de usuário individuais** com a opção De usuário da Loja no **aplicativo.**

## <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas de usuário individuais, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` o aplicativo recebe automaticamente uma referência de pacote para o pacote no arquivo de projeto do aplicativo. O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.

## <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticar usuários é registrado `AddOidcAuthentication` no contêiner de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` serviço com o método de extensão fornecido pelo pacote. Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).

*Program.cs:*

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

O suporte à autenticação para aplicativos autônomos é oferecido usando o Open ID Connect (OIDC). O `AddOidcAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando OIDC. Os valores necessários para a configuração do aplicativo podem ser obtidos a partir do IP compatível com OIDC. Obtenha os valores ao cadastrar o aplicativo, o que normalmente ocorre em seu portal online.

## <a name="access-token-scopes"></a>Acessar escopos de token

O Blazor modelo WebAssembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura. Para provisionar um token como parte do fluxo de login, adicione o `OidcProviderOptions`escopo aos escopos de token padrão do :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Se o portal Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção não tratada** quando receber uma resposta não autorizada do *401 Da* API, tente usar um URI de escopo que não inclua o esquema e o host. Por exemplo, o portal Azure pode fornecer um dos seguintes formatos uri de escopo:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Forneça o URI de escopo sem o esquema e o host:
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirecionarToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Solicite tokens de acesso adicionais](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
