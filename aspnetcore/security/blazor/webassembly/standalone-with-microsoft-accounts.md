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
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Proteja um Blazor aplicativo autônomo ASP.NET WebAssembly do Core Com contas Microsoft

Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para criar Blazor um aplicativo autônomo do WebAssembly que usa [contas Microsoft com AAD (AAD) active director (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para autenticação:

1. [Crie um inquilino AAD e um aplicativo web](/azure/active-directory/develop/v2-overview)

   Registre um aplicativo AAD na área de inscrições do **Azure Active Directory** > **App** do portal Azure:

   1\. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor Client AAD**).<br>
   2\. Em **tipos de conta suportados,** selecione Contas em qualquer diretório **organizacional**.<br>
   3\. Deixe o **URI redirecionar** definido para **web**e forneça `https://localhost:5001/authentication/login-callback`um URI redirecionado de .<br>
   4\. Desabilite a caixa de seleção de **permissões** > grant para**openid e offline_access permissões.**<br>
   5\. Selecione **Registrar**.

   Em**configurações** > da plataforma **de autenticação** > **Web**:

   1\. Confirme se o `https://localhost:5001/authentication/login-callback` **URI redirecionamento** está presente.<br>
   2\. Para **conceder implicitamente,** selecione as caixas de seleção **para tokens de acesso** e **tokens de ID**.<br>
   3\. Os demais padrões para o aplicativo são aceitáveis para esta experiência.<br>
   4\. Selecione o botão **Salvar**.

   Registre o ID do aplicativo (ID `11111111-1111-1111-1111-111111111111`do cliente) (por exemplo, ).

1. Substitua os espaços reservados no seguinte comando com as informações gravadas anteriormente e execute o comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ). O nome da pasta também passa a fazer parte do nome do projeto.

Depois de criar o aplicativo, você deve ser capaz de:

* Faça login no aplicativo usando uma conta microsoft.
* Solicite tokens de acesso para APIs da Blazor Microsoft usando a mesma abordagem que para aplicativos autônomos, desde que você tenha configurado o aplicativo corretamente. Para obter mais informações, consulte [Quickstart: Configure um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar`SingleOrg`contas de trabalho ou escola ( ), o`Microsoft.Authentication.WebAssembly.Msal`aplicativo recebe automaticamente uma referência de pacote para a [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.

O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.

## <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticar usuários é registrado `AddMsalAuthentication` no contêiner de `Microsoft.Authentication.WebAssembly.Msal` serviço com o método de extensão fornecido pelo pacote. Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).

*Program.cs:*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para a configuração do aplicativo podem ser obtidos na configuração de Contas Microsoft ao registrar o aplicativo.

## <a name="access-token-scopes"></a>Acessar escopos de token

O Blazor modelo WebAssembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura. Para provisionar um token como parte do fluxo de login, adicione o `MsalProviderOptions`escopo aos escopos de token de acesso padrão do :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Para obter mais informações, consulte <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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
* [Quickstart: Registre um aplicativo com a plataforma de identidade da Microsoft](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Quickstart: Configure um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
