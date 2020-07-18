---
title: Scaffold Identity em projetos de ASP.NET Core
author: rick-anderson
description: Saiba como Scaffold Identity em um projeto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: a8ca520d84d382b95cd4c0e2962ba4e5c922049e
ms.sourcegitcommit: 3544941682869734ea0113e24e02ed0ec9e1a9ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2020
ms.locfileid: "86464560"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Scaffold Identity em projetos de ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class). Os aplicativos que incluem Identity o podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na Identity Razor biblioteca de classes (RCL). Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento. Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro. O código gerado tem precedência sobre o mesmo código no Identity RCL. Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar Identity origem da interface do usuário completa](#full).

Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o Identity pacote RCL. Você tem a opção de selecionar o Identity código a ser gerado.

Embora o scaffolder gere a maior parte do código necessário, você precisa atualizar seu projeto para concluir o processo. Este documento explica as etapas necessárias para concluir uma Identity atualização do scaffolding.

É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações. Inspecione as alterações depois de executar o Identity scaffolder.

Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com o Identity . Serviços ou stubs de serviço não são gerados quando scaffolding Identity . Os serviços para habilitar esses recursos devem ser adicionados manualmente. Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).

Quando scaffolding Identity com um novo contexto de dados em um projeto com contas individuais existentes:

* No `Startup.ConfigureServices` , remova as chamadas para:
  * `AddDbContext`
  * `AddDefaultIdentity`

Por exemplo, `AddDbContext` e `AddDefaultIdentity` são comentados no código a seguir:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

O código anterior comenta o código que está duplicado em *áreas/ Identity / Identity HostingStartup.cs*

Normalmente, os aplicativos que foram criados com contas individuais ***não*** devem criar um novo contexto de dados.

## <a name="scaffold-identity-into-an-empty-project"></a>Scaffold Identity em um projeto vazio

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Atualize a `Startup` classe com um código semelhante ao seguinte:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Scaffold Identity em um Razor projeto sem autorização existente

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrações, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Habilitar autenticação

Atualize a `Startup` classe com um código semelhante ao seguinte:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Alterações de layout

Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo de layout:

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Scaffold Identity em um Razor projeto com autorização

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Algumas Identity opções são configuradas em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Scaffold Identity em um projeto MVC sem autorização existente

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo *views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*

Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Atualize a `Startup` classe com um código semelhante ao seguinte:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Scaffold Identity em um projeto MVC com autorização

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a>Scaffold Identity em um Blazor Server projeto sem autorização existente

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Migrações

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Passar um token XSRF para o aplicativo

Tokens podem ser passados para componentes:

* Quando os tokens de autenticação são provisionados e salvos no cookie de autenticação, eles podem ser passados para componentes.
* Razoros componentes não podem usar `HttpContext` diretamente, portanto, não há como obter um [token de XSRF (falsificação de solicitação)](xref:security/anti-request-forgery) para postar no Identity ponto de extremidade de logout em `/Identity/Account/Logout` . Um token XSRF pode ser passado para componentes.

Para obter mais informações, consulte <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

No arquivo *pages/_Host. cshtml* , estabeleça o token depois de adicioná-lo às `InitialApplicationState` `TokenProvider` classes e:

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Atualize o `App` componente (*app. Razor*) para atribuir `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

O `TokenProvider` serviço demonstrado no tópico é usado no `LoginDisplay` componente na seção [layout e alterações de fluxo de autenticação](#layout-and-authentication-flow-changes) a seguir.

### <a name="enable-authentication"></a>Habilitar autenticação

Na `Startup` classe:

* Confirme se as Razor páginas de serviços são adicionadas no `Startup.ConfigureServices` .
* Se estiver usando o [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre o serviço.
* Chame `UseDatabaseErrorPage` no Application Builder no `Startup.Configure` para o ambiente de desenvolvimento.
* Chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` .
* Adicione um ponto de extremidade para Razor páginas.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Alterações no fluxo de layout e de autenticação

Adicione um `RedirectToLogin` componente (*RedirectToLogin. Razor*) à pasta *compartilhada* do aplicativo na raiz do projeto:

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Adicione um `LoginDisplay` componente (*LoginDisplay. Razor*) à pasta *compartilhada* do aplicativo. O [serviço TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fornece o token XSRF para o formulário HTML que posta para o Identity ponto de extremidade de logout:

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

No `MainLayout` componente (*Shared/MainLayout. Razor*), adicione o `LoginDisplay` componente ao `<div>` conteúdo do elemento de linha superior:

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Pontos de extremidade de autenticação de estilo

Como Blazor Server o usa páginas Razor Identity de páginas, o estilo da interface do usuário é alterado quando um visitante navega entre Identity páginas e componentes. Você tem duas opções para abordar os estilos de incongruous:

#### <a name="build-identity-components"></a>Componentes de compilação Identity

Uma abordagem para usar componentes do Identity em vez de páginas é criar Identity componentes. Como `SignInManager` e `UserManager` não têm suporte em Razor componentes, use pontos de extremidade de API no Blazor Server aplicativo para processar ações de conta de usuário.

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a>Usar um layout personalizado com Blazor estilos de aplicativo

O Identity layout de páginas e os estilos podem ser modificados para produzir páginas que usam o Blazor tema padrão.

> [!NOTE]
> O exemplo nesta seção é meramente um ponto de partida para personalização. Um trabalho adicional é provavelmente necessário para a melhor experiência do usuário.

Crie um novo `NavMenu_IdentityLayout` componente (*Shared/NavMenu_ Identity layout. Razor*). Para a marcação e o código do componente, use o mesmo conteúdo do componente do aplicativo `NavMenu` (*Shared/NavMenu. Razor*). Remova todos os `NavLink` s para componentes que não podem ser acessados anonimamente porque os redirecionamentos automáticos no `RedirectToLogin` componente falham para componentes que exigem autenticação ou autorização.

No arquivo *pages/Shared/layout. cshtml* , faça as seguintes alterações:

* Adicione Razor diretivas à parte superior do arquivo para usar auxiliares de marca e os componentes do aplicativo na pasta *compartilhada* :

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Substituir `{APPLICATION ASSEMBLY}` pelo nome do assembly do aplicativo.

* Adicione uma `<base>` marca e uma Blazor folha `<link>` de estilos ao `<head>` conteúdo:

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Altere o conteúdo da `<body>` marca para o seguinte:

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a>Scaffold Identity em um Blazor Server projeto com autorização

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Algumas Identity opções são configuradas em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Criar Identity origem da interface do usuário completa

Para manter o controle total da Identity interface do usuário, execute o Identity scaffolder e selecione **substituir todos os arquivos**.

O código realçado a seguir mostra as alterações para substituir a Identity interface do usuário padrão por Identity em um aplicativo web ASP.NET Core 2,1. Talvez você queira fazer isso para ter controle total da interface do Identity usuário.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

O padrão Identity é substituído no código a seguir:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre uma `IEmailSender` implementação, por exemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Configuração de senha

Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em Identity páginas com Scaffold. `InputModel``Password`as propriedades são encontradas nos seguintes arquivos:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Desabilitar uma página

Estas seções mostram como desabilitar a página de registro, mas a abordagem pode ser usada para desabilitar qualquer página.

Para desabilitar o registro do usuário:

* Scaffold Identity . Inclua conta. Register, Account. login e Account. RegisterConfirmation. Por exemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Atualizar *áreas/ Identity /pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Atualize *áreas/ Identity /pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente ou remova o link de registro de *áreas/ Identity /pages/Account/Login.cshtml*

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Atualize a página *áreas/ Identity /pages/Account/RegisterConfirmation* .

  * Remova o código e os links do arquivo cshtml.
  * Remova o código de confirmação do `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Usar outro aplicativo para adicionar usuários

Forneça um mecanismo para adicionar usuários fora do aplicativo Web. As opções para adicionar usuários incluem:

* Um aplicativo Web de administrador dedicado.
* Um aplicativo de console.

O código a seguir descreve uma abordagem para adicionar usuários:

* Uma lista de usuários é lida na memória.
* Uma senha exclusiva forte é gerada para cada usuário.
* O usuário é adicionado ao Identity banco de dados.
* O usuário é notificado e foi instruído a alterar a senha.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

O código a seguir descreve como adicionar um usuário:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Uma abordagem semelhante pode ser seguida para cenários de produção.

## <a name="prevent-publish-of-static-identity-assets"></a>Impedir a publicação de Identity ativos estáticos

Para evitar a publicação de ativos estáticos na Identity raiz da Web, consulte <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>Recursos adicionais

* [Alterações no código de autenticação para ASP.NET Core 2,1 e posterior](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 e posterior fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class). Os aplicativos que incluem Identity o podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na Identity Razor biblioteca de classes (RCL). Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento. Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro. O código gerado tem precedência sobre o mesmo código no Identity RCL. Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).

Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o Identity pacote RCL. Você tem a opção de selecionar o Identity código a ser gerado.

Embora o scaffolder gere a maior parte do código necessário, você precisará atualizar seu projeto para concluir o processo. Este documento explica as etapas necessárias para concluir uma Identity atualização do scaffolding.

Quando o Identity scaffolder é executado, um arquivo de *ScaffoldingReadme.txt* é criado no diretório do projeto. O arquivo de *ScaffoldingReadme.txt* contém instruções gerais sobre o que é necessário para concluir a Identity atualização do scaffolding. Este documento contém instruções mais completas do que o arquivo de *ScaffoldingReadme.txt* .

É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações. Inspecione as alterações depois de executar o Identity scaffolder.

> [!NOTE]
> Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com o Identity . Serviços ou stubs de serviço não são gerados quando scaffolding Identity . Os serviços para habilitar esses recursos devem ser adicionados manualmente. Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Scaffold Identity em um projeto vazio

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Adicione as seguintes chamadas realçadas à `Startup` classe:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Scaffold Identity em um Razor projeto sem autorização existente

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrações, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Habilitar autenticação

No `Configure` método da `Startup` classe, chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Alterações de layout

Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo de layout:

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Scaffold Identity em um Razor projeto com autorização

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Algumas Identity opções são configuradas em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Scaffold Identity em um projeto MVC sem autorização existente

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo *views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*

Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*. Para obter mais informações, consulte IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Chamar [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Scaffold Identity em um projeto MVC com autorização

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Exclua as *páginas/pasta compartilhada* e os arquivos nessa pasta.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Criar Identity origem da interface do usuário completa

Para manter o controle total da Identity interface do usuário, execute o Identity scaffolder e selecione **substituir todos os arquivos**.

O código realçado a seguir mostra as alterações para substituir a Identity interface do usuário padrão por Identity em um aplicativo web ASP.NET Core 2,1. Talvez você queira fazer isso para ter controle total da interface do Identity usuário.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

O padrão Identity é substituído no código a seguir:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre uma `IEmailSender` implementação, por exemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Configuração de senha

Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em Identity páginas com Scaffold. `InputModel``Password`as propriedades são encontradas nos seguintes arquivos:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Página desabilitar registro

Para desabilitar o registro do usuário:

* Scaffold Identity . Inclua conta. Register, Account. login e Account. RegisterConfirmation. Por exemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Atualizar *áreas/ Identity /pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Atualize *áreas/ Identity /pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente ou remova o link de registro de *áreas/ Identity /pages/Account/Login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Atualize a página *áreas/ Identity /pages/Account/RegisterConfirmation* .

  * Remova o código e os links do arquivo cshtml.
  * Remova o código de confirmação do `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Usar outro aplicativo para adicionar usuários

Forneça um mecanismo para adicionar usuários fora do aplicativo Web. As opções para adicionar usuários incluem:

* Um aplicativo Web de administrador dedicado.
* Um aplicativo de console.

O código a seguir descreve uma abordagem para adicionar usuários:

* Uma lista de usuários é lida na memória.
* Uma senha exclusiva forte é gerada para cada usuário.
* O usuário é adicionado ao Identity banco de dados.
* O usuário é notificado e foi instruído a alterar a senha.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

O código a seguir descreve como adicionar um usuário:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Uma abordagem semelhante pode ser seguida para cenários de produção.

## <a name="additional-resources"></a>Recursos adicionais

* [Alterações no código de autenticação para ASP.NET Core 2,1 e posterior](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
