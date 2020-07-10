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
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="31778-104">Introdução ao Identity no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31778-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31778-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="31778-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="31778-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="31778-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="31778-107">É uma API que dá suporte à funcionalidade de logon da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="31778-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="31778-108">Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.</span><span class="sxs-lookup"><span data-stu-id="31778-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="31778-109">Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="31778-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="31778-110">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="31778-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="31778-111">O [ Identity código-fonte](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponível no github.</span><span class="sxs-lookup"><span data-stu-id="31778-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="31778-112">[Scaffold Identity ](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para revisar a interação do modelo com o Identity .</span><span class="sxs-lookup"><span data-stu-id="31778-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="31778-113">Normalmente, o é configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="31778-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="31778-114">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="31778-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="31778-115">Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="31778-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="31778-116">Observação: os modelos tratam o nome de usuário e o email como o mesmo para os usuários.</span><span class="sxs-lookup"><span data-stu-id="31778-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="31778-117">Para obter instruções mais detalhadas sobre como criar aplicativos que usam Identity o, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="31778-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="31778-118">A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:</span><span class="sxs-lookup"><span data-stu-id="31778-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="31778-119">Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).</span><span class="sxs-lookup"><span data-stu-id="31778-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="31778-120">Não relacionado a ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="31778-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="31778-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="31778-121">Server4.md)]</span></span>

<span data-ttu-id="31778-122">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="31778-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="31778-123">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="31778-123">Create a Web app with authentication</span></span>

<span data-ttu-id="31778-124">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="31778-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31778-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31778-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31778-126">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="31778-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="31778-127">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="31778-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="31778-128">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="31778-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="31778-129">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="31778-129">Click **OK**.</span></span>
* <span data-ttu-id="31778-130">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="31778-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="31778-131">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="31778-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31778-132">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="31778-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="31778-133">O comando anterior cria um Razor aplicativo Web usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="31778-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="31778-134">Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="31778-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="31778-135">O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="31778-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="31778-136">A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="31778-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="31778-137">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="31778-137">For example:</span></span>

* <span data-ttu-id="31778-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="31778-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="31778-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="31778-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="31778-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="31778-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="31778-141">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="31778-141">Apply migrations</span></span>

<span data-ttu-id="31778-142">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="31778-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31778-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31778-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31778-144">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="31778-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="31778-145">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="31778-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31778-146">As migrações não são necessárias nesta etapa ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="31778-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="31778-147">Para o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="31778-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="31778-148">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="31778-148">Test Register and Login</span></span>

<span data-ttu-id="31778-149">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="31778-149">Run the app and register a user.</span></span> <span data-ttu-id="31778-150">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="31778-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="31778-151">Configurar Identity serviços</span><span class="sxs-lookup"><span data-stu-id="31778-151">Configure Identity services</span></span>

<span data-ttu-id="31778-152">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="31778-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="31778-153">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="31778-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="31778-154">O código realçado anterior Identity é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="31778-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="31778-155">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="31778-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="31778-156">é habilitado chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="31778-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="31778-157">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="31778-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="31778-158">O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="31778-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="31778-159">`app.UseAuthorization`está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização.</span><span class="sxs-lookup"><span data-stu-id="31778-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="31778-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.</span><span class="sxs-lookup"><span data-stu-id="31778-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="31778-161">Para obter mais informações sobre o `IdentityOptions` e o `Startup` , consulte <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="31778-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="31778-162">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="31778-162">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31778-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31778-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31778-164">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="31778-164">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="31778-165">Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="31778-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31778-166">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="31778-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31778-167">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="31778-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="31778-168">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="31778-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="31778-169">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="31778-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="31778-170">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="31778-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="31778-171">Para obter mais informações sobre scaffolding Identity , consulte [identidade do Scaffold em um Razor projeto com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="31778-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="31778-172">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="31778-172">Examine Register</span></span>

<span data-ttu-id="31778-173">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="31778-173">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="31778-174">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="31778-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="31778-175">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="31778-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="31778-176">Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="31778-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="31778-177">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="31778-177">Log in</span></span>

<span data-ttu-id="31778-178">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="31778-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="31778-179">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="31778-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="31778-180">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="31778-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="31778-181">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="31778-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="31778-182">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="31778-182">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="31778-183">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="31778-183">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="31778-184">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="31778-184">Log out</span></span>

<span data-ttu-id="31778-185">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="31778-185">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="31778-186">No código anterior, o código `return RedirectToPage();` precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.</span><span class="sxs-lookup"><span data-stu-id="31778-186">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="31778-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="31778-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="31778-188">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="31778-188">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="31778-189">TestarIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-189">Test Identity</span></span>

<span data-ttu-id="31778-190">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="31778-190">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="31778-191">Para testar Identity , adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="31778-191">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="31778-192">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="31778-192">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="31778-193">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="31778-193">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="31778-194">ApresentaIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-194">Explore Identity</span></span>

<span data-ttu-id="31778-195">Para explorar Identity mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="31778-195">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="31778-196">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="31778-196">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="31778-197">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="31778-197">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="31778-198">QC</span><span class="sxs-lookup"><span data-stu-id="31778-198"> Components</span></span>

<span data-ttu-id="31778-199">Todos os Identity pacotes NuGet dependentes são incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="31778-199">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="31778-200">O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="31778-200">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="31778-201">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="31778-201">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="31778-202">Migrando para o ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-202">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="31778-203">Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="31778-203">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="31778-204">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="31778-204">Setting password strength</span></span>

<span data-ttu-id="31778-205">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="31778-205">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="31778-206">ADDDEFAULT Identity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-206">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="31778-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="31778-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="31778-208">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="31778-208">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="31778-209">Consulte a [ Identity fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="31778-209">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="31778-210">Impedir a publicação de Identity ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="31778-210">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="31778-211">Para evitar a publicação de ativos estáticos Identity (folhas de estilo e arquivos JavaScript para Identity a interface do usuário) na raiz da Web, adicione a seguinte `ResolveStaticWebAssetsInputsDependsOn` propriedade e `RemoveIdentityAssets` destino ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="31778-211">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="31778-212">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="31778-212">Next Steps</span></span>

* <span data-ttu-id="31778-213">[ASP.NET Core Identity código-fonte](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="31778-213">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="31778-214">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="31778-214">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="31778-215">[ConfigurarIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="31778-215">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="31778-216">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="31778-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="31778-217">ASP.NET Core Identity é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="31778-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="31778-218">Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="31778-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="31778-219">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="31778-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="31778-220">o pode ser configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="31778-220"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="31778-221">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="31778-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="31778-222">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="31778-222">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="31778-223">Neste tópico, você aprenderá a usar o Identity para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="31778-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="31778-224">Para obter instruções mais detalhadas sobre como criar aplicativos que usam Identity o, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="31778-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="31778-225">ADDDEFAULT Identity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="31778-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="31778-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="31778-227">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="31778-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="31778-228">Consulte a [ Identity fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="31778-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="31778-229">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="31778-229">Create a Web app with authentication</span></span>

<span data-ttu-id="31778-230">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="31778-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31778-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31778-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31778-232">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="31778-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="31778-233">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="31778-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="31778-234">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="31778-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="31778-235">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="31778-235">Click **OK**.</span></span>
* <span data-ttu-id="31778-236">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="31778-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="31778-237">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="31778-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31778-238">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="31778-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="31778-239">O projeto gerado fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="31778-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="31778-240">A Identity Razor biblioteca de classes expõe pontos de extremidade com a `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="31778-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="31778-241">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="31778-241">For example:</span></span>

* <span data-ttu-id="31778-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="31778-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="31778-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="31778-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="31778-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="31778-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="31778-245">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="31778-245">Apply migrations</span></span>

<span data-ttu-id="31778-246">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="31778-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31778-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31778-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31778-248">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="31778-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="31778-249">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="31778-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="31778-250">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="31778-250">Test Register and Login</span></span>

<span data-ttu-id="31778-251">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="31778-251">Run the app and register a user.</span></span> <span data-ttu-id="31778-252">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="31778-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="31778-253">Configurar Identity serviços</span><span class="sxs-lookup"><span data-stu-id="31778-253">Configure Identity services</span></span>

<span data-ttu-id="31778-254">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="31778-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="31778-255">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="31778-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="31778-256">O código anterior Identity é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="31778-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="31778-257">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="31778-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="31778-258">é habilitado chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="31778-258"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="31778-259">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="31778-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="31778-260">Para obter mais informações, consulte a [ Identity classe Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="31778-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="31778-261">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="31778-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="31778-262">Siga a [identidade do Scaffold em um Razor projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="31778-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31778-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31778-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31778-264">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="31778-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31778-265">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="31778-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31778-266">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="31778-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="31778-267">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="31778-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="31778-268">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="31778-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="31778-269">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="31778-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="31778-270">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="31778-270">Examine Register</span></span>

<span data-ttu-id="31778-271">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="31778-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="31778-272">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="31778-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="31778-273">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="31778-273">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="31778-274">**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="31778-274">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="31778-275">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="31778-275">Log in</span></span>

<span data-ttu-id="31778-276">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="31778-276">The Login form is displayed when:</span></span>

* <span data-ttu-id="31778-277">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="31778-277">The **Log in** link is selected.</span></span>
* <span data-ttu-id="31778-278">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="31778-278">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="31778-279">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="31778-279">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="31778-280">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="31778-280">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="31778-281">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="31778-281">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="31778-282">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="31778-282">Log out</span></span>

<span data-ttu-id="31778-283">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="31778-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="31778-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="31778-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="31778-285">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="31778-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="31778-286">TestarIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-286">Test Identity</span></span>

<span data-ttu-id="31778-287">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="31778-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="31778-288">Para testar Identity , adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="31778-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="31778-289">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="31778-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="31778-290">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="31778-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="31778-291">ApresentaIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-291">Explore Identity</span></span>

<span data-ttu-id="31778-292">Para explorar Identity mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="31778-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="31778-293">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="31778-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="31778-294">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="31778-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="31778-295">QC</span><span class="sxs-lookup"><span data-stu-id="31778-295"> Components</span></span>

<span data-ttu-id="31778-296">Todos os Identity pacotes NuGet dependentes estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="31778-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="31778-297">O pacote principal para o Identity é [Microsoft. AspNetCore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="31778-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="31778-298">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="31778-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="31778-299">Migrando para o ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="31778-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="31778-300">Para obter mais informações e diretrizes sobre como migrar seu Identity repositório existente, consulte [ Identity migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="31778-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="31778-301">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="31778-301">Setting password strength</span></span>

<span data-ttu-id="31778-302">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="31778-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="31778-303">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="31778-303">Next Steps</span></span>

* <span data-ttu-id="31778-304">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o Identity uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="31778-304">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="31778-305">[ConfigurarIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="31778-305">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
