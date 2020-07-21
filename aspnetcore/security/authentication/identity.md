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
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="30964-104">Introdução ao :::no-loc(Identity)::: no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30964-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="30964-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="30964-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="30964-106">ASP.NET Core :::no-loc(Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="30964-106">ASP.NET Core :::no-loc(Identity)::::</span></span>

* <span data-ttu-id="30964-107">É uma API que dá suporte à funcionalidade de logon da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="30964-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="30964-108">Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.</span><span class="sxs-lookup"><span data-stu-id="30964-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="30964-109">Os usuários podem criar uma conta com as informações de logon armazenadas no :::no-loc(Identity)::: ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="30964-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="30964-110">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="30964-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="30964-111">O [ :::no-loc(Identity)::: código-fonte](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) está disponível no github.</span><span class="sxs-lookup"><span data-stu-id="30964-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="30964-112">[Scaffold :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para revisar a interação do modelo com o :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="30964-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="30964-113">:::no-loc(Identity):::Normalmente, o é configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="30964-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="30964-114">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="30964-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="30964-115">Neste tópico, você aprenderá a usar o :::no-loc(Identity)::: para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="30964-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="30964-116">Observação: os modelos tratam o nome de usuário e o email como o mesmo para os usuários.</span><span class="sxs-lookup"><span data-stu-id="30964-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="30964-117">Para obter instruções mais detalhadas sobre como criar aplicativos que usam o :::no-loc(Identity)::: , consulte [próximas etapas](#next).</span><span class="sxs-lookup"><span data-stu-id="30964-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="30964-118">A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:</span><span class="sxs-lookup"><span data-stu-id="30964-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="30964-119">Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).</span><span class="sxs-lookup"><span data-stu-id="30964-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="30964-120">Não relacionado a ASP.NET Core :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="30964-120">Unrelated to ASP.NET Core :::no-loc(Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="30964-121">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="30964-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="30964-122">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="30964-122">Create a Web app with authentication</span></span>

<span data-ttu-id="30964-123">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="30964-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30964-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30964-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30964-125">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="30964-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="30964-126">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="30964-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="30964-127">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="30964-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="30964-128">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="30964-128">Click **OK**.</span></span>
* <span data-ttu-id="30964-129">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="30964-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="30964-130">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="30964-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30964-131">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30964-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="30964-132">O comando anterior cria um :::no-loc(Razor)::: aplicativo Web usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="30964-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="30964-133">Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="30964-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="30964-134">O projeto gerado fornece [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="30964-134">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="30964-135">A :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de classes expõe pontos de extremidade com a `:::no-loc(Identity):::` área.</span><span class="sxs-lookup"><span data-stu-id="30964-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="30964-136">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="30964-136">For example:</span></span>

* <span data-ttu-id="30964-137">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="30964-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="30964-138">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="30964-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="30964-139">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="30964-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="30964-140">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="30964-140">Apply migrations</span></span>

<span data-ttu-id="30964-141">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="30964-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30964-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30964-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30964-143">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="30964-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="30964-144">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30964-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30964-145">As migrações não são necessárias nesta etapa ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="30964-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="30964-146">Para o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="30964-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="30964-147">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="30964-147">Test Register and Login</span></span>

<span data-ttu-id="30964-148">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="30964-148">Run the app and register a user.</span></span> <span data-ttu-id="30964-149">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="30964-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="30964-150">Configurar :::no-loc(Identity)::: serviços</span><span class="sxs-lookup"><span data-stu-id="30964-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="30964-151">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="30964-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="30964-152">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="30964-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="30964-153">O código realçado anterior :::no-loc(Identity)::: é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="30964-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="30964-154">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="30964-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="30964-155">:::no-loc(Identity):::é habilitado chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="30964-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="30964-156">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="30964-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="30964-157">O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="30964-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="30964-158">`app.UseAuthorization`está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização.</span><span class="sxs-lookup"><span data-stu-id="30964-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="30964-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.</span><span class="sxs-lookup"><span data-stu-id="30964-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="30964-160">Para obter mais informações sobre o `:::no-loc(Identity):::Options` e o `Startup` , consulte <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="30964-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="30964-161">Scaffold registro, logon, LogOut e RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="30964-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30964-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30964-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30964-163">Adicione os `Register` `Login` arquivos,, `LogOut` e `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="30964-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="30964-164">Siga a [identidade do Scaffold em um :::no-loc(Razor)::: projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="30964-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30964-165">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30964-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30964-166">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="30964-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="30964-167">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="30964-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="30964-168">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="30964-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="30964-169">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="30964-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="30964-170">Para obter mais informações sobre scaffolding :::no-loc(Identity)::: , consulte [identidade do Scaffold em um :::no-loc(Razor)::: projeto com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="30964-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="30964-171">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="30964-171">Examine Register</span></span>

<span data-ttu-id="30964-172">Quando um usuário clica no botão **registrar** na `Register` página, a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="30964-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="30964-173">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="30964-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="30964-174">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="30964-174">Log in</span></span>

<span data-ttu-id="30964-175">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="30964-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="30964-176">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="30964-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="30964-177">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="30964-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="30964-178">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="30964-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="30964-179">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="30964-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="30964-180">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="30964-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="30964-181">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="30964-181">Log out</span></span>

<span data-ttu-id="30964-182">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="30964-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="30964-183">No código anterior, o código `return RedirectToPage();` precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.</span><span class="sxs-lookup"><span data-stu-id="30964-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="30964-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="30964-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="30964-185">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="30964-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="30964-186">Testar:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-187">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="30964-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="30964-188">Para testar :::no-loc(Identity)::: , adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="30964-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="30964-189">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="30964-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="30964-190">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="30964-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="30964-191">Apresenta:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-192">Para explorar :::no-loc(Identity)::: mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="30964-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="30964-193">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="30964-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="30964-194">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="30964-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="30964-195">:::no-loc(Identity):::QC</span><span class="sxs-lookup"><span data-stu-id="30964-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="30964-196">Todos os :::no-loc(Identity)::: pacotes NuGet dependentes são incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="30964-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="30964-197">O pacote principal para o :::no-loc(Identity)::: é [Microsoft. AspNetCore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span><span class="sxs-lookup"><span data-stu-id="30964-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="30964-198">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: e é incluído pelo `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="30964-198">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="30964-199">Migrando para o ASP.NET Core:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-199">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-200">Para obter mais informações e diretrizes sobre como migrar seu :::no-loc(Identity)::: repositório existente, consulte [ :::no-loc(Identity)::: migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="30964-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="30964-201">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="30964-201">Setting password strength</span></span>

<span data-ttu-id="30964-202">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="30964-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="30964-203">ADDDEFAULT :::no-loc(Identity)::: e Add:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="30964-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="30964-205">Chamar `AddDefault:::no-loc(Identity):::` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="30964-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="30964-206">Consulte a [ :::no-loc(Identity)::: fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="30964-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="30964-207">Impedir a publicação de :::no-loc(Identity)::: ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="30964-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="30964-208">Para evitar a publicação de ativos estáticos :::no-loc(Identity)::: (folhas de estilo e arquivos JavaScript para :::no-loc(Identity)::: a interface do usuário) na raiz da Web, adicione a seguinte `ResolveStaticWebAssetsInputsDependsOn` propriedade e `Remove:::no-loc(Identity):::Assets` destino ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="30964-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="30964-209">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="30964-209">Next Steps</span></span>

* <span data-ttu-id="30964-210">[ASP.NET Core :::no-loc(Identity)::: código-fonte](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="30964-210">[ASP.NET Core :::no-loc(Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="30964-211">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o :::no-loc(Identity)::: uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="30964-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="30964-212">Configurar:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="30964-213">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="30964-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="30964-214">ASP.NET Core :::no-loc(Identity)::: é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30964-214">ASP.NET Core :::no-loc(Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="30964-215">Os usuários podem criar uma conta com as informações de logon armazenadas no :::no-loc(Identity)::: ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="30964-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="30964-216">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="30964-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="30964-217">:::no-loc(Identity):::o pode ser configurado usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="30964-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="30964-218">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="30964-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="30964-219">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="30964-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="30964-220">Neste tópico, você aprenderá a usar o :::no-loc(Identity)::: para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="30964-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="30964-221">Para obter instruções mais detalhadas sobre como criar aplicativos que usam :::no-loc(Identity)::: o, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="30964-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="30964-222">ADDDEFAULT :::no-loc(Identity)::: e Add:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="30964-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="30964-224">Chamar `AddDefault:::no-loc(Identity):::` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="30964-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="30964-225">Consulte a [ :::no-loc(Identity)::: fonte padrão](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="30964-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="30964-226">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="30964-226">Create a Web app with authentication</span></span>

<span data-ttu-id="30964-227">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="30964-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30964-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30964-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30964-229">Selecione **arquivo** > **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="30964-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="30964-230">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="30964-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="30964-231">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="30964-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="30964-232">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="30964-232">Click **OK**.</span></span>
* <span data-ttu-id="30964-233">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="30964-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="30964-234">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="30964-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30964-235">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30964-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="30964-236">O projeto gerado fornece [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como uma [ :::no-loc(Razor)::: biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="30964-236">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="30964-237">A :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de classes expõe pontos de extremidade com a `:::no-loc(Identity):::` área.</span><span class="sxs-lookup"><span data-stu-id="30964-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="30964-238">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="30964-238">For example:</span></span>

* <span data-ttu-id="30964-239">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="30964-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="30964-240">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="30964-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="30964-241">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="30964-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="30964-242">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="30964-242">Apply migrations</span></span>

<span data-ttu-id="30964-243">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="30964-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30964-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30964-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30964-245">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="30964-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="30964-246">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30964-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="30964-247">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="30964-247">Test Register and Login</span></span>

<span data-ttu-id="30964-248">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="30964-248">Run the app and register a user.</span></span> <span data-ttu-id="30964-249">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="30964-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="30964-250">Configurar :::no-loc(Identity)::: serviços</span><span class="sxs-lookup"><span data-stu-id="30964-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="30964-251">Os serviços são adicionados no `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="30964-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="30964-252">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="30964-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="30964-253">O código anterior :::no-loc(Identity)::: é configurado com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="30964-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="30964-254">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="30964-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="30964-255">:::no-loc(Identity):::é habilitado chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="30964-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="30964-256">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="30964-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="30964-257">Para obter mais informações, consulte a [ :::no-loc(Identity)::: classe Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="30964-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="30964-258">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="30964-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="30964-259">Siga a [identidade do Scaffold em um :::no-loc(Razor)::: projeto com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="30964-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30964-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30964-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30964-261">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="30964-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30964-262">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30964-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30964-263">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="30964-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="30964-264">Caso contrário, use o namespace correto para o `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="30964-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="30964-265">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="30964-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="30964-266">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="30964-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="30964-267">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="30964-267">Examine Register</span></span>

<span data-ttu-id="30964-268">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="30964-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="30964-269">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="30964-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="30964-270">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="30964-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="30964-271">**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="30964-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="30964-272">Fazer logon</span><span class="sxs-lookup"><span data-stu-id="30964-272">Log in</span></span>

<span data-ttu-id="30964-273">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="30964-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="30964-274">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="30964-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="30964-275">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="30964-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="30964-276">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="30964-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="30964-277">`PasswordSignInAsync`é chamado no `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="30964-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="30964-278">Para obter informações sobre como tomar decisões de autorização, consulte <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="30964-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="30964-279">Faça logoff</span><span class="sxs-lookup"><span data-stu-id="30964-279">Log out</span></span>

<span data-ttu-id="30964-280">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="30964-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="30964-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="30964-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="30964-282">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="30964-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="30964-283">Testar:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-284">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="30964-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="30964-285">Para testar :::no-loc(Identity)::: , adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="30964-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="30964-286">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="30964-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="30964-287">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="30964-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="30964-288">Apresenta:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-289">Para explorar :::no-loc(Identity)::: mais detalhadamente:</span><span class="sxs-lookup"><span data-stu-id="30964-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="30964-290">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="30964-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="30964-291">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="30964-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="30964-292">:::no-loc(Identity):::QC</span><span class="sxs-lookup"><span data-stu-id="30964-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="30964-293">Todos os :::no-loc(Identity)::: pacotes NuGet dependentes estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="30964-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="30964-294">O pacote principal para o :::no-loc(Identity)::: é [Microsoft. AspNetCore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span><span class="sxs-lookup"><span data-stu-id="30964-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="30964-295">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: e é incluído pelo `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="30964-295">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="30964-296">Migrando para o ASP.NET Core:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-296">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="30964-297">Para obter mais informações e diretrizes sobre como migrar seu :::no-loc(Identity)::: repositório existente, consulte [ :::no-loc(Identity)::: migrar autenticação e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="30964-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="30964-298">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="30964-298">Setting password strength</span></span>

<span data-ttu-id="30964-299">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="30964-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="30964-300">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="30964-300">Next Steps</span></span>

* <span data-ttu-id="30964-301">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar o :::no-loc(Identity)::: uso do SQLite.</span><span class="sxs-lookup"><span data-stu-id="30964-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="30964-302">Configurar:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="30964-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
