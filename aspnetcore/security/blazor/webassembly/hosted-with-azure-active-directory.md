---
title: Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110922"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> As diretrizes neste artigo se aplicam a ASP.NET Core 3,2 Preview 4. Este tópico será atualizado para cobrir a visualização 5 em sexta-feira, 24 de abril.

Este artigo descreve como criar um [ Blazor aplicativo hospedado Webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrar aplicativos em AAD B2C e criar solução

### <a name="create-a-tenant"></a>Criar um locatário

Siga as orientações em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um locatário no AAD.

### <a name="register-a-server-api-app"></a>Registrar um aplicativo de API do servidor

Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo de API do servidor* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:

1. Selecione **Novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor servidor AAD**).
1. Escolha um **tipo de conta com suporte**. Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.
1. O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.
1. Desabilite a caixa de seleção **permissões** > **conceder administrador concento para OpenID e offline_access** .
1. Selecione **Registrar**.

Em **permissões de API**, remova a permissão **Microsoft Graph** > **User. Read** , pois o aplicativo não requer acesso ao perfil de entrada ou UER.

No **expor uma API**:

1. Selecione **Adicionar um escopo**.
1. Selecione **Salvar e continuar**.
1. Forneça um **nome de escopo** (por exemplo `API.Access`,).
1. Forneça um **nome de exibição de consentimento do administrador** ( `Access API`por exemplo,).
1. Forneça uma **Descrição de consentimento do administrador** (por `Allows the app to access server app API endpoints.`exemplo,).
1. Confirme se o **estado** está definido como **habilitado**.
1. Selecione **Adicionar escopo**.

Registre as seguintes informações:

* *Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por `11111111-1111-1111-1111-111111111111`exemplo,)
* URI da ID do aplicativo (por `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`exemplo `api://11111111-1111-1111-1111-111111111111`,, ou o valor personalizado que você forneceu)
* ID do diretório (ID do locatário) (por `222222222-2222-2222-2222-222222222222`exemplo,)
* Domínio de locatário do AAD (por `contoso.onmicrosoft.com`exemplo,)
* Escopo padrão (por exemplo, `API.Access`)

### <a name="register-a-client-app"></a>Registrar um aplicativo cliente

Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo cliente* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:

1. Selecione **Novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor cliente AAD**).
1. Escolha um **tipo de conta com suporte**. Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.
1. Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI `https://localhost:5001/authentication/login-callback`de redirecionamento de.
1. Desabilite a caixa de seleção **permissões** > **conceder administrador concento para OpenID e offline_access** .
1. Selecione **Registrar**.

Em **Authentication** > **Platform configurations**configurações > **da**plataforma de autenticação Web:

1. Confirme se o URI de `https://localhost:5001/authentication/login-callback` **redirecionamento** do está presente.
1. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

Em **permissões de API**:

1. Confirme se o aplicativo tem a permissão **Microsoft Graph** > **User. Read** .
1. Selecione **Adicionar uma permissão** seguida por **minhas APIs**.
1. Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, ** Blazor servidor AAD**).
1. Abra a lista de **APIs** .
1. Habilite o acesso à API (por exemplo `API.Access`,).
1. Selecione **Adicionar Permissões**.
1. Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** . Clique em **Sim** para confirmar.

Registre a ID do aplicativo de aplicativo *cliente* (ID do cliente) ( `33333333-3333-3333-3333-333333333333`por exemplo,).

### <a name="create-the-app"></a>Criar o aplicativo

Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,). O nome da pasta também se torna parte do nome do projeto.

> [!NOTE]
> Passe o URI da ID do aplicativo `app-id-uri` para a opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .

## <a name="server-app-configuration"></a>Configuração de aplicativo do servidor

*Esta seção pertence ao aplicativo de **servidor** da solução.*

### <a name="authentication-package"></a>Pacote de autenticação

O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão. O `AddAzureADBearer` método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`e `UseAuthorization` Verifique se:

* O aplicativo tenta analisar e validar tokens em solicitações de entrada.
* Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Por padrão, a API do aplicativo de servidor `User.Identity.Name` popula com o valor do `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de declaração (por exemplo `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`,).

Para configurar o aplicativo para receber o valor do tipo `name` de declaração, configure o [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices`:

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configurações do aplicativo

O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.

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

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida `[Authorize]` com o atributo aplicado ao controlador. É **importante** entender que:

* O `[Authorize]` atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.
* O `[Authorize]` atributo usado no Blazor aplicativo Webassembly serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.

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

*Esta seção pertence ao aplicativo **cliente** da solução.*

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas corporativas ou de`SingleOrg`estudante (), o aplicativo recebe automaticamente uma referência de pacote para a biblioteca`Microsoft.Authentication.WebAssembly.Msal`de autenticação da [Microsoft](/azure/active-directory/develop/msal-overview) (). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.

O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticação de usuários é registrado no contêiner de serviço `AddMsalAuthentication` com o método de extensão `Microsoft.Authentication.WebAssembly.Msal` fornecido pelo pacote. Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

### <a name="access-token-scopes"></a>Escopos de token de acesso

Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:

* Incluído por padrão na solicitação de entrada.
* Usado para provisionar um token de acesso imediatamente após a autenticação.

Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory. Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:

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

### <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Executar o aplicativo

Execute o aplicativo no projeto do servidor. Ao usar o Visual Studio, selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [Documentação da plataforma de identidade da Microsoft](/azure/active-directory/develop/)
