---
title: Introdução ao Identity no ASP.NET Core
author: rick-anderson
description: Use Identity com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 6d9532ed8ff89735f41c362b27826436c37c6cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452129"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="b652e-104">Introdução ao Identity no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b652e-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b652e-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b652e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b652e-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="b652e-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="b652e-107">É uma API que dá suporte à funcionalidade de logon da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b652e-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="b652e-108">Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.</span><span class="sxs-lookup"><span data-stu-id="b652e-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="b652e-109">Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="b652e-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="b652e-110">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b652e-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b652e-111">O [ Identity código-fonte](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponível no github.</span><span class="sxs-lookup"><span data-stu-id="b652e-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="b652e-112">[Scaffold Identity ](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para revisar a interação do modelo com o Identity .</span><span class="sxs-lookup"><span data-stu-id="b652e-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="b652e-113">Normalmente, o é configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="b652e-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="b652e-114">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="b652e-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="b652e-115">Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="b652e-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="b652e-116">Observação: os modelos tratam o nome de usuário e o email como o mesmo para os usuários.</span><span class="sxs-lookup"><span data-stu-id="b652e-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="b652e-117">Para obter instruções mais detalhadas sobre como criar aplicativos que usam Identity o, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="b652e-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="b652e-118">A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:</span><span class="sxs-lookup"><span data-stu-id="b652e-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="b652e-119">Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).</span><span class="sxs-lookup"><span data-stu-id="b652e-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="b652e-120">Não relacionado a ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b652e-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="b652e-121">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b652e-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="b652e-122">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="b652e-122">Create a Web app with authentication</span></span>

<span data-ttu-id="b652e-123">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="b652e-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b652e-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b652e-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b652e-125">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="b652e-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="b652e-126">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b652e-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b652e-127">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="b652e-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="b652e-128">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="b652e-128">Click **OK**.</span></span>
* <span data-ttu-id="b652e-129">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="b652e-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="b652e-130">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="b652e-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b652e-131">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b652e-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="b652e-132">O comando anterior cria um Razor aplicativo Web usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="b652e-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="b652e-133">Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b652e-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="b652e-134">O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="b652e-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b652e-135">A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="b652e-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="b652e-136">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b652e-136">For example:</span></span>

* <span data-ttu-id="b652e-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="b652e-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="b652e-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="b652e-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="b652e-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="b652e-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="b652e-140">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="b652e-140">Apply migrations</span></span>

<span data-ttu-id="b652e-141">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b652e-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b652e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b652e-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b652e-143">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="b652e-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="b652e-144">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b652e-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b652e-145">As migrações não são necessárias nesta etapa ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="b652e-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="b652e-146">Para o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b652e-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="b652e-147">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="b652e-147">Test Register and Login</span></span>

<span data-ttu-id="b652e-148">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="b652e-148">Run the app and register a user.</span></span> <span data-ttu-id="b652e-149">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="b652e-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="b652e-150">Configurar Identity serviços</span><span class="sxs-lookup"><span data-stu-id="b652e-150">Configure Identity services</span></span>

<span data-ttu-id="b652e-151">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b652e-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="b652e-152">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="b652e-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="b652e-153">O código realçado anterior Identity é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="b652e-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="b652e-154">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b652e-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="b652e-155">é habilitado chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="b652e-155"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="b652e-156">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="b652e-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="b652e-157">O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="b652e-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="b652e-158">`app.UseAuthorization`está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização.</span><span class="sxs-lookup"><span data-stu-id="b652e-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="b652e-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.</span><span class="sxs-lookup"><span data-stu-id="b652e-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="b652e-160">Para obter mais informações sobre o `IdentityOptions` e o `Startup` , consulte <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="b652e-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="b652e-161">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="b652e-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b652e-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b652e-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b652e-163">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="b652e-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="b652e-164">Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="b652e-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b652e-165">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b652e-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b652e-166">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="b652e-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="b652e-167">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="b652e-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="b652e-168">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="b652e-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="b652e-169">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="b652e-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="b652e-170">Para obter mais informações sobre scaffolding Identity , consulte [identidade do Scaffold em um Razor projeto com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="b652e-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="b652e-171">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="b652e-171">Examine Register</span></span>

<span data-ttu-id="b652e-172">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="b652e-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="b652e-173">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="b652e-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="b652e-174">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="b652e-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="b652e-175">Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="b652e-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="b652e-176">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="b652e-176">Log in</span></span>

<span data-ttu-id="b652e-177">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="b652e-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="b652e-178">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="b652e-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="b652e-179">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="b652e-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="b652e-180">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="b652e-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="b652e-181">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="b652e-181">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="b652e-182">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="b652e-182">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="b652e-183">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="b652e-183">Log out</span></span>

<span data-ttu-id="b652e-184">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="b652e-184">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="b652e-185">No código anterior, o código `return RedirectToPage();` precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.</span><span class="sxs-lookup"><span data-stu-id="b652e-185">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="b652e-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="b652e-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="b652e-187">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b652e-187">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="b652e-188">TestarIdentity</span><span class="sxs-lookup"><span data-stu-id="b652e-188">Test Identity</span></span>

<span data-ttu-id="b652e-189">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="b652e-189">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="b652e-190">Para testar Identity , adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="b652e-190">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="b652e-191">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="b652e-191">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="b652e-192">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="b652e-192">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="b652e-193">ApresentaIdentity</span><span class="sxs-lookup"><span data-stu-id="b652e-193">Explore Identity</span></span>

<span data-ttu-id="b652e-194">Para explorar Identity mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="b652e-194">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="b652e-195">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="b652e-195">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="b652e-196">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="b652e-196">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="b652e-197">QC</span><span class="sxs-lookup"><span data-stu-id="b652e-197"> Components</span></span>

<span data-ttu-id="b652e-198">Todos os Identity pacotes NuGet dependentes são incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="b652e-198">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="b652e-199">O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="b652e-199">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="b652e-200">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="b652e-200">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="b652e-201">Migrando para o ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b652e-201">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="b652e-202">Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="b652e-202">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="b652e-203">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="b652e-203">Setting password strength</span></span>

<span data-ttu-id="b652e-204">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="b652e-204">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="b652e-205">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="b652e-205">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="b652e-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="b652e-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="b652e-207">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b652e-207">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="b652e-208">Consulte [AddDefaultIdentity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b652e-208">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="b652e-209">Impedir a publicação de Identity ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="b652e-209">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="b652e-210">Para evitar a publicação de ativos estáticos Identity (folhas de estilo e arquivos JavaScript para Identity a interface do usuário) na raiz da Web, adicione a seguinte `ResolveStaticWebAssetsInputsDependsOn` propriedade e `RemoveIdentityAssets` destino ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b652e-210">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="b652e-211">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b652e-211">Next Steps</span></span>

* <span data-ttu-id="b652e-212">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="b652e-212">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="b652e-213">[ConfigurarIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="b652e-213">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b652e-214">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b652e-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b652e-215">ASP.NET Core Identity é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b652e-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="b652e-216">Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="b652e-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="b652e-217">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b652e-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="b652e-218">o pode ser configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="b652e-218"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="b652e-219">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="b652e-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="b652e-220">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b652e-220">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b652e-221">Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="b652e-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="b652e-222">Para obter instruções mais detalhadas sobre como criar aplicativos que usam Identity o, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="b652e-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="b652e-223">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="b652e-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="b652e-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="b652e-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="b652e-225">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b652e-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="b652e-226">Consulte [AddDefaultIdentity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b652e-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="b652e-227">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="b652e-227">Create a Web app with authentication</span></span>

<span data-ttu-id="b652e-228">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="b652e-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b652e-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b652e-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b652e-230">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="b652e-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="b652e-231">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b652e-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b652e-232">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="b652e-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="b652e-233">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="b652e-233">Click **OK**.</span></span>
* <span data-ttu-id="b652e-234">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="b652e-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="b652e-235">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="b652e-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b652e-236">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b652e-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="b652e-237">O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="b652e-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b652e-238">A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="b652e-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="b652e-239">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b652e-239">For example:</span></span>

* <span data-ttu-id="b652e-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="b652e-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="b652e-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="b652e-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="b652e-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="b652e-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="b652e-243">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="b652e-243">Apply migrations</span></span>

<span data-ttu-id="b652e-244">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b652e-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b652e-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b652e-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b652e-246">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="b652e-246">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="b652e-247">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b652e-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="b652e-248">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="b652e-248">Test Register and Login</span></span>

<span data-ttu-id="b652e-249">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="b652e-249">Run the app and register a user.</span></span> <span data-ttu-id="b652e-250">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="b652e-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="b652e-251">Configurar Identity serviços</span><span class="sxs-lookup"><span data-stu-id="b652e-251">Configure Identity services</span></span>

<span data-ttu-id="b652e-252">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b652e-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="b652e-253">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="b652e-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="b652e-254">O código anterior Identity é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="b652e-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="b652e-255">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b652e-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="b652e-256">é habilitado chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="b652e-256"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="b652e-257">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="b652e-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="b652e-258">Para obter mais informações, consulte a [classe identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="b652e-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="b652e-259">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="b652e-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="b652e-260">Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="b652e-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b652e-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b652e-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b652e-262">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="b652e-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b652e-263">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b652e-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b652e-264">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="b652e-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="b652e-265">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="b652e-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="b652e-266">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="b652e-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="b652e-267">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="b652e-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="b652e-268">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="b652e-268">Examine Register</span></span>

<span data-ttu-id="b652e-269">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="b652e-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="b652e-270">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="b652e-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="b652e-271">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="b652e-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="b652e-272">**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="b652e-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="b652e-273">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="b652e-273">Log in</span></span>

<span data-ttu-id="b652e-274">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="b652e-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="b652e-275">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="b652e-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="b652e-276">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="b652e-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="b652e-277">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="b652e-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="b652e-278">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="b652e-278">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="b652e-279">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="b652e-279">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="b652e-280">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="b652e-280">Log out</span></span>

<span data-ttu-id="b652e-281">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="b652e-281">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="b652e-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="b652e-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="b652e-283">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b652e-283">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="b652e-284">TestarIdentity</span><span class="sxs-lookup"><span data-stu-id="b652e-284">Test Identity</span></span>

<span data-ttu-id="b652e-285">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="b652e-285">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="b652e-286">Para testar Identity , adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="b652e-286">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="b652e-287">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="b652e-287">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="b652e-288">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="b652e-288">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="b652e-289">ApresentaIdentity</span><span class="sxs-lookup"><span data-stu-id="b652e-289">Explore Identity</span></span>

<span data-ttu-id="b652e-290">Para explorar Identity mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="b652e-290">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="b652e-291">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="b652e-291">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="b652e-292">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="b652e-292">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="b652e-293">QC</span><span class="sxs-lookup"><span data-stu-id="b652e-293"> Components</span></span>

<span data-ttu-id="b652e-294">Todos os Identity pacotes NuGet dependentes estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b652e-294">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b652e-295">O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="b652e-295">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="b652e-296">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="b652e-296">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="b652e-297">Migrando para o ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b652e-297">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="b652e-298">Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="b652e-298">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="b652e-299">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="b652e-299">Setting password strength</span></span>

<span data-ttu-id="b652e-300">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="b652e-300">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b652e-301">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b652e-301">Next Steps</span></span>

* <span data-ttu-id="b652e-302">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="b652e-302">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="b652e-303">[ConfigurarIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="b652e-303">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
