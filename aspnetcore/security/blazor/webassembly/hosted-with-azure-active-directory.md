---
title: Proteja um Blazor aplicativo hospedado ASP.NET Core WebAssembly com o Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8fec9f585f42469665cf29069674a199e1626629
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977126"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Proteja um Blazor aplicativo hospedado ASP.NET Core WebAssembly com o Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



Este artigo descreve como criar um [ Blazor aplicativo hospedado no WebAssembly](xref:blazor/hosting-models#blazor-webassembly) que usa [o AaD (AAD) active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registre aplicativos no AAD B2C e crie solução

### <a name="create-a-tenant"></a>Criar um locatário

Siga a orientação em [Quickstart: Configure um inquilino](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um inquilino no AAD.

### <a name="register-a-server-api-app"></a>Registre um aplicativo de API de servidor

Siga a orientação no [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](/azure/active-directory/develop/quickstart-register-app) e tópicos AAD subsequentes do Azure para registrar um aplicativo AAD para o aplicativo *API do servidor* na área de registros do**Aplicativo** de Diretório > Ativo do azure: **Azure Active Directory**

1. Selecione **Novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor Server AAD**).
1. Escolha um **tipo de conta suportada**. Você pode selecionar **Contas neste diretório organizacional apenas** (inquilino único) para esta experiência.
1. O *aplicativo API do servidor* não requer um URI de **redirecionamento** neste cenário, portanto, deixe o set de desada para **Web** e não insira um URI de redirecionamento.
1. Desabilite a caixa de seleção de **permissões** > grant para**openid e offline_access permissões.**
1. Selecione **Registrar**.

Em **permissões de API,** remova a permissão **Microsoft Graph** > **User.Read,** pois o aplicativo não requer acesso a login ou perfil uer.

Em **Expor uma API**:

1. Selecione **Adicionar um escopo**.
1. Selecione **Salvar e continuar**.
1. Forneça um **nome de** `API.Access`escopo (por exemplo, ).
1. Forneça **um nome de exibição de consentimento de admin** (por exemplo, `Access API`).
1. Fornecer **uma descrição de consentimento do Admin** (por exemplo, `Allows the app to access server app API endpoints.`).
1. Confirme se o **Estado** está definido **como Ativado**.
1. Selecione **Adicionar escopo**.

Registre as seguintes informações:

* *Aplicativo de API do servidor* ID do aplicativo (ID do `11111111-1111-1111-1111-111111111111`cliente) (por exemplo, )
* App ID URI (por exemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`ou o valor personalizado que você forneceu)
* ID do diretório (ID do `222222222-2222-2222-2222-222222222222`inquilino) (por exemplo, )
* Domínio do inquilino AAD `contoso.onmicrosoft.com`(por exemplo, )
* Escopo padrão (por `API.Access`exemplo, )

### <a name="register-a-client-app"></a>Registrar um aplicativo cliente

Siga a orientação no [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](/azure/active-directory/develop/quickstart-register-app) e tópicos AAD subsequentes do Azure para registrar um aplicativo AAD para o aplicativo *Cliente* na área de registros do**Aplicativo** de Diretório >  **Ativo do Azure**do portal Azure:

1. Selecione **Novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor Client AAD**).
1. Escolha um **tipo de conta suportada**. Você pode selecionar **Contas neste diretório organizacional apenas** (inquilino único) para esta experiência.
1. Deixe o **URI redirecionar** definido para **web**e forneça `https://localhost:5001/authentication/login-callback`um URI redirecionado de .
1. Desabilite a caixa de seleção de **permissões** > grant para**openid e offline_access permissões.**
1. Selecione **Registrar**.

Em**configurações** > da plataforma **de autenticação** > **Web**:

1. Confirme se o `https://localhost:5001/authentication/login-callback` **URI redirecionamento** está presente.
1. Para **conceder implicitamente,** selecione as caixas de seleção **para tokens de acesso** e **tokens de ID**.
1. Os demais padrões para o aplicativo são aceitáveis para esta experiência.
1. Selecione o botão **Salvar**.

Em **permissões de API:**

1. Confirme se o aplicativo tem permissão **Microsoft Graph** > **User.Read.**
1. Selecione **Adicionar uma permissão** seguida por **Minhas APIs**.
1. Selecione o *aplicativo API* do servidor na coluna **Nome** (por exemplo, ** Blazor Server AAD**).
1. Abra a **lista de API.**
1. Habilite o acesso à `API.Access`API (por exemplo, ).
1. Selecione **Adicionar Permissões**.
1. Selecione o **conteúdo de admin do Grant para o** botão {TENANT NAME}. Clique em **Sim** para confirmar.

Registre o *ID* do aplicativo do aplicativo cliente `33333333-3333-3333-3333-333333333333`(ID do cliente) (por exemplo, ).

### <a name="create-the-app"></a>Criar o aplicativo

Substitua os espaços reservados no seguinte comando com as informações gravadas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ). O nome da pasta também passa a fazer parte do nome do projeto.

> [!NOTE]
> Passe o APP ID `app-id-uri` URI para a opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo do cliente, que está descrito na seção [Escopos de token de acesso.](#access-token-scopes)

## <a name="server-app-configuration"></a>Configuração do aplicativo do servidor

*Esta seção diz respeito ao aplicativo **Server** da solução.*

### <a name="authentication-package"></a>Pacote de autenticação

O suporte para autenticar e autorizar chamadas para ASP.NET ASCS da Web principal é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O `AddAuthentication` método configura serviços de autenticação dentro do aplicativo e configura o manipulador JWT Bearer como o método de autenticação padrão. O `AddAzureADBearer` método define os parâmetros específicos no manipulador JWT Bearer necessários para validar tokens emitidos pelo Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`e `UseAuthorization` garantir que:

* O aplicativo tenta analisar e validar tokens em solicitações recebidas.
* Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falha.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Por padrão, a API `User.Identity.Name` do aplicativo Servidor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` é preenchida com `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`o valor do tipo de solicitação (por exemplo, ).

Para configurar o aplicativo para receber `name` o valor do tipo de solicitação, configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`o [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) da in :

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configurações do aplicativo

O arquivo *appsettings.json* contém as opções para configurar o manipulador portador JWT usado para validar tokens de acesso.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>Controlador weatherforecast

O controlador WeatherForecast *(Controllers/WeatherForecastController.cs)* expõe uma `[Authorize]` API protegida com o atributo aplicado ao controlador. É **importante** entender que:

* O `[Authorize]` atributo neste controlador de API é a única coisa que protege esta API de acesso não autorizado.
* O `[Authorize]` atributo Blazor usado no aplicativo WebAssembly serve apenas como uma dica para o aplicativo de que o usuário deve ser autorizado para que o aplicativo funcione corretamente.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Configuração do aplicativo cliente

*Esta seção diz respeito ao aplicativo **Client** da solução.*

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar`SingleOrg`contas de trabalho ou escola ( ), o`Microsoft.Authentication.WebAssembly.Msal`aplicativo recebe automaticamente uma referência de pacote para a [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.

O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticar usuários é registrado `AddMsalAuthentication` no contêiner de `Microsoft.Authentication.WebAssembly.Msal` serviço com o método de extensão fornecido pelo pacote. Este método configura todos os serviços necessários para que o aplicativo interaja com o Provedor de Identidade (IP).

*Program.cs:*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para a configuração do aplicativo podem ser obtidos na configuração AAD do Portal Azure ao registrar o aplicativo.

### <a name="access-token-scopes"></a>Acessar escopos de token

Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:

* Incluído por padrão no sinal na solicitação.
* Usado para provisionar um token de acesso imediatamente após a autenticação.

Todos os escopos devem pertencer ao mesmo aplicativo pelas regras do Azure Active Directory. Podem ser adicionados escopos adicionais para aplicativos adicionais de API conforme necessário:

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

### <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirecionarToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Executar o aplicativo

Execute o aplicativo do projeto Servidor. Ao usar o Visual Studio, selecione o projeto Servidor no **Solution Explorer** e selecione o botão **Executar** na barra de ferramentas ou iniciar o aplicativo no menu **Debug.**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Solicite tokens de acesso adicionais](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [Documentação da plataforma de identidade da Microsoft](/azure/active-directory/develop/)
