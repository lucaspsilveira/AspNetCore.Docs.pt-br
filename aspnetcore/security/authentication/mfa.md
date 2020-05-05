---
title: Autenticação multifator no ASP.NET Core
author: damienbod
description: Saiba como configurar a MFA (autenticação multifator) em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: e2f34a72515a700223ce83ce6ec8b55020599ab0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767415"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="fb151-103">Autenticação multifator no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb151-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="fb151-104">Por [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="fb151-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

<span data-ttu-id="fb151-105">A autenticação multifator (MFA) é um processo no qual um usuário é solicitado durante um evento de entrada para formulários adicionais de identificação.</span><span class="sxs-lookup"><span data-stu-id="fb151-105">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="fb151-106">Esse prompt pode ser para inserir um código de um celular, usar uma chave FIDO2 ou para fornecer uma verificação de impressão digital.</span><span class="sxs-lookup"><span data-stu-id="fb151-106">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="fb151-107">Quando você precisa de uma segunda forma de autenticação, a segurança é aprimorada.</span><span class="sxs-lookup"><span data-stu-id="fb151-107">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="fb151-108">O fator adicional não é facilmente obtido ou duplicado por um invasor.</span><span class="sxs-lookup"><span data-stu-id="fb151-108">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="fb151-109">Este artigo aborda as seguintes áreas:</span><span class="sxs-lookup"><span data-stu-id="fb151-109">This article covers the following areas:</span></span>

* <span data-ttu-id="fb151-110">O que é MFA e quais fluxos de MFA são recomendados</span><span class="sxs-lookup"><span data-stu-id="fb151-110">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="fb151-111">Configurar o MFA para páginas de administração usando ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="fb151-111">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="fb151-112">Enviar requisito de entrada MFA ao servidor OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="fb151-112">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="fb151-113">Forçar ASP.NET Core cliente do OpenID Connect a exigir MFA</span><span class="sxs-lookup"><span data-stu-id="fb151-113">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="fb151-114">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="fb151-114">MFA, 2FA</span></span>

<span data-ttu-id="fb151-115">A MFA exige pelo menos dois ou mais tipos de provas para uma identidade como algo que você conhece, algo que você possui ou validação biométrica para o usuário se autenticar.</span><span class="sxs-lookup"><span data-stu-id="fb151-115">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="fb151-116">A autenticação de dois fatores (2FA) é como um subconjunto de MFA, mas a diferença é que a MFA pode exigir dois ou mais fatores para provar a identidade.</span><span class="sxs-lookup"><span data-stu-id="fb151-116">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="fb151-117">MFA TOTP (algoritmo de senha de uso único baseado em tempo)</span><span class="sxs-lookup"><span data-stu-id="fb151-117">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="fb151-118">O MFA usando TOTP é uma implementação com suporte Identityusando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fb151-118">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="fb151-119">Isso pode ser usado junto com qualquer aplicativo autenticador compatível, incluindo:</span><span class="sxs-lookup"><span data-stu-id="fb151-119">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="fb151-120">Aplicativo Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="fb151-120">Microsoft Authenticator App</span></span>
* <span data-ttu-id="fb151-121">Aplicativo de autenticador do Google</span><span class="sxs-lookup"><span data-stu-id="fb151-121">Google Authenticator App</span></span>

<span data-ttu-id="fb151-122">Consulte o link a seguir para obter detalhes de implementação:</span><span class="sxs-lookup"><span data-stu-id="fb151-122">See the following link for implementation details:</span></span>

[<span data-ttu-id="fb151-123">Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb151-123">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="fb151-124">MFA FIDO2 ou com senha</span><span class="sxs-lookup"><span data-stu-id="fb151-124">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="fb151-125">No momento, FIDO2 é:</span><span class="sxs-lookup"><span data-stu-id="fb151-125">FIDO2 is currently:</span></span>

* <span data-ttu-id="fb151-126">A maneira mais segura de obter MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-126">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="fb151-127">O único fluxo de MFA que protege contra ataques de phishing.</span><span class="sxs-lookup"><span data-stu-id="fb151-127">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="fb151-128">No momento, ASP.NET Core não dá suporte diretamente a FIDO2.</span><span class="sxs-lookup"><span data-stu-id="fb151-128">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="fb151-129">FIDO2 pode ser usado para fluxos MFA ou com senha.</span><span class="sxs-lookup"><span data-stu-id="fb151-129">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="fb151-130">Azure Active Directory fornece suporte para fluxos FIDO2 e sem senha.</span><span class="sxs-lookup"><span data-stu-id="fb151-130">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="fb151-131">Para obter mais informações, consulte [Opções de autenticação com senha para Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="fb151-131">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="fb151-132">SMS MFA</span><span class="sxs-lookup"><span data-stu-id="fb151-132">MFA SMS</span></span>

<span data-ttu-id="fb151-133">A MFA com o SMS aumenta a segurança em grande comparação com a autenticação de senha (fator único).</span><span class="sxs-lookup"><span data-stu-id="fb151-133">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="fb151-134">No entanto, o uso do SMS como um segundo fator não é mais recomendado.</span><span class="sxs-lookup"><span data-stu-id="fb151-134">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="fb151-135">Existem muitos vetores de ataque conhecidos para esse tipo de implementação.</span><span class="sxs-lookup"><span data-stu-id="fb151-135">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="fb151-136">Diretrizes do NIST</span><span class="sxs-lookup"><span data-stu-id="fb151-136">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-identity"></a><span data-ttu-id="fb151-137">Configurar o MFA para páginas de administração usando ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="fb151-137">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="fb151-138">A MFA pode ser forçada em usuários a acessarem páginas Identity confidenciais em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fb151-138">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="fb151-139">Isso pode ser útil para aplicativos em que existem diferentes níveis de acesso para as diferentes identidades.</span><span class="sxs-lookup"><span data-stu-id="fb151-139">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="fb151-140">Por exemplo, os usuários podem ser capazes de exibir os dados de perfil usando um logon de senha, mas um administrador precisaria usar o MFA para acessar as páginas administrativas.</span><span class="sxs-lookup"><span data-stu-id="fb151-140">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="fb151-141">Estender o logon com uma declaração MFA</span><span class="sxs-lookup"><span data-stu-id="fb151-141">Extend the login with an MFA claim</span></span>

<span data-ttu-id="fb151-142">O código de demonstração é configurado usando ASP.NET Core Identity e Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="fb151-142">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="fb151-143">O `AddIdentity` método é usado em vez `AddDefaultIdentity` de um, portanto `IUserClaimsPrincipalFactory` , uma implementação pode ser usada para adicionar declarações à identidade após um logon bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="fb151-143">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="fb151-144">A `AdditionalUserClaimsPrincipalFactory` classe adiciona a `amr` declaração às declarações do usuário somente após um logon bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="fb151-144">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="fb151-145">O valor da declaração é lido no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fb151-145">The claim's value is read from the database.</span></span> <span data-ttu-id="fb151-146">A declaração é adicionada aqui porque o usuário só deve acessar a exibição protegida mais alta se a identidade tiver feito logon com MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-146">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="fb151-147">Se a exibição do banco de dados for lida diretamente do banco de dados em vez de usar a declaração, é possível acessar a exibição sem MFA diretamente após ativar a MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-147">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="fb151-148">Como a Identity configuração do serviço foi alterada `Startup` na classe, os layouts do Identity precisam ser atualizados.</span><span class="sxs-lookup"><span data-stu-id="fb151-148">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="fb151-149">Scaffold as Identity páginas no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb151-149">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="fb151-150">Defina o layout no arquivo \* Identity/Account/Manage/_Layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="fb151-150">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="fb151-151">Também atribua o layout para todas as páginas de gerenciamento das Identity páginas:</span><span class="sxs-lookup"><span data-stu-id="fb151-151">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="fb151-152">Validar o requisito de MFA na página de administração</span><span class="sxs-lookup"><span data-stu-id="fb151-152">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="fb151-153">A página Razor administração valida que o usuário fez logon usando MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-153">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="fb151-154">No `OnGet` método, a identidade é usada para acessar as declarações do usuário.</span><span class="sxs-lookup"><span data-stu-id="fb151-154">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="fb151-155">A `amr` declaração é verificada quanto ao `mfa`valor.</span><span class="sxs-lookup"><span data-stu-id="fb151-155">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="fb151-156">Se a identidade não tiver essa declaração ou for `false`, a página será redirecionada para a página habilitar MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-156">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="fb151-157">Isso é possível porque o usuário já fez logon, mas sem MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-157">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="fb151-158">Lógica da interface do usuário para ativar ou desativar informações de logon</span><span class="sxs-lookup"><span data-stu-id="fb151-158">UI logic to toggle user login information</span></span>

<span data-ttu-id="fb151-159">Uma política de autorização foi adicionada na inicialização.</span><span class="sxs-lookup"><span data-stu-id="fb151-159">An authorization policy was added at startup.</span></span> <span data-ttu-id="fb151-160">A política requer a `amr` declaração com o valor `mfa`.</span><span class="sxs-lookup"><span data-stu-id="fb151-160">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="fb151-161">Essa política pode ser usada na `_Layout` exibição para mostrar ou ocultar o menu **admin** com o aviso:</span><span class="sxs-lookup"><span data-stu-id="fb151-161">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="fb151-162">Se a identidade tiver feito logon usando o MFA, o menu **admin** será exibido sem o aviso da dica de ferramenta.</span><span class="sxs-lookup"><span data-stu-id="fb151-162">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="fb151-163">Quando o usuário fizer logon sem MFA, o menu **admin (não habilitado)** será exibido junto com a dica de ferramenta que informa ao usuário (explicando o aviso).</span><span class="sxs-lookup"><span data-stu-id="fb151-163">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="fb151-164">Se o usuário fizer logon sem MFA, o aviso será exibido:</span><span class="sxs-lookup"><span data-stu-id="fb151-164">If the user logs in without MFA, the warning is displayed:</span></span>

![Autenticação de MFA do administrador](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="fb151-166">O usuário é redirecionado para a exibição habilitar do MFA ao clicar no link do **administrador** :</span><span class="sxs-lookup"><span data-stu-id="fb151-166">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![O administrador ativa a autenticação MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="fb151-168">Enviar requisito de entrada MFA ao servidor OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="fb151-168">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="fb151-169">O `acr_values` parâmetro pode ser usado para passar o `mfa` valor necessário do cliente para o servidor em uma solicitação de autenticação.</span><span class="sxs-lookup"><span data-stu-id="fb151-169">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="fb151-170">O `acr_values` parâmetro precisa ser manipulado no servidor do Open ID Connect para que isso funcione.</span><span class="sxs-lookup"><span data-stu-id="fb151-170">The `acr_values` parameter needs to be handled on the Open ID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="fb151-171">Cliente do OpenID Connect ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb151-171">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="fb151-172">O aplicativo Razor cliente do ASP.NET Core Pages Open ID Connect usa `AddOpenIdConnect` o método para fazer logon no servidor do Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="fb151-172">The ASP.NET Core Razor Pages Open ID Connect client app uses the `AddOpenIdConnect` method to login to the Open ID Connect server.</span></span> <span data-ttu-id="fb151-173">O `acr_values` parâmetro é definido com o `mfa` valor e enviado com a solicitação de autenticação.</span><span class="sxs-lookup"><span data-stu-id="fb151-173">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="fb151-174">O `OpenIdConnectEvents` é usado para adicionar isso.</span><span class="sxs-lookup"><span data-stu-id="fb151-174">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="fb151-175">Para obter `acr_values` os valores de parâmetro recomendados, consulte [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="fb151-175">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-identity"></a><span data-ttu-id="fb151-176">Exemplo de servidor OpenID Connect IdentityServer 4 com ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="fb151-176">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="fb151-177">No servidor OpenID Connect, que é implementado usando ASP.NET Core Identity com exibições MVC, uma nova exibição chamada *ErrorEnable2FA. cshtml* é criada.</span><span class="sxs-lookup"><span data-stu-id="fb151-177">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="fb151-178">A exibição:</span><span class="sxs-lookup"><span data-stu-id="fb151-178">The view:</span></span>

* <span data-ttu-id="fb151-179">Exibe se o Identity é proveniente de um aplicativo que requer MFA, mas o usuário não ativou Identityisso no.</span><span class="sxs-lookup"><span data-stu-id="fb151-179">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="fb151-180">Informa ao usuário e adiciona um link para ativar isso.</span><span class="sxs-lookup"><span data-stu-id="fb151-180">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="fb151-181">No `Login` método, a implementação `IIdentityServerInteractionService` `_interaction` da interface é usada para acessar os parâmetros de solicitação do Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="fb151-181">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the Open ID Connect request parameters.</span></span> <span data-ttu-id="fb151-182">O `acr_values` parâmetro é acessado `AcrValues` usando a propriedade.</span><span class="sxs-lookup"><span data-stu-id="fb151-182">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="fb151-183">Como o cliente enviou isso com `mfa` set, ele pode ser verificado.</span><span class="sxs-lookup"><span data-stu-id="fb151-183">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="fb151-184">Se a MFA for necessária e o usuário no ASP.NET Core Identity tiver a MFA habilitada, o logon continuará.</span><span class="sxs-lookup"><span data-stu-id="fb151-184">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="fb151-185">Quando o usuário não tem a MFA habilitada, o usuário é redirecionado para a exibição personalizada *ErrorEnable2FA. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb151-185">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="fb151-186">Em seguida Identity , ASP.NET Core assina o usuário.</span><span class="sxs-lookup"><span data-stu-id="fb151-186">Then ASP.NET Core Identity signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="fb151-187">O `ExternalLoginCallback` método funciona como o logon Identity local.</span><span class="sxs-lookup"><span data-stu-id="fb151-187">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="fb151-188">A `AcrValues` propriedade é verificada quanto `mfa` ao valor.</span><span class="sxs-lookup"><span data-stu-id="fb151-188">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="fb151-189">Se o `mfa` valor estiver presente, a MFA será forçada antes que o logon seja concluído (por exemplo, Redirecionado para a `ErrorEnable2FA` exibição).</span><span class="sxs-lookup"><span data-stu-id="fb151-189">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="fb151-190">Se o usuário já estiver conectado, o aplicativo cliente:</span><span class="sxs-lookup"><span data-stu-id="fb151-190">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="fb151-191">Ainda valida a `amr` declaração.</span><span class="sxs-lookup"><span data-stu-id="fb151-191">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="fb151-192">Pode configurar o MFA com um link para a exibição ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="fb151-192">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="fb151-194">Forçar ASP.NET Core cliente do OpenID Connect a exigir MFA</span><span class="sxs-lookup"><span data-stu-id="fb151-194">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="fb151-195">Este exemplo mostra como um aplicativo Razor de página ASP.NET Core, que usa o OpenID Connect para entrar, pode exigir que os usuários tenham se autenticado usando o MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-195">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="fb151-196">Para validar o requisito de MFA, `IAuthorizationRequirement` um requisito é criado.</span><span class="sxs-lookup"><span data-stu-id="fb151-196">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="fb151-197">Isso será adicionado às páginas usando uma política que requer MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-197">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="fb151-198">Um `AuthorizationHandler` é implementado, que usará `amr` a declaração e verificará o `mfa`valor.</span><span class="sxs-lookup"><span data-stu-id="fb151-198">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="fb151-199">O `amr` é retornado no `id_token` de uma autenticação bem-sucedida e pode ter muitos valores diferentes, conforme definido na especificação de [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="fb151-199">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="fb151-200">O valor retornado depende de como a identidade é autenticada e na implementação do servidor do Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="fb151-200">The returned value depends on how the identity authenticated and on the Open ID Connect server implementation.</span></span>

<span data-ttu-id="fb151-201">O `AuthorizationHandler` usa o `RequireMfa` requisito e valida a `amr` declaração.</span><span class="sxs-lookup"><span data-stu-id="fb151-201">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="fb151-202">O servidor OpenID Connect pode ser implementado usando o IdentityServer4 com Identityo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fb151-202">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="fb151-203">Quando um usuário faz logon usando TOTP, a `amr` declaração é retornada com um valor de MFA.</span><span class="sxs-lookup"><span data-stu-id="fb151-203">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="fb151-204">Se estiver usando uma implementação de servidor OpenID Connect diferente ou um tipo de MFA `amr` diferente, a declaração será ou poderá ter um valor diferente.</span><span class="sxs-lookup"><span data-stu-id="fb151-204">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="fb151-205">O código deve ser estendido para aceitar isso também.</span><span class="sxs-lookup"><span data-stu-id="fb151-205">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="fb151-206">No `Startup.ConfigureServices` método, o `AddOpenIdConnect` método é usado como o esquema de desafio padrão.</span><span class="sxs-lookup"><span data-stu-id="fb151-206">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="fb151-207">O manipulador de autorização, que é usado para verificar `amr` a declaração, é adicionado à inversão do contêiner de controle.</span><span class="sxs-lookup"><span data-stu-id="fb151-207">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="fb151-208">Em seguida, uma política é criada, `RequireMfa` o que adiciona o requisito.</span><span class="sxs-lookup"><span data-stu-id="fb151-208">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

<span data-ttu-id="fb151-209">Essa política é usada na Razor página conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="fb151-209">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="fb151-210">A política também pode ser adicionada globalmente para todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fb151-210">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="fb151-211">Se o usuário se autenticar sem MFA, `amr` a declaração provavelmente terá um `pwd` valor.</span><span class="sxs-lookup"><span data-stu-id="fb151-211">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="fb151-212">A solicitação não será autorizada a acessar a página.</span><span class="sxs-lookup"><span data-stu-id="fb151-212">The request won't be authorized to access the page.</span></span> <span data-ttu-id="fb151-213">Usando os valores padrão, o usuário será redirecionado para a página *Account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="fb151-213">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="fb151-214">Esse comportamento pode ser alterado ou você pode implementar sua própria lógica personalizada aqui.</span><span class="sxs-lookup"><span data-stu-id="fb151-214">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="fb151-215">Neste exemplo, um link é adicionado para que o usuário válido possa configurar o MFA para sua conta.</span><span class="sxs-lookup"><span data-stu-id="fb151-215">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="fb151-216">Agora, somente os usuários que se autenticam com o MFA podem acessar a página ou o site.</span><span class="sxs-lookup"><span data-stu-id="fb151-216">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="fb151-217">Se tipos de MFA diferentes forem usados ou se 2FA estiver ok, `amr` a declaração terá valores diferentes e precisará ser processada corretamente.</span><span class="sxs-lookup"><span data-stu-id="fb151-217">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="fb151-218">Os diferentes servidores do Open ID Connect também retornam valores diferentes para essa declaração e podem não seguir a especificação de [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="fb151-218">Different Open ID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="fb151-219">Ao fazer logon sem MFA (por exemplo, usando apenas uma senha):</span><span class="sxs-lookup"><span data-stu-id="fb151-219">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="fb151-220">O `amr` tem o `pwd` valor:</span><span class="sxs-lookup"><span data-stu-id="fb151-220">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02. png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="fb151-222">Acesso negado:</span><span class="sxs-lookup"><span data-stu-id="fb151-222">Access is denied:</span></span>

    ![require_mfa_oidc_03. png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="fb151-224">Como alternativa, faça logon usando OTP com Identity:</span><span class="sxs-lookup"><span data-stu-id="fb151-224">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01. png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="fb151-226">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fb151-226">Additional resources</span></span>

* [<span data-ttu-id="fb151-227">Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb151-227">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="fb151-228">Opções de autenticação com senha para Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="fb151-228">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="fb151-229">FIDO2 biblioteca .NET para FIDO2/webauthn atestado e asserção usando o .NET</span><span class="sxs-lookup"><span data-stu-id="fb151-229">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="fb151-230">Webauthn awesome</span><span class="sxs-lookup"><span data-stu-id="fb151-230">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
