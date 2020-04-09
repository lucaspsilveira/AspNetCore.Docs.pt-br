---
title: Proteja um Blazor aplicativo hospedado ASP.NET WebAssembly do Core com o Identity Server
author: guardrex
description: Para criar Blazor um novo aplicativo hospedado com autenticação dentro do Visual Studio que usa um backend [do IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501276"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Proteja um Blazor aplicativo hospedado ASP.NET WebAssembly do Core com o Identity Server

Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para criar Blazor um novo aplicativo hospedado no Visual Studio que usa [o IdentityServer](https://identityserver.io/) para autenticar usuários e chamadas de API:

1. Use o Visual Studio ** Blazor ** para criar um novo aplicativo WebAssembly. Para obter mais informações, consulte <xref:blazor/get-started>.
1. Na **criação de Blazor uma nova** caixa de diálogo de aplicativo, **selecione Alterar** na seção **Autenticação.**
1. Selecione **Contas de Usuário Individuais** seguidas por **OK**.
1. Selecione a caixa de seleção **hospedada ASP.NET Core** na seção **Avançado.**
1. Selecione o botão **Criar**.

Para criar o aplicativo em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ). O nome da pasta também passa a fazer parte do nome do projeto.

## <a name="server-app-configuration"></a>Configuração do aplicativo do servidor

As seções a seguir descrevem adições ao projeto quando o suporte à autenticação é incluído.

### <a name="startup-class"></a>Classe de inicialização

A `Startup` classe tem as seguintes adições:

* Em `Startup.ConfigureServices`:

  * Identidade com a ui padrão:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer com <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> um método de ajuda adicional que configura algumas convenções padrão ASP.NET Core em cima do IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticação com <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> um método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Em `Startup.Configure`:

  * O middleware de autenticação responsável pela validação das credenciais de solicitação e pela definição do usuário no contexto da solicitação:

    ```csharp
    app.UseAuthentication();
    ```

  * O middleware IdentityServer que expõe os pontos finais do Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>Autorização do AddApi

O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método helper configura [O IdentityServer](https://identityserver.io/) para ASP.NET cenários Core. IdentityServer é uma estrutura poderosa e extensível para lidar com preocupações de segurança de aplicativos. IdentityServer expõe complexidade desnecessária para os cenários mais comuns. Consequentemente, um conjunto de convenções e opções de configuração é fornecido que consideramos um bom ponto de partida. Uma vez que sua autenticação precise ser mudada, o poder total do IdentityServer ainda está disponível para personalizar a autenticação de acordo com as necessidades de um aplicativo.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método helper configura um esquema de diretiva para o aplicativo como o manipulador de autenticação padrão. A diretiva está configurada para permitir que a Identidade manuseie `/Identity`todas as solicitações roteadas para qualquer subcaminho no espaço URL de identidade . O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> cabo todos os outros pedidos. Além disso, este método:

* Registra um `{APPLICATION NAME}API` recurso de API com o `{APPLICATION NAME}API`IdentityServer com um escopo padrão de .
* Configura o JWT Bearer Token Middleware para validar tokens emitidos pelo IdentityServer para o aplicativo.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

No `WeatherForecastController` *(Controllers/WeatherForecastController.cs),* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o atributo é aplicado à classe. O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso. A política de autorização padrão está configurada para usar o <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> esquema de autenticação padrão, que é configurado pelo esquema de diretiva mencionado anteriormente. O método helper <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> configura-se como o manipulador padrão para solicitações ao aplicativo.

### <a name="applicationdbcontext"></a>ApplicationDbContext

No `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> o mesmo é usado em <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Identidade, com a exceção que se estende para incluir o esquema para IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>é derivado <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>de .

Para obter o controle total do esquema do banco <xref:Microsoft.EntityFrameworkCore.DbContext> de dados, herde de uma das classes `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` de `OnModelCreating` Identidade disponíveis e configure o contexto para incluir o esquema de Identidade, chamando o método.

### <a name="oidcconfigurationcontroller"></a>Controlador de configuração oidc

No `OidcConfigurationController` *(Controllers/OidcConfigurationController.cs*), o ponto final do cliente é provisionado para atender aos parâmetros OIDC.

### <a name="app-settings-files"></a>Arquivos de configuração do aplicativo

No arquivo de configurações do aplicativo *(appsettings.json*) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados. No exemplo a seguir, há um único cliente. O nome do cliente corresponde ao nome do aplicativo e `ClientId` é mapeado por convenção para o parâmetro OAuth. O perfil indica que o tipo de aplicativo está sendo configurado. O perfil é usado internamente para conduzir convenções que simplificam o processo de configuração do servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

No arquivo de configurações do aplicativo ambiente Desenvolvimento *(appsettings. Development.json*) Na raiz `IdentityServer` do projeto, a seção descreve a chave usada para assinar tokens. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Configuração do aplicativo cliente

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para`Individual`usar contas de usuário individuais ( `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ), o aplicativo recebe automaticamente uma referência de pacote para o pacote no arquivo de projeto do aplicativo. O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.

### <a name="api-authorization-support"></a>Suporte à autorização da API

O suporte para autenticar usuários é conectado ao contêiner de serviço `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pelo método de extensão fornecido dentro do pacote. Este método configura todos os serviços necessários para que o aplicativo interaja com o sistema de autorização existente.

```csharp
builder.Services.AddApiAuthorization();
```

Por padrão, ele carrega a configuração `_configuration/{client-id}`para o aplicativo por convenção de . Por convenção, o ID do cliente é definido para o nome de montagem do aplicativo. Esta URL pode ser alterada para apontar para um ponto final separado, chamando a sobrecarga com opções.

### <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirecionarToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

O `LoginDisplay` componente (*Compartilhado/LoginDisplay.razor*) é `MainLayout` renderizado no componente *(Compartilhado/MainLayout.razor)* e gerencia os seguintes comportamentos:

* Para usuários autenticados:
  * Exibe o nome de usuário atual.
  * Oferece um link para a página de perfil do usuário em ASP.NET Identidade Central.
  * Oferece um botão para sair do aplicativo.
* Para usuários anônimos:
  * Oferece a opção de se inscrever.
  * Oferece a opção de fazer login.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Executar o aplicativo

Execute o aplicativo do projeto Servidor. Ao usar o Visual Studio, selecione o projeto Servidor no **Solution Explorer** e selecione o botão **Executar** na barra de ferramentas ou iniciar o aplicativo no menu **Debug.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Solicite tokens de acesso adicionais](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
