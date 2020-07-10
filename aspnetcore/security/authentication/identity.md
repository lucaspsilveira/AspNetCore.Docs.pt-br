---
title: Introdução ao Identity no ASP.NET Core
author: rick-anderson
description: Use Identity com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 6ac565bfa4862168fa143417ab5a81c51b620f16
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212453"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Introdução ao Identity no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity :

* É uma API que dá suporte à funcionalidade de logon da interface do usuário.
* Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.

Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo. Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).

O [ Identity código-fonte](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponível no github. [Scaffold Identity ](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para revisar a interação do modelo com o Identity .

IdentityNormalmente, o é configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil. Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.

Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário. Observação: os modelos tratam o nome de usuário e o email como o mesmo para os usuários. Para obter instruções mais detalhadas sobre como criar aplicativos que usam Identity o, consulte a seção próximas etapas no final deste artigo.

A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:

* Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).
* Não relacionado a ASP.NET Core Identity .

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Criar um aplicativo Web com autenticação

Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selecione **arquivo** > **novo** > **projeto**.
* Selecione **Aplicativo Web ASP.NET Core**. Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto. Clique em **OK**.
* Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.
* Selecione **contas de usuário individuais** e clique em **OK**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

O comando anterior cria um Razor aplicativo Web usando o SQLite. Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class). A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área. Por exemplo:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Aplicar migrações

Aplique as migrações para inicializar o banco de dados.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Execute o seguinte comando no console do Gerenciador de pacotes (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

As migrações não são necessárias nesta etapa ao usar o SQLite.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Para o LocalDB, execute o seguinte comando:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Teste de registro e logon

Execute o aplicativo e registre um usuário. Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurar Identity serviços

Os serviços são adicionados no `ConfigureServices` . O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

O código realçado anterior Identity é configurado com valores de opção padrão. Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

Identityé habilitado chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data). `app.UseAuthorization`está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização. `UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.

Para obter mais informações sobre o `IdentityOptions` e o `Startup` , consulte <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [inicialização do aplicativo](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro em Scaffold, logon e logoff

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Adicione os arquivos de registro, logon e LogOut. Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir. Caso contrário, use o namespace correto para o `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.

Para obter mais informações sobre scaffolding Identity , consulte [identidade do Scaffold em um Razor projeto com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Examinar registro

Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada. O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .

Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.

### <a name="log-in"></a>Fazer logon

O formulário de logon é exibido quando:

* O link **logon** está selecionado.
* Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.

Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada. `PasswordSignInAsync`é chamado no `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .

### <a name="log-out"></a>Faça logoff

O link **fazer logoff** invoca a `LogoutModel.OnPost` ação. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

No código anterior, o código `return RedirectToPage();` precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.

Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>TestarIdentity

Os modelos de projeto Web padrão permitem acesso anônimo às home pages. Para testar Identity , adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** . Você é redirecionado à página de logon.

### <a name="explore-identity"></a>ApresentaIdentity

Para explorar Identity mais detalhadamente:

* [Criar origem da interface do usuário de identidade completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine a origem de cada página e percorra o depurador.

## <a name="identity-components"></a>IdentityQC

Todos os Identity pacotes NuGet dependentes são incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-identity"></a>Migrando para o ASP.NET CoreIdentity

Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).

## <a name="setting-password-strength"></a>Definindo a força da senha

Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.

## <a name="adddefaultidentity-and-addidentity"></a>ADDDEFAULT Identity e AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1. Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Consulte a [ Identity fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.

## <a name="prevent-publish-of-static-identity-assets"></a>Impedir a publicação de Identity ativos estáticos

Para evitar a publicação de ativos estáticos Identity (folhas de estilo e arquivos JavaScript para Identity a interface do usuário) na raiz da Web, adicione a seguinte `ResolveStaticWebAssetsInputsDependsOn` propriedade e `RemoveIdentityAssets` destino ao arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a>Próximas etapas

* [ASP.NET Core Identity código-fonte](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)
* Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.
* [ConfigurarIdentity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core. Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo. Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).

Identityo pode ser configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil. Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).

Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário. Para obter instruções mais detalhadas sobre como criar aplicativos que usam Identity o, consulte a seção próximas etapas no final deste artigo.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>ADDDEFAULT Identity e AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1. Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Consulte a [ Identity fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.

## <a name="create-a-web-app-with-authentication"></a>Criar um aplicativo Web com autenticação

Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selecione **arquivo** > **novo** > **projeto**.
* Selecione **Aplicativo Web ASP.NET Core**. Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto. Clique em **OK**.
* Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.
* Selecione **contas de usuário individuais** e clique em **OK**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class). A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área. Por exemplo:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Aplicar migrações

Aplique as migrações para inicializar o banco de dados.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Execute o seguinte comando no console do Gerenciador de pacotes (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Teste de registro e logon

Execute o aplicativo e registre um usuário. Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurar Identity serviços

Os serviços são adicionados no `ConfigureServices` . O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

O código anterior Identity é configurado com valores de opção padrão. Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

Identityé habilitado chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Para obter mais informações, consulte a [ Identity classe Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro em Scaffold, logon e logoff

Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Adicione os arquivos de registro, logon e LogOut.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir. Caso contrário, use o namespace correto para o `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.

---

### <a name="examine-register"></a>Examinar registro

Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada. O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .

**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.

### <a name="log-in"></a>Fazer logon

O formulário de logon é exibido quando:

* O link **logon** está selecionado.
* Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.

Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada. `PasswordSignInAsync`é chamado no `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .

### <a name="log-out"></a>Faça logoff

O link **fazer logoff** invoca a `LogoutModel.OnPost` ação. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.

Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>TestarIdentity

Os modelos de projeto Web padrão permitem acesso anônimo às home pages. Para testar Identity , adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** . Você é redirecionado à página de logon.

### <a name="explore-identity"></a>ApresentaIdentity

Para explorar Identity mais detalhadamente:

* [Criar origem da interface do usuário de identidade completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine a origem de cada página e percorra o depurador.

## <a name="identity-components"></a>IdentityQC

Todos os Identity pacotes NuGet dependentes estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-identity"></a>Migrando para o ASP.NET CoreIdentity

Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).

## <a name="setting-password-strength"></a>Definindo a força da senha

Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.

## <a name="next-steps"></a>Próximas etapas

* Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.
* [ConfigurarIdentity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
