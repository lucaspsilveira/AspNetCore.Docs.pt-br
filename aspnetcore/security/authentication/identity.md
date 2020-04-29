---
title: Introdução à identidade no ASP.NET Core
author: rick-anderson
description: Use a identidade com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205937"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="e33d2-104">Introdução à identidade no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e33d2-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e33d2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e33d2-106">ASP.NET Core identidade:</span><span class="sxs-lookup"><span data-stu-id="e33d2-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="e33d2-107">É uma API que dá suporte à funcionalidade de logon da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="e33d2-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="e33d2-108">Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.</span><span class="sxs-lookup"><span data-stu-id="e33d2-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="e33d2-109">Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="e33d2-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="e33d2-110">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e33d2-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="e33d2-111">O [código-fonte da identidade](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponível no github.</span><span class="sxs-lookup"><span data-stu-id="e33d2-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="e33d2-112">[Scaffold identidade](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para examinar a interação do modelo com a identidade.</span><span class="sxs-lookup"><span data-stu-id="e33d2-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="e33d2-113">Normalmente, a identidade é configurada usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="e33d2-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="e33d2-114">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="e33d2-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="e33d2-115">Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="e33d2-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="e33d2-116">Observação: os modelos tratam o nome de usuário e o email como o mesmo para os usuários.</span><span class="sxs-lookup"><span data-stu-id="e33d2-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="e33d2-117">Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="e33d2-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="e33d2-118">A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:</span><span class="sxs-lookup"><span data-stu-id="e33d2-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="e33d2-119">Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).</span><span class="sxs-lookup"><span data-stu-id="e33d2-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="e33d2-120">Não relacionado à identidade de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e33d2-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="e33d2-121">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e33d2-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="e33d2-122">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="e33d2-122">Create a Web app with authentication</span></span>

<span data-ttu-id="e33d2-123">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="e33d2-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e33d2-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e33d2-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e33d2-125">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="e33d2-126">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="e33d2-127">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="e33d2-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="e33d2-128">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-128">Click **OK**.</span></span>
* <span data-ttu-id="e33d2-129">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="e33d2-130">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e33d2-131">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="e33d2-132">O comando anterior cria um aplicativo Web Razor usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="e33d2-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="e33d2-133">Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e33d2-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="e33d2-134">O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="e33d2-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="e33d2-135">A biblioteca de classes de identidade Razor expõe pontos de extremidade `Identity` com a área.</span><span class="sxs-lookup"><span data-stu-id="e33d2-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="e33d2-136">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e33d2-136">For example:</span></span>

* <span data-ttu-id="e33d2-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="e33d2-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="e33d2-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="e33d2-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="e33d2-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="e33d2-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="e33d2-140">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="e33d2-140">Apply migrations</span></span>

<span data-ttu-id="e33d2-141">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e33d2-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e33d2-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e33d2-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e33d2-143">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="e33d2-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="e33d2-144">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e33d2-145">As migrações não são necessárias nesta etapa ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="e33d2-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="e33d2-146">Para o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e33d2-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="e33d2-147">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="e33d2-147">Test Register and Login</span></span>

<span data-ttu-id="e33d2-148">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="e33d2-148">Run the app and register a user.</span></span> <span data-ttu-id="e33d2-149">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="e33d2-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="e33d2-150">Configurar serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="e33d2-150">Configure Identity services</span></span>

<span data-ttu-id="e33d2-151">Os serviços são adicionados `ConfigureServices`no.</span><span class="sxs-lookup"><span data-stu-id="e33d2-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="e33d2-152">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="e33d2-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="e33d2-153">O código realçado anterior configura a identidade com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="e33d2-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="e33d2-154">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e33d2-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="e33d2-155">A identidade é habilitada <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>chamando.</span><span class="sxs-lookup"><span data-stu-id="e33d2-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="e33d2-156">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="e33d2-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="e33d2-157">O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="e33d2-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="e33d2-158">`app.UseAuthorization`está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização.</span><span class="sxs-lookup"><span data-stu-id="e33d2-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="e33d2-159">`UseRouting``UseAuthorization` `UseEndpoints` , `UseAuthentication`, e devem ser chamados na ordem mostrada no código anterior.</span><span class="sxs-lookup"><span data-stu-id="e33d2-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="e33d2-160">Para obter mais informações `IdentityOptions` sobre `Startup`o e <xref:Microsoft.AspNetCore.Identity.IdentityOptions> o, consulte e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="e33d2-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="e33d2-161">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="e33d2-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e33d2-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e33d2-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e33d2-163">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="e33d2-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="e33d2-164">Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="e33d2-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e33d2-165">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e33d2-166">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="e33d2-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="e33d2-167">Caso contrário, use o namespace correto para `ApplicationDbContext`o:</span><span class="sxs-lookup"><span data-stu-id="e33d2-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="e33d2-168">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="e33d2-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="e33d2-169">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="e33d2-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="e33d2-170">Para obter mais informações sobre a identidade do scaffolding, consulte [identidade do Scaffold em um projeto do Razor com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="e33d2-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="e33d2-171">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="e33d2-171">Examine Register</span></span>

<span data-ttu-id="e33d2-172">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="e33d2-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="e33d2-173">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="e33d2-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="e33d2-174">`_userManager`é fornecido pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="e33d2-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="e33d2-175">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="e33d2-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="e33d2-176">Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="e33d2-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="e33d2-177">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="e33d2-177">Log in</span></span>

<span data-ttu-id="e33d2-178">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="e33d2-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="e33d2-179">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="e33d2-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="e33d2-180">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="e33d2-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="e33d2-181">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="e33d2-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="e33d2-182">`PasswordSignInAsync`é chamado no `_signInManager` objeto (fornecido pela injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="e33d2-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="e33d2-183">A classe `Controller` base expõe uma `User` propriedade que pode ser acessada por meio de métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="e33d2-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="e33d2-184">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="e33d2-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="e33d2-185">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="e33d2-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="e33d2-186">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="e33d2-186">Log out</span></span>

<span data-ttu-id="e33d2-187">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="e33d2-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="e33d2-188">No código anterior, o código `return RedirectToPage();` precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.</span><span class="sxs-lookup"><span data-stu-id="e33d2-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="e33d2-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="e33d2-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="e33d2-190">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e33d2-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="e33d2-191">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="e33d2-191">Test Identity</span></span>

<span data-ttu-id="e33d2-192">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="e33d2-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="e33d2-193">Para testar a identidade, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Adicione:</span><span class="sxs-lookup"><span data-stu-id="e33d2-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="e33d2-194">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="e33d2-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="e33d2-195">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="e33d2-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="e33d2-196">Explorar identidade</span><span class="sxs-lookup"><span data-stu-id="e33d2-196">Explore Identity</span></span>

<span data-ttu-id="e33d2-197">Para explorar a identidade com mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="e33d2-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="e33d2-198">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="e33d2-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="e33d2-199">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="e33d2-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="e33d2-200">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="e33d2-200">Identity Components</span></span>

<span data-ttu-id="e33d2-201">Todos os pacotes NuGet dependentes de identidade estão incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="e33d2-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="e33d2-202">O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="e33d2-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="e33d2-203">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="e33d2-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="e33d2-204">Migrando para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="e33d2-205">Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="e33d2-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="e33d2-206">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="e33d2-206">Setting password strength</span></span>

<span data-ttu-id="e33d2-207">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="e33d2-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="e33d2-208">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="e33d2-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="e33d2-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="e33d2-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="e33d2-210">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e33d2-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="e33d2-211">Consulte [AddDefaultIdentity Source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="e33d2-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="e33d2-212">Impedir a publicação de ativos de identidade estática</span><span class="sxs-lookup"><span data-stu-id="e33d2-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="e33d2-213">Para evitar a publicação de ativos de identidade estática (folhas de estilo e arquivos JavaScript para a interface do usuário de identidade) `ResolveStaticWebAssetsInputsDependsOn` na raiz `RemoveIdentityAssets` da Web, adicione a seguinte propriedade e destino ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e33d2-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="e33d2-214">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e33d2-214">Next Steps</span></span>

* <span data-ttu-id="e33d2-215">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar a identidade usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="e33d2-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="e33d2-216">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="e33d2-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e33d2-217">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e33d2-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e33d2-218">ASP.NET Core identidade é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e33d2-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="e33d2-219">Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="e33d2-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="e33d2-220">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e33d2-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="e33d2-221">A identidade pode ser configurada usando um banco de dados de SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="e33d2-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="e33d2-222">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="e33d2-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="e33d2-223">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e33d2-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e33d2-224">Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="e33d2-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="e33d2-225">Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="e33d2-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="e33d2-226">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="e33d2-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="e33d2-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="e33d2-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="e33d2-228">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e33d2-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="e33d2-229">Consulte [AddDefaultIdentity Source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="e33d2-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="e33d2-230">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="e33d2-230">Create a Web app with authentication</span></span>

<span data-ttu-id="e33d2-231">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="e33d2-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e33d2-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e33d2-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e33d2-233">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="e33d2-234">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="e33d2-235">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="e33d2-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="e33d2-236">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-236">Click **OK**.</span></span>
* <span data-ttu-id="e33d2-237">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="e33d2-238">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="e33d2-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e33d2-239">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="e33d2-240">O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="e33d2-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="e33d2-241">A biblioteca de classes de identidade Razor expõe pontos de extremidade `Identity` com a área.</span><span class="sxs-lookup"><span data-stu-id="e33d2-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="e33d2-242">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e33d2-242">For example:</span></span>

* <span data-ttu-id="e33d2-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="e33d2-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="e33d2-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="e33d2-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="e33d2-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="e33d2-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="e33d2-246">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="e33d2-246">Apply migrations</span></span>

<span data-ttu-id="e33d2-247">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e33d2-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e33d2-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e33d2-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e33d2-249">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="e33d2-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="e33d2-250">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="e33d2-251">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="e33d2-251">Test Register and Login</span></span>

<span data-ttu-id="e33d2-252">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="e33d2-252">Run the app and register a user.</span></span> <span data-ttu-id="e33d2-253">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="e33d2-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="e33d2-254">Configurar serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="e33d2-254">Configure Identity services</span></span>

<span data-ttu-id="e33d2-255">Os serviços são adicionados `ConfigureServices`no.</span><span class="sxs-lookup"><span data-stu-id="e33d2-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="e33d2-256">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="e33d2-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="e33d2-257">O código anterior configura a identidade com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="e33d2-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="e33d2-258">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e33d2-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="e33d2-259">A identidade é habilitada chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="e33d2-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="e33d2-260">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="e33d2-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="e33d2-261">Para obter mais informações, consulte a [classe identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="e33d2-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="e33d2-262">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="e33d2-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="e33d2-263">Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="e33d2-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e33d2-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e33d2-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e33d2-265">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="e33d2-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e33d2-266">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e33d2-267">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="e33d2-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="e33d2-268">Caso contrário, use o namespace correto para `ApplicationDbContext`o:</span><span class="sxs-lookup"><span data-stu-id="e33d2-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="e33d2-269">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="e33d2-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="e33d2-270">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="e33d2-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="e33d2-271">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="e33d2-271">Examine Register</span></span>

<span data-ttu-id="e33d2-272">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="e33d2-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="e33d2-273">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="e33d2-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="e33d2-274">`_userManager`é fornecido pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="e33d2-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="e33d2-275">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="e33d2-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="e33d2-276">**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="e33d2-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="e33d2-277">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="e33d2-277">Log in</span></span>

<span data-ttu-id="e33d2-278">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="e33d2-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="e33d2-279">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="e33d2-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="e33d2-280">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="e33d2-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="e33d2-281">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="e33d2-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="e33d2-282">`PasswordSignInAsync`é chamado no `_signInManager` objeto (fornecido pela injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="e33d2-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="e33d2-283">A classe `Controller` base expõe uma `User` propriedade que você pode acessar por meio de métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="e33d2-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="e33d2-284">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="e33d2-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="e33d2-285">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="e33d2-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="e33d2-286">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="e33d2-286">Log out</span></span>

<span data-ttu-id="e33d2-287">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="e33d2-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="e33d2-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="e33d2-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="e33d2-289">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e33d2-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="e33d2-290">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="e33d2-290">Test Identity</span></span>

<span data-ttu-id="e33d2-291">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="e33d2-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="e33d2-292">Para testar a identidade, [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) adicione à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="e33d2-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="e33d2-293">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="e33d2-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="e33d2-294">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="e33d2-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="e33d2-295">Explorar identidade</span><span class="sxs-lookup"><span data-stu-id="e33d2-295">Explore Identity</span></span>

<span data-ttu-id="e33d2-296">Para explorar a identidade com mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="e33d2-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="e33d2-297">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="e33d2-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="e33d2-298">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="e33d2-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="e33d2-299">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="e33d2-299">Identity Components</span></span>

<span data-ttu-id="e33d2-300">Todos os pacotes NuGet dependentes de identidade estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e33d2-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="e33d2-301">O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="e33d2-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="e33d2-302">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="e33d2-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="e33d2-303">Migrando para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e33d2-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="e33d2-304">Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="e33d2-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="e33d2-305">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="e33d2-305">Setting password strength</span></span>

<span data-ttu-id="e33d2-306">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="e33d2-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e33d2-307">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e33d2-307">Next Steps</span></span>

* <span data-ttu-id="e33d2-308">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar a identidade usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="e33d2-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="e33d2-309">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="e33d2-309">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
