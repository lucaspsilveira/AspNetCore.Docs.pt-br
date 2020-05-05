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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768384"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="95b16-103">Scaffold Identity em projetos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95b16-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="95b16-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="95b16-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="95b16-105">ASP.NET Core fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="95b16-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="95b16-106">Os aplicativos que Identity incluem o podem aplicar o scaffolder para adicionar seletivamente o código-fonte Identity Razor contido na biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="95b16-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="95b16-107">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="95b16-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="95b16-108">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="95b16-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="95b16-109">O código gerado tem precedência sobre o mesmo código Identity no RCL.</span><span class="sxs-lookup"><span data-stu-id="95b16-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="95b16-110">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).</span><span class="sxs-lookup"><span data-stu-id="95b16-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="95b16-111">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o pacote Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="95b16-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="95b16-112">Você tem a opção de selecionar Identity o código a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="95b16-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="95b16-113">Embora o scaffolder gere a maior parte do código necessário, você precisa atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="95b16-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="95b16-114">Este documento explica as etapas necessárias para concluir uma Identity atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="95b16-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="95b16-115">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="95b16-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="95b16-116">Inspecione as alterações depois de executar Identity o scaffolder.</span><span class="sxs-lookup"><span data-stu-id="95b16-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="95b16-117">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e Identityoutros recursos de segurança com o.</span><span class="sxs-lookup"><span data-stu-id="95b16-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="95b16-118">Serviços ou stubs de serviço não são Identitygerados quando scaffolding.</span><span class="sxs-lookup"><span data-stu-id="95b16-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="95b16-119">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="95b16-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="95b16-120">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="95b16-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="95b16-121">Quando scaffolding Identity com um novo contexto de dados em um projeto com contas individuais existentes:</span><span class="sxs-lookup"><span data-stu-id="95b16-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="95b16-122">No `Startup.ConfigureServices`, remova as chamadas para:</span><span class="sxs-lookup"><span data-stu-id="95b16-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="95b16-123">Por exemplo, `AddDbContext` e `AddDefaultIdentity` são comentados no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="95b16-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="95b16-124">O código anterior comenta o código que está duplicado em *áreas/Identity/IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="95b16-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="95b16-125">Normalmente, os aplicativos que foram criados com contas individuais ***não*** devem criar um novo contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="95b16-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="95b16-126">Scaffold identidade em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="95b16-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="95b16-127">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="95b16-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="95b16-128">Scaffold identidade em um Razor projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="95b16-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="95b16-129">é configurado em *áreas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="95b16-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="95b16-130">para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95b16-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="95b16-131">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="95b16-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="95b16-132">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="95b16-132">Enable authentication</span></span>

<span data-ttu-id="95b16-133">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="95b16-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="95b16-134">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="95b16-134">Layout changes</span></span>

<span data-ttu-id="95b16-135">Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="95b16-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="95b16-136">Scaffold identidade em um Razor projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="95b16-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="95b16-137">Algumas Identity opções são configuradas em *áreas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="95b16-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="95b16-138">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95b16-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="95b16-139">Scaffold identidade em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="95b16-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="95b16-140">Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="95b16-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="95b16-141">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="95b16-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="95b16-142">é configurado em *áreas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="95b16-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="95b16-143">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="95b16-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="95b16-144">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="95b16-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="95b16-145">Scaffold identidade em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="95b16-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="95b16-146">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="95b16-146">Create full identity UI source</span></span>

<span data-ttu-id="95b16-147">Para manter o controle total da Identity interface do usuário, Identity execute o scaffolder e selecione **substituir todos os arquivos**.</span><span class="sxs-lookup"><span data-stu-id="95b16-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="95b16-148">O código realçado a seguir mostra as alterações para Identity substituir a Identity interface do usuário padrão por em um aplicativo Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="95b16-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="95b16-149">Talvez você queira fazer isso para ter controle total da Identity interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="95b16-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="95b16-150">O padrão Identity é substituído no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="95b16-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="95b16-151">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="95b16-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="95b16-152">Registre uma `IEmailSender` implementação, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="95b16-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="95b16-153">Página desabilitar registro</span><span class="sxs-lookup"><span data-stu-id="95b16-153">Disable register page</span></span>

<span data-ttu-id="95b16-154">Para desabilitar o registro do usuário:</span><span class="sxs-lookup"><span data-stu-id="95b16-154">To disable user registration:</span></span>

* <span data-ttu-id="95b16-155">Scaffold Identity.</span><span class="sxs-lookup"><span data-stu-id="95b16-155">Scaffold Identity.</span></span> <span data-ttu-id="95b16-156">Inclua conta. Register, Account. login e Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="95b16-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="95b16-157">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="95b16-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="95b16-158">Atualizar *áreas/Identity/Pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="95b16-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="95b16-159">Atualize *áreas/Identity/Pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:</span><span class="sxs-lookup"><span data-stu-id="95b16-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="95b16-160">Comente ou remova o link de registro de *áreas/Identity/Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="95b16-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="95b16-161">Atualize a página *áreasIdentity//Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="95b16-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="95b16-162">Remova o código e os links do arquivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="95b16-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="95b16-163">Remova o código de confirmação do `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="95b16-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="95b16-164">Usar outro aplicativo para adicionar usuários</span><span class="sxs-lookup"><span data-stu-id="95b16-164">Use another app to add users</span></span>

<span data-ttu-id="95b16-165">Forneça um mecanismo para adicionar usuários fora do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="95b16-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="95b16-166">As opções para adicionar usuários incluem:</span><span class="sxs-lookup"><span data-stu-id="95b16-166">Options to add users include:</span></span>

* <span data-ttu-id="95b16-167">Um aplicativo Web de administrador dedicado.</span><span class="sxs-lookup"><span data-stu-id="95b16-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="95b16-168">Um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="95b16-168">A console app.</span></span>

<span data-ttu-id="95b16-169">O código a seguir descreve uma abordagem para adicionar usuários:</span><span class="sxs-lookup"><span data-stu-id="95b16-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="95b16-170">Uma lista de usuários é lida na memória.</span><span class="sxs-lookup"><span data-stu-id="95b16-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="95b16-171">Uma senha exclusiva forte é gerada para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="95b16-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="95b16-172">O usuário é adicionado ao banco Identity de dados.</span><span class="sxs-lookup"><span data-stu-id="95b16-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="95b16-173">O usuário é notificado e foi instruído a alterar a senha.</span><span class="sxs-lookup"><span data-stu-id="95b16-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="95b16-174">O código a seguir descreve como adicionar um usuário:</span><span class="sxs-lookup"><span data-stu-id="95b16-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="95b16-175">Uma abordagem semelhante pode ser seguida para cenários de produção.</span><span class="sxs-lookup"><span data-stu-id="95b16-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="95b16-176">Impedir a publicação de Identity ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="95b16-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="95b16-177">Para evitar a publicação Identity de ativos estáticos na raiz da <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>Web, consulte.</span><span class="sxs-lookup"><span data-stu-id="95b16-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95b16-178">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="95b16-178">Additional resources</span></span>

* [<span data-ttu-id="95b16-179">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="95b16-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="95b16-180">ASP.NET Core 2,1 e posterior fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="95b16-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="95b16-181">Os aplicativos que Identity incluem o podem aplicar o scaffolder para adicionar seletivamente o código-fonte Identity Razor contido na biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="95b16-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="95b16-182">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="95b16-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="95b16-183">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="95b16-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="95b16-184">O código gerado tem precedência sobre o mesmo código Identity no RCL.</span><span class="sxs-lookup"><span data-stu-id="95b16-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="95b16-185">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).</span><span class="sxs-lookup"><span data-stu-id="95b16-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="95b16-186">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o pacote Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="95b16-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="95b16-187">Você tem a opção de selecionar Identity o código a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="95b16-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="95b16-188">Embora o scaffolder gere a maior parte do código necessário, você precisará atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="95b16-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="95b16-189">Este documento explica as etapas necessárias para concluir uma Identity atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="95b16-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="95b16-190">Quando o Identity scaffolder é executado, um arquivo *ScaffoldingReadme. txt* é criado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="95b16-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="95b16-191">O arquivo *ScaffoldingReadme. txt* contém instruções gerais sobre o que é necessário para concluir Identity a atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="95b16-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="95b16-192">Este documento contém instruções mais completas do que o arquivo *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="95b16-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="95b16-193">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="95b16-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="95b16-194">Inspecione as alterações depois de executar Identity o scaffolder.</span><span class="sxs-lookup"><span data-stu-id="95b16-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="95b16-195">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e Identityoutros recursos de segurança com o.</span><span class="sxs-lookup"><span data-stu-id="95b16-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="95b16-196">Serviços ou stubs de serviço não são Identitygerados quando scaffolding.</span><span class="sxs-lookup"><span data-stu-id="95b16-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="95b16-197">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="95b16-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="95b16-198">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="95b16-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="95b16-199">Scaffold identidade em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="95b16-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="95b16-200">Adicione as seguintes chamadas realçadas `Startup` à classe:</span><span class="sxs-lookup"><span data-stu-id="95b16-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="95b16-201">Scaffold identidade em um Razor projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="95b16-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="95b16-202">é configurado em *áreas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="95b16-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="95b16-203">para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95b16-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="95b16-204">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="95b16-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="95b16-205">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="95b16-205">Enable authentication</span></span>

<span data-ttu-id="95b16-206">No `Configure` método da `Startup` classe, chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após: `UseStaticFiles`</span><span class="sxs-lookup"><span data-stu-id="95b16-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="95b16-207">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="95b16-207">Layout changes</span></span>

<span data-ttu-id="95b16-208">Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="95b16-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="95b16-209">Scaffold identidade em um Razor projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="95b16-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="95b16-210">Algumas Identity opções são configuradas em *áreas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="95b16-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="95b16-211">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95b16-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="95b16-212">Scaffold identidade em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="95b16-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="95b16-213">Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="95b16-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="95b16-214">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="95b16-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="95b16-215">é configurado em *áreas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="95b16-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="95b16-216">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="95b16-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="95b16-217">Chamar [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="95b16-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="95b16-218">Scaffold identidade em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="95b16-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="95b16-219">Exclua as *páginas/pasta compartilhada* e os arquivos nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="95b16-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="95b16-220">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="95b16-220">Create full identity UI source</span></span>

<span data-ttu-id="95b16-221">Para manter o controle total da Identity interface do usuário, Identity execute o scaffolder e selecione **substituir todos os arquivos**.</span><span class="sxs-lookup"><span data-stu-id="95b16-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="95b16-222">O código realçado a seguir mostra as alterações para Identity substituir a Identity interface do usuário padrão por em um aplicativo Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="95b16-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="95b16-223">Talvez você queira fazer isso para ter controle total da Identity interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="95b16-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="95b16-224">O padrão Identity é substituído no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="95b16-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="95b16-225">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="95b16-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="95b16-226">Registre uma `IEmailSender` implementação, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="95b16-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="95b16-227">Página desabilitar registro</span><span class="sxs-lookup"><span data-stu-id="95b16-227">Disable register page</span></span>

<span data-ttu-id="95b16-228">Para desabilitar o registro do usuário:</span><span class="sxs-lookup"><span data-stu-id="95b16-228">To disable user registration:</span></span>

* <span data-ttu-id="95b16-229">Scaffold Identity.</span><span class="sxs-lookup"><span data-stu-id="95b16-229">Scaffold Identity.</span></span> <span data-ttu-id="95b16-230">Inclua conta. Register, Account. login e Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="95b16-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="95b16-231">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="95b16-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="95b16-232">Atualizar *áreas/Identity/Pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="95b16-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="95b16-233">Atualize *áreas/Identity/Pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:</span><span class="sxs-lookup"><span data-stu-id="95b16-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="95b16-234">Comente ou remova o link de registro de *áreas/Identity/Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="95b16-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="95b16-235">Atualize a página *áreasIdentity//Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="95b16-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="95b16-236">Remova o código e os links do arquivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="95b16-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="95b16-237">Remova o código de confirmação do `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="95b16-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="95b16-238">Usar outro aplicativo para adicionar usuários</span><span class="sxs-lookup"><span data-stu-id="95b16-238">Use another app to add users</span></span>

<span data-ttu-id="95b16-239">Forneça um mecanismo para adicionar usuários fora do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="95b16-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="95b16-240">As opções para adicionar usuários incluem:</span><span class="sxs-lookup"><span data-stu-id="95b16-240">Options to add users include:</span></span>

* <span data-ttu-id="95b16-241">Um aplicativo Web de administrador dedicado.</span><span class="sxs-lookup"><span data-stu-id="95b16-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="95b16-242">Um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="95b16-242">A console app.</span></span>

<span data-ttu-id="95b16-243">O código a seguir descreve uma abordagem para adicionar usuários:</span><span class="sxs-lookup"><span data-stu-id="95b16-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="95b16-244">Uma lista de usuários é lida na memória.</span><span class="sxs-lookup"><span data-stu-id="95b16-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="95b16-245">Uma senha exclusiva forte é gerada para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="95b16-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="95b16-246">O usuário é adicionado ao banco Identity de dados.</span><span class="sxs-lookup"><span data-stu-id="95b16-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="95b16-247">O usuário é notificado e foi instruído a alterar a senha.</span><span class="sxs-lookup"><span data-stu-id="95b16-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="95b16-248">O código a seguir descreve como adicionar um usuário:</span><span class="sxs-lookup"><span data-stu-id="95b16-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="95b16-249">Uma abordagem semelhante pode ser seguida para cenários de produção.</span><span class="sxs-lookup"><span data-stu-id="95b16-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95b16-250">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="95b16-250">Additional resources</span></span>

* [<span data-ttu-id="95b16-251">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="95b16-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end