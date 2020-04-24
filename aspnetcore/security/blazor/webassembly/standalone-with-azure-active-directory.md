---
title: Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 71229f41f3f1021aa9ad02402af21de51d7eeee4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111195"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Proteger um aplicativo Blazor autônomo webassembly ASP.NET Core com Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> As diretrizes neste artigo se aplicam a ASP.NET Core 3,2 Preview 4. Este tópico será atualizado para cobrir a visualização 5 em sexta-feira, 24 de abril.

Para criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:

[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):

Registre um aplicativo do AAD na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:

1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor cliente AAD**).
1. Escolha um **tipo de conta com suporte**. Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.
1. Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI `https://localhost:5001/authentication/login-callback`de redirecionamento de.
1. Desabilite a caixa de seleção **permissões** > **conceder administrador concento para OpenID e offline_access** .
1. Selecione **Registrar**.

Em **Authentication** > **Platform configurations**configurações > **da**plataforma de autenticação Web:

1. Confirme se o URI de `https://localhost:5001/authentication/login-callback` **redirecionamento** do está presente.
1. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

Registre as seguintes informações:

* ID do aplicativo (ID do cliente) (por `11111111-1111-1111-1111-111111111111`exemplo,)
* ID do diretório (ID do locatário) (por `22222222-2222-2222-2222-222222222222`exemplo,)

Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,). O nome da pasta também se torna parte do nome do projeto.

## <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas corporativas ou de`SingleOrg`estudante (), o aplicativo recebe automaticamente uma referência de pacote para a biblioteca`Microsoft.Authentication.WebAssembly.Msal`de autenticação da [Microsoft](/azure/active-directory/develop/msal-overview) (). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.

O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.

## <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticação de usuários é registrado no contêiner de serviço `AddMsalAuthentication` com o método de extensão `Microsoft.Authentication.WebAssembly.Msal` fornecido pelo pacote. Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

## <a name="access-token-scopes"></a>Escopos de token de acesso

O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura. Para provisionar um token como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão `MsalProviderOptions`do:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Se o portal do Azure fornecer um URI de escopo e **o aplicativo lançar uma exceção sem tratamento** quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host. Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Forneça o URI do escopo sem o esquema e o host:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [Documentação da plataforma de identidade da Microsoft](/azure/active-directory/develop/)
