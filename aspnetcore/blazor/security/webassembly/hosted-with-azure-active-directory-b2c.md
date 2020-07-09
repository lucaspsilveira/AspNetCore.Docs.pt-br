---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory B2C
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: b9125526db9a7484aca50f2ffa6175fd99b11453
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147765"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory B2C

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

Este artigo descreve como criar um Blazor WebAssembly aplicativo autônomo que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrar aplicativos em AAD B2C e criar solução

### <a name="create-a-tenant"></a>Criar um locatário

Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C.

Registre a instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/` , que inclui a barra à direita). A instância é o esquema e o host de um registro de aplicativo B2C do Azure, que pode ser encontrado abrindo a janela **pontos de extremidade** na página **registros de aplicativo** no portal do Azure.

### <a name="register-a-server-api-app"></a>Registrar um aplicativo de API do servidor

Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar um aplicativo do AAD para o *aplicativo de API do servidor* e, em seguida, faça o seguinte:

1. Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor Server AAD B2C**).
1. Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**
1. O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.
1. Confirme se **as permissões**  >  **concedem consentimento de administrador para OpenID e offline_access permissões** estão habilitadas.
1. Selecione **Registrar**.

Registre as seguintes informações:

* *Aplicativo de API do servidor* ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* Domínio principal/Publicador/locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.

No **expor uma API**:

1. Selecione **Adicionar um escopo**.
1. Selecione **Salvar e continuar**.
1. Forneça um **nome de escopo** (por exemplo, `API.Access` ).
1. Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).
1. Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).
1. Confirme se o **estado** está definido como **habilitado**.
1. Selecione **Adicionar escopo**.

Registre as seguintes informações:

* URI da ID do aplicativo (por exemplo,, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` ou o valor personalizado que você forneceu)
* Escopo padrão (por exemplo, `API.Access` )

O URI da ID do aplicativo pode exigir uma configuração especial no aplicativo cliente, que é descrita na seção [escopos de token de acesso](#access-token-scopes) mais adiante neste tópico.

### <a name="register-a-client-app"></a>Registrar um aplicativo cliente

Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *aplicativo cliente* e, em seguida, faça o seguinte:

1. Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor AAD B2C cliente**).
1. Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**
1. Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` . A porta padrão para um aplicativo em execução no Kestrel é 5001. Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo. Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo do servidor no painel de **depuração** . Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento. Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.
1. Confirme se **as permissões**  >  **concedem consentimento de administrador para OpenID e offline_access permissões** estão habilitadas.
1. Selecione **Registrar**.

Registre a ID do aplicativo (cliente) (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:

1. Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.
1. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

Em **permissões de API**:

1. Selecione **Adicionar uma permissão** seguida por **minhas APIs**.
1. Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, ** Blazor Server AAD B2C**).
1. Abra a lista de **APIs** .
1. Habilite o acesso à API (por exemplo, `API.Access` ).
1. Escolha **Adicionar permissões**.
1. Selecione o botão **conceder consentimento de administrador para {nome do locatário}** . Clique em **Sim** para confirmar.

Em **casa**  >  **Azure ad B2C**  >  **fluxos de usuário**:

[Criar um fluxo de usuário de inscrição e de entrada](/azure/active-directory-b2c/tutorial-create-user-flows)

No mínimo, selecione o atributo de usuário nome de exibição de **declarações do aplicativo**  >  **Display Name** para popular o `context.User.Identity.Name` no `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).

Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>Criar o aplicativo

Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Espaço reservado                   | Nome do portal do Azure                                     | Exemplo                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | Instância                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | ID do aplicativo (cliente) para o *aplicativo cliente*          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | Nome do escopo                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | ID do aplicativo (cliente) para o *aplicativo de API do servidor*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | URI da ID do aplicativo ([consulte a observação](#access-token-scopes)) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Fluxo de usuário de inscrição/entrada                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | Domínio primário/Publicador/locatário                       | `contoso.onmicrosoft.com`              |

O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.

> [!NOTE]
> Passe o URI da ID do aplicativo para a `app-id-uri` opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .
>
> Além disso, o escopo configurado pelo modelo hospedado Blazor pode ter o host de URI de ID de aplicativo repetido. Confirme se o escopo configurado para a `DefaultAccessTokenScopes` coleção está correto em `Program.Main` ( `Program.cs` ) do *aplicativo cliente*.

> [!NOTE]
> No portal do Azure, o URI de redirecionamento da Web de configurações da plataforma de autenticação *do aplicativo cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.
>
> Se o *aplicativo cliente* for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades *do aplicativo de API do servidor* no painel de **depuração** .
>
> Se a porta não foi configurada anteriormente com a porta conhecida *do aplicativo cliente* , retorne ao registro *do aplicativo cliente* no portal do Azure e atualize o URI de redirecionamento com a porta correta.

## <a name="server-app-configuration"></a>Configuração de aplicativo do servidor

*Esta seção pertence ao aplicativo da solução **`Server`** .*

### <a name="authentication-package"></a>Pacote de autenticação

O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) pacote:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão. O <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory B2C:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Verifique se:

* O aplicativo tenta analisar e validar tokens em solicitações de entrada.
* Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Usuário. Identity . Nomes

Por padrão, o `User.Identity.Name` não é preenchido.

Para configurar o aplicativo para receber o valor do `name` tipo de declaração, configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configurações do aplicativo

O `appsettings.json` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Exemplo:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado ao controlador. É **importante** entender que:

* O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.
* O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo usado no Blazor WebAssembly aplicativo serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.

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

*Esta seção pertence ao aplicativo da solução **`Client`** .*

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar uma conta individual do B2C ( `IndividualB2C` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote ao aplicativo.

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para <xref:System.Net.Http.HttpClient> instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.ServerAPI` ).

O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote. Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Exemplo:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

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

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :

* [Solicitar tokens de acesso adicionais](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anexar tokens a solicitações de saída](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


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

Execute o aplicativo no projeto do servidor. Ao usar o Visual Studio, seja:

* Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .
* Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Tutorial - Criar um locatário do Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentação da plataforma de identidade da Microsoft](/azure/active-directory/develop/)
