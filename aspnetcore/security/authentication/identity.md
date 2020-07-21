---
title: 'Introdução ao :::no-loc(Identity)::: no ASP.NET Core'
author: rick-anderson
description: 'Use :::no-loc(Identity)::: com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).'
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86445419"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>Introdução ao :::no-loc(Identity)::: no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core :::no-loc(Identity)::: :

* É uma API que dá suporte à funcionalidade de logon da interface do usuário.
* Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.

Os usuários podem criar uma conta com as informações de logon armazenadas no :::no-loc(Identity)::: ou podem usar um provedor de logon externo. Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).

O [ :::no-loc(Identity)::: código-fonte](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) está disponível no github. [Scaffold :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para revisar a interação do modelo com o :::no-loc(Identity)::: .

:::no-loc(Identity):::Normalmente, o é configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil. Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.

Neste tópico, você aprenderá a usar o :::no-loc(Identity)::: para registrar, fazer logon e fazer logoff de um usuário. Observação: os modelos tratam o nome de usuário e o email como o mesmo para os usuários. Para obter instruções mais detalhadas sobre como criar aplicativos que usam o :::no-loc(Identity)::: , consulte [próximas etapas](#next).

A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:

* Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).
* Não relacionado a ASP.NET Core :::no-loc(Identity)::: .

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

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

O comando anterior cria um :::no-loc(Razor)::: aplicativo Web usando o SQLite. Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

O projeto gerado fornece [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class). A :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de classes expõe pontos de extremidade com a `:::no-loc(Identity):::` área. Por exemplo:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

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

### <a name="configure-no-locidentity-services"></a>Configurar :::no-loc(Identity)::: serviços

Os serviços são adicionados no `ConfigureServices` . O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

O código realçado anterior :::no-loc(Identity)::: é configurado com valores de opção padrão. Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::é habilitado chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data). `app.UseAuthorization`está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização. `UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.

Para obter mais informações sobre o `:::no-loc(Identity):::Options` e o `Startup` , consulte <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> e [inicialização do aplicativo](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Scaffold registro, logon, LogOut e RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Adicione os `Register` `Login` arquivos,, `LogOut` e `RegisterConfirmation` . Siga a [identidade do Scaffold em um :::no-loc(Razor)::: projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir. Caso contrário, use o namespace correto para o `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.

Para obter mais informações sobre scaffolding :::no-loc(Identity)::: , consulte [identidade do Scaffold em um :::no-loc(Razor)::: projeto com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Examinar registro

Quando um usuário clica no botão **registrar** na `Register` página, a `RegisterModel.OnPostAsync` ação é invocada. O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Fazer logon

O formulário de logon é exibido quando:

* O link **logon** está selecionado.
* Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.

Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada. `PasswordSignInAsync`é chamado no `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .

### <a name="log-out"></a>Faça logoff

O link **fazer logoff** invoca a `LogoutModel.OnPost` ação. 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

No código anterior, o código `return RedirectToPage();` precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.

Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Testar:::no-loc(Identity):::

Os modelos de projeto Web padrão permitem acesso anônimo às home pages. Para testar :::no-loc(Identity)::: , adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** . Você é redirecionado à página de logon.

### <a name="explore-no-locidentity"></a>Apresenta:::no-loc(Identity):::

Para explorar :::no-loc(Identity)::: mais detalhadamente:

* [Criar origem da interface do usuário de identidade completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine a origem de cada página e percorra o depurador.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::QC

Todos os :::no-loc(Identity)::: pacotes NuGet dependentes são incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

O pacote principal para o :::no-loc(Identity)::: é [Microsoft. AspNetCore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/). Esse pacote contém o conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: e é incluído pelo `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Migrando para o ASP.NET Core:::no-loc(Identity):::

Para obter mais informações e diretrizes sobre como migrar seu :::no-loc(Identity)::: repositório existente, consulte [ :::no-loc(Identity)::: migrar autenticação e ](xref:migration/identity).

## <a name="setting-password-strength"></a>Definindo a força da senha

Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>ADDDEFAULT :::no-loc(Identity)::: e Add:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>foi introduzido no ASP.NET Core 2,1. Chamar `AddDefault:::no-loc(Identity):::` é semelhante a chamar o seguinte:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Consulte a [ :::no-loc(Identity)::: fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Impedir a publicação de :::no-loc(Identity)::: ativos estáticos

Para evitar a publicação de ativos estáticos :::no-loc(Identity)::: (folhas de estilo e arquivos JavaScript para :::no-loc(Identity)::: a interface do usuário) na raiz da Web, adicione a seguinte `ResolveStaticWebAssetsInputsDependsOn` propriedade e `Remove:::no-loc(Identity):::Assets` destino ao arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

* [ASP.NET Core :::no-loc(Identity)::: código-fonte](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)
* Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o :::no-loc(Identity)::: uso do SQLite.
* [Configurar:::no-loc(Identity):::](xref:security/authentication/identity-configuration)
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

ASP.NET Core :::no-loc(Identity)::: é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core. Os usuários podem criar uma conta com as informações de logon armazenadas no :::no-loc(Identity)::: ou podem usar um provedor de logon externo. Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).

:::no-loc(Identity):::o pode ser configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil. Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).

Neste tópico, você aprenderá a usar o :::no-loc(Identity)::: para registrar, fazer logon e fazer logoff de um usuário. Para obter instruções mais detalhadas sobre como criar aplicativos que usam :::no-loc(Identity)::: o, consulte a seção próximas etapas no final deste artigo.

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>ADDDEFAULT :::no-loc(Identity)::: e Add:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>foi introduzido no ASP.NET Core 2,1. Chamar `AddDefault:::no-loc(Identity):::` é semelhante a chamar o seguinte:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Consulte a [ :::no-loc(Identity)::: fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.

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

O projeto gerado fornece [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class). A :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de classes expõe pontos de extremidade com a `:::no-loc(Identity):::` área. Por exemplo:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

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

### <a name="configure-no-locidentity-services"></a>Configurar :::no-loc(Identity)::: serviços

Os serviços são adicionados no `ConfigureServices` . O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

O código anterior :::no-loc(Identity)::: é configurado com valores de opção padrão. Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::é habilitado chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Para obter mais informações, consulte a [ :::no-loc(Identity)::: classe Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro em Scaffold, logon e logoff

Siga a [identidade do Scaffold em um :::no-loc(Razor)::: projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.

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

Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada. O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .

**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.

### <a name="log-in"></a>Fazer logon

O formulário de logon é exibido quando:

* O link **logon** está selecionado.
* Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.

Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada. `PasswordSignInAsync`é chamado no `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .

### <a name="log-out"></a>Faça logoff

O link **fazer logoff** invoca a `LogoutModel.OnPost` ação. 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.

Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Testar:::no-loc(Identity):::

Os modelos de projeto Web padrão permitem acesso anônimo às home pages. Para testar :::no-loc(Identity)::: , adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** . Você é redirecionado à página de logon.

### <a name="explore-no-locidentity"></a>Apresenta:::no-loc(Identity):::

Para explorar :::no-loc(Identity)::: mais detalhadamente:

* [Criar origem da interface do usuário de identidade completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine a origem de cada página e percorra o depurador.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::QC

Todos os :::no-loc(Identity)::: pacotes NuGet dependentes estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

O pacote principal para o :::no-loc(Identity)::: é [Microsoft. AspNetCore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/). Esse pacote contém o conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: e é incluído pelo `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Migrando para o ASP.NET Core:::no-loc(Identity):::

Para obter mais informações e diretrizes sobre como migrar seu :::no-loc(Identity)::: repositório existente, consulte [ :::no-loc(Identity)::: migrar autenticação e ](xref:migration/identity).

## <a name="setting-password-strength"></a>Definindo a força da senha

Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.

## <a name="next-steps"></a>Próximas etapas

* Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o :::no-loc(Identity)::: uso do SQLite.
* [Configurar:::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
