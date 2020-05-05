---
title: Usar autenticação de cookie sem ASP.NET CoreIdentity
author: rick-anderson
description: Saiba como usar a autenticação de cookie sem IdentityASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: c179b3657ad4cbda960c2afe685a63f3266a7402
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773838"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="d1274-103">Usar autenticação de cookie sem identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1274-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="d1274-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1274-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1274-105">A identidade de ASP.NET Core é um provedor de autenticação completo e repleto de recursos para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="d1274-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="d1274-106">No entanto, um provedor de autenticação baseado em cookie sem ASP.NET Core identidade pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="d1274-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="d1274-107">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="d1274-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="d1274-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1274-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1274-109">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="d1274-110">Use o **Email** endereço `maria.rodriguez@contoso.com` de email e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="d1274-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="d1274-111">O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="d1274-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="d1274-112">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d1274-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="d1274-113">Configuração</span><span class="sxs-lookup"><span data-stu-id="d1274-113">Configuration</span></span>

<span data-ttu-id="d1274-114">Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="d1274-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="d1274-115">No `Startup.ConfigureServices` método, crie os serviços de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> métodos <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> e:</span><span class="sxs-lookup"><span data-stu-id="d1274-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d1274-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="d1274-117">`AuthenticationScheme`é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="d1274-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="d1274-118">A definição `AuthenticationScheme` de para [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1274-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="d1274-119">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1274-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="d1274-120">O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="d1274-121">Quando um esquema de autenticação de cookie não <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>é fornecido para `CookieAuthenticationDefaults.AuthenticationScheme` , ele usa ("cookies").</span><span class="sxs-lookup"><span data-stu-id="d1274-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="d1274-122">A propriedade do <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> cookie de autenticação é definida `true` como por padrão.</span><span class="sxs-lookup"><span data-stu-id="d1274-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="d1274-123">Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="d1274-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="d1274-124">Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="d1274-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="d1274-125">No `Startup.Configure`, chame `UseAuthentication` e `UseAuthorization` para definir a `HttpContext.User` Propriedade e executar o middleware de autorização para solicitações.</span><span class="sxs-lookup"><span data-stu-id="d1274-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="d1274-126">Chame os `UseAuthentication` métodos `UseAuthorization` e antes de `UseEndpoints`chamar:</span><span class="sxs-lookup"><span data-stu-id="d1274-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="d1274-127">A <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1274-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="d1274-128">Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d1274-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="d1274-129">Middleware de política de cookie</span><span class="sxs-lookup"><span data-stu-id="d1274-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="d1274-130">O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie.</span><span class="sxs-lookup"><span data-stu-id="d1274-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="d1274-131">A adição do middleware ao pipeline de processamento do aplicativo é a&mdash;diferenciação de ordem que afeta apenas os componentes de downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="d1274-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="d1274-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="d1274-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="d1274-133">O valor <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="d1274-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="d1274-134">Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="d1274-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="d1274-135">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="d1274-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="d1274-136">A configuração de middleware de política de `MinimumSameSitePolicy` cookie para pode afetar a `Cookie.SameSite` configuração `CookieAuthenticationOptions` de em configurações de acordo com a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1274-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="d1274-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="d1274-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="d1274-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="d1274-138">Cookie.SameSite</span></span> | <span data-ttu-id="d1274-139">Cookie resultante. configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="d1274-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="d1274-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-140">SameSiteMode.None</span></span>     | <span data-ttu-id="d1274-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-141">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1274-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-144">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d1274-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="d1274-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-148">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1274-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d1274-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="d1274-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-155">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1274-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="d1274-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="d1274-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="d1274-161">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="d1274-161">Create an authentication cookie</span></span>

<span data-ttu-id="d1274-162">Para criar um cookie que contém informações do usuário, <xref:System.Security.Claims.ClaimsPrincipal>Construa um.</span><span class="sxs-lookup"><span data-stu-id="d1274-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="d1274-163">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="d1274-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="d1274-164">Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer s <xref:System.Security.Claims.Claim>necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="d1274-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="d1274-165">`SignInAsync`Cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="d1274-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="d1274-166">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="d1274-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="d1274-167">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="d1274-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="d1274-168">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="d1274-169">Sair</span><span class="sxs-lookup"><span data-stu-id="d1274-169">Sign out</span></span>

<span data-ttu-id="d1274-170">Para sair do usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="d1274-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="d1274-171">Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1274-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="d1274-172">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="d1274-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="d1274-173">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="d1274-173">React to back-end changes</span></span>

<span data-ttu-id="d1274-174">Depois que um cookie é criado, o cookie é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="d1274-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="d1274-175">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="d1274-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="d1274-176">O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1274-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="d1274-177">O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.</span><span class="sxs-lookup"><span data-stu-id="d1274-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="d1274-178">O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="d1274-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="d1274-179">A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="d1274-180">Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="d1274-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="d1274-181">Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="d1274-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="d1274-182">No aplicativo de exemplo, o banco de dados é `IUserRepository` implementado no e `LastChanged` armazena um valor.</span><span class="sxs-lookup"><span data-stu-id="d1274-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="d1274-183">Quando um usuário é atualizado no banco de dados, `LastChanged` o valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="d1274-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="d1274-184">Para invalidar um cookie quando o banco de dados é alterado com base no `LastChanged` valor, crie o cookie com uma `LastChanged` declaração contendo o valor `LastChanged` atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="d1274-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="d1274-185">Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="d1274-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="d1274-186">Veja a seguir um exemplo de implementação `CookieAuthenticationEvents`de:</span><span class="sxs-lookup"><span data-stu-id="d1274-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="d1274-187">Registre a instância de eventos durante o `Startup.ConfigureServices` registro do serviço de cookie no método.</span><span class="sxs-lookup"><span data-stu-id="d1274-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d1274-188">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para `CustomCookieAuthenticationEvents` sua classe:</span><span class="sxs-lookup"><span data-stu-id="d1274-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="d1274-189">Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="d1274-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="d1274-190">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` Propriedade como. `true`</span><span class="sxs-lookup"><span data-stu-id="d1274-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d1274-191">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="d1274-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="d1274-192">A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="d1274-193">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="d1274-193">Persistent cookies</span></span>

<span data-ttu-id="d1274-194">Talvez você queira que o cookie persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1274-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="d1274-195">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1274-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="d1274-196">O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1274-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="d1274-197">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="d1274-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="d1274-198">Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="d1274-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="d1274-199">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="d1274-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="d1274-200">Expiração de cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="d1274-200">Absolute cookie expiration</span></span>

<span data-ttu-id="d1274-201">Um tempo de expiração absoluto pode ser <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>definido com.</span><span class="sxs-lookup"><span data-stu-id="d1274-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="d1274-202">Para criar um cookie persistente, `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="d1274-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="d1274-203">Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="d1274-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="d1274-204">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, se definido.</span><span class="sxs-lookup"><span data-stu-id="d1274-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="d1274-205">O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="d1274-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="d1274-206">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="d1274-206">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1274-207">A identidade de ASP.NET Core é um provedor de autenticação completo e repleto de recursos para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="d1274-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="d1274-208">No entanto, um provedor de autenticação de autenticação baseada em cookie sem ASP.NET Core identidade pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="d1274-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="d1274-209">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="d1274-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="d1274-210">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1274-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1274-211">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="d1274-212">Use o **Email** endereço `maria.rodriguez@contoso.com` de email e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="d1274-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="d1274-213">O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="d1274-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="d1274-214">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d1274-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="d1274-215">Configuração</span><span class="sxs-lookup"><span data-stu-id="d1274-215">Configuration</span></span>

<span data-ttu-id="d1274-216">Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="d1274-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="d1274-217">No `Startup.ConfigureServices` método, crie o serviço de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> métodos <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> e:</span><span class="sxs-lookup"><span data-stu-id="d1274-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d1274-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="d1274-219">`AuthenticationScheme`é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="d1274-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="d1274-220">A definição `AuthenticationScheme` de para [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1274-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="d1274-221">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1274-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="d1274-222">O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="d1274-223">Quando um esquema de autenticação de cookie não <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>é fornecido para `CookieAuthenticationDefaults.AuthenticationScheme` , ele usa ("cookies").</span><span class="sxs-lookup"><span data-stu-id="d1274-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="d1274-224">A propriedade do <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> cookie de autenticação é definida `true` como por padrão.</span><span class="sxs-lookup"><span data-stu-id="d1274-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="d1274-225">Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="d1274-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="d1274-226">Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="d1274-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="d1274-227">No `Startup.Configure` método, chame o `UseAuthentication` método para invocar o middleware de autenticação que define a `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d1274-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="d1274-228">Chame o `UseAuthentication` método antes de `UseMvcWithDefaultRoute` chamar `UseMvc`ou:</span><span class="sxs-lookup"><span data-stu-id="d1274-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="d1274-229">A <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1274-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="d1274-230">Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d1274-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="d1274-231">Middleware de política de cookie</span><span class="sxs-lookup"><span data-stu-id="d1274-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="d1274-232">O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie.</span><span class="sxs-lookup"><span data-stu-id="d1274-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="d1274-233">A adição do middleware ao pipeline de processamento do aplicativo é a&mdash;diferenciação de ordem que afeta apenas os componentes de downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="d1274-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="d1274-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="d1274-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="d1274-235">O valor <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="d1274-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="d1274-236">Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="d1274-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="d1274-237">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="d1274-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="d1274-238">A configuração de middleware de política de `MinimumSameSitePolicy` cookie para pode afetar a `Cookie.SameSite` configuração `CookieAuthenticationOptions` de em configurações de acordo com a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1274-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="d1274-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="d1274-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="d1274-240">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="d1274-240">Cookie.SameSite</span></span> | <span data-ttu-id="d1274-241">Cookie resultante. configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="d1274-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="d1274-242">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-242">SameSiteMode.None</span></span>     | <span data-ttu-id="d1274-243">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-243">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-244">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-245">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1274-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-246">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d1274-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="d1274-250">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-250">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-252">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1274-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d1274-256">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="d1274-257">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="d1274-257">SameSiteMode.None</span></span><br><span data-ttu-id="d1274-258">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="d1274-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1274-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1274-260">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="d1274-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="d1274-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="d1274-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="d1274-263">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="d1274-263">Create an authentication cookie</span></span>

<span data-ttu-id="d1274-264">Para criar um cookie que contém informações do usuário, <xref:System.Security.Claims.ClaimsPrincipal>Construa um.</span><span class="sxs-lookup"><span data-stu-id="d1274-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="d1274-265">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="d1274-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="d1274-266">Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer s <xref:System.Security.Claims.Claim>necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="d1274-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="d1274-267">`SignInAsync`Cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="d1274-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="d1274-268">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="d1274-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="d1274-269">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="d1274-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="d1274-270">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="d1274-271">Sair</span><span class="sxs-lookup"><span data-stu-id="d1274-271">Sign out</span></span>

<span data-ttu-id="d1274-272">Para sair do usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="d1274-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="d1274-273">Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1274-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="d1274-274">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="d1274-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="d1274-275">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="d1274-275">React to back-end changes</span></span>

<span data-ttu-id="d1274-276">Depois que um cookie é criado, o cookie é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="d1274-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="d1274-277">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="d1274-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="d1274-278">O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1274-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="d1274-279">O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.</span><span class="sxs-lookup"><span data-stu-id="d1274-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="d1274-280">O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="d1274-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="d1274-281">A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="d1274-282">Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="d1274-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="d1274-283">Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="d1274-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="d1274-284">No aplicativo de exemplo, o banco de dados é `IUserRepository` implementado no e `LastChanged` armazena um valor.</span><span class="sxs-lookup"><span data-stu-id="d1274-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="d1274-285">Quando um usuário é atualizado no banco de dados, `LastChanged` o valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="d1274-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="d1274-286">Para invalidar um cookie quando o banco de dados é alterado com base no `LastChanged` valor, crie o cookie com uma `LastChanged` declaração contendo o valor `LastChanged` atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="d1274-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="d1274-287">Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="d1274-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="d1274-288">Veja a seguir um exemplo de implementação `CookieAuthenticationEvents`de:</span><span class="sxs-lookup"><span data-stu-id="d1274-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="d1274-289">Registre a instância de eventos durante o `Startup.ConfigureServices` registro do serviço de cookie no método.</span><span class="sxs-lookup"><span data-stu-id="d1274-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d1274-290">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para `CustomCookieAuthenticationEvents` sua classe:</span><span class="sxs-lookup"><span data-stu-id="d1274-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="d1274-291">Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="d1274-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="d1274-292">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` Propriedade como. `true`</span><span class="sxs-lookup"><span data-stu-id="d1274-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d1274-293">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="d1274-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="d1274-294">A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1274-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="d1274-295">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="d1274-295">Persistent cookies</span></span>

<span data-ttu-id="d1274-296">Talvez você queira que o cookie persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1274-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="d1274-297">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1274-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="d1274-298">O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1274-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="d1274-299">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="d1274-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="d1274-300">Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="d1274-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="d1274-301">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="d1274-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="d1274-302">Expiração de cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="d1274-302">Absolute cookie expiration</span></span>

<span data-ttu-id="d1274-303">Um tempo de expiração absoluto pode ser <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>definido com.</span><span class="sxs-lookup"><span data-stu-id="d1274-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="d1274-304">Para criar um cookie persistente, `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="d1274-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="d1274-305">Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="d1274-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="d1274-306">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se definido.</span><span class="sxs-lookup"><span data-stu-id="d1274-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="d1274-307">O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="d1274-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="d1274-308">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="d1274-308">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d1274-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d1274-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="d1274-310">Verificações de função baseadas em política</span><span class="sxs-lookup"><span data-stu-id="d1274-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
