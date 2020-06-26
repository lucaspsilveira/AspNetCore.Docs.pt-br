---
title: Usar autenticação de cookie sem ASP.NET CoreIdentity
author: rick-anderson
description: Saiba como usar a autenticação de cookie sem ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 401d03352b8c2c040202716bdddf484e3b778f52
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408819"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="645e7-103">Usar autenticação de cookie sem ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="645e7-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="645e7-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="645e7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="645e7-105">IdentityO ASP.NET Core é um provedor completo de autenticação completa para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="645e7-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="645e7-106">No entanto, um provedor de autenticação baseado em cookie sem ASP.NET Core Identity pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="645e7-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="645e7-107">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="645e7-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="645e7-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="645e7-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="645e7-109">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="645e7-110">Use o endereço de **email** `maria.rodriguez@contoso.com` e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="645e7-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="645e7-111">O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="645e7-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="645e7-112">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="645e7-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="645e7-113">Configuração</span><span class="sxs-lookup"><span data-stu-id="645e7-113">Configuration</span></span>

<span data-ttu-id="645e7-114">Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="645e7-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="645e7-115">No `Startup.ConfigureServices` método, crie os serviços de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos e:</span><span class="sxs-lookup"><span data-stu-id="645e7-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="645e7-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="645e7-117">`AuthenticationScheme`é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="645e7-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="645e7-118">A definição de `AuthenticationScheme` para [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="645e7-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="645e7-119">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="645e7-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="645e7-120">O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="645e7-121">Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , ele usa `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").</span><span class="sxs-lookup"><span data-stu-id="645e7-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="645e7-122">A propriedade do cookie de autenticação <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> é definida como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="645e7-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="645e7-123">Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="645e7-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="645e7-124">Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="645e7-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="645e7-125">No `Startup.Configure` , chame `UseAuthentication` e `UseAuthorization` para definir a `HttpContext.User` propriedade e executar o middleware de autorização para solicitações.</span><span class="sxs-lookup"><span data-stu-id="645e7-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="645e7-126">Chame os `UseAuthentication` `UseAuthorization` métodos e antes de chamar `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="645e7-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="645e7-127">A <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="645e7-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="645e7-128">Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="645e7-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="645e7-129">Middleware de política de cookie</span><span class="sxs-lookup"><span data-stu-id="645e7-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="645e7-130">O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie.</span><span class="sxs-lookup"><span data-stu-id="645e7-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="645e7-131">A adição do middleware ao pipeline de processamento do aplicativo é a diferenciação de ordem &mdash; que afeta apenas os componentes de downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="645e7-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="645e7-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="645e7-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="645e7-133">O <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="645e7-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="645e7-134">Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict` , defina o `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="645e7-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="645e7-135">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="645e7-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="645e7-136">A configuração de middleware de política de cookie para `MinimumSameSitePolicy` pode afetar a configuração de `Cookie.SameSite` em `CookieAuthenticationOptions` configurações de acordo com a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="645e7-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="645e7-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="645e7-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="645e7-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="645e7-138">Cookie.SameSite</span></span> | <span data-ttu-id="645e7-139">Cookie resultante. configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="645e7-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="645e7-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-140">SameSiteMode.None</span></span>     | <span data-ttu-id="645e7-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-141">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="645e7-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-144">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="645e7-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="645e7-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-148">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="645e7-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="645e7-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="645e7-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-155">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="645e7-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="645e7-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="645e7-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="645e7-161">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="645e7-161">Create an authentication cookie</span></span>

<span data-ttu-id="645e7-162">Para criar um cookie que contém informações do usuário, construa um <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="645e7-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="645e7-163">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="645e7-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="645e7-164">Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer <xref:System.Security.Claims.Claim> s necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="645e7-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="645e7-165">`SignInAsync`Cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="645e7-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="645e7-166">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="645e7-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="645e7-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>é usado apenas em alguns caminhos específicos por padrão, por exemplo, o caminho de logon e os caminhos de logout.</span><span class="sxs-lookup"><span data-stu-id="645e7-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="645e7-168">Para obter mais informações, consulte a [origem do CookieAuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="645e7-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="645e7-169">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="645e7-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="645e7-170">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="645e7-171">Sair</span><span class="sxs-lookup"><span data-stu-id="645e7-171">Sign out</span></span>

<span data-ttu-id="645e7-172">Para sair do usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="645e7-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="645e7-173">Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="645e7-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="645e7-174">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="645e7-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="645e7-175">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="645e7-175">React to back-end changes</span></span>

<span data-ttu-id="645e7-176">Depois que um cookie é criado, o cookie é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="645e7-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="645e7-177">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="645e7-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="645e7-178">O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="645e7-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="645e7-179">O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.</span><span class="sxs-lookup"><span data-stu-id="645e7-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="645e7-180">O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="645e7-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="645e7-181">A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="645e7-182">Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="645e7-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="645e7-183">Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="645e7-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="645e7-184">No aplicativo de exemplo, o banco de dados é implementado no `IUserRepository` e armazena um `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="645e7-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="645e7-185">Quando um usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="645e7-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="645e7-186">Para invalidar um cookie quando o banco de dados é alterado com base no `LastChanged` valor, crie o cookie com uma `LastChanged` declaração contendo o `LastChanged` valor atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="645e7-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="645e7-187">Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="645e7-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="645e7-188">Veja a seguir um exemplo de implementação de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="645e7-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="645e7-189">Registre a instância de eventos durante o registro do serviço de cookie no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="645e7-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="645e7-190">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para sua `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="645e7-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="645e7-191">Considere uma situação em que o nome do usuário é atualizado &mdash; uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="645e7-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="645e7-192">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` propriedade como `true` .</span><span class="sxs-lookup"><span data-stu-id="645e7-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="645e7-193">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="645e7-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="645e7-194">A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="645e7-195">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="645e7-195">Persistent cookies</span></span>

<span data-ttu-id="645e7-196">Talvez você queira que o cookie persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="645e7-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="645e7-197">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="645e7-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="645e7-198">O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="645e7-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="645e7-199">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="645e7-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="645e7-200">Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="645e7-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="645e7-201">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="645e7-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="645e7-202">Expiração de cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="645e7-202">Absolute cookie expiration</span></span>

<span data-ttu-id="645e7-203">Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="645e7-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="645e7-204">Para criar um cookie persistente, `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="645e7-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="645e7-205">Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="645e7-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="645e7-206">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , se definido.</span><span class="sxs-lookup"><span data-stu-id="645e7-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="645e7-207">O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="645e7-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="645e7-208">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="645e7-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="645e7-209">IdentityO ASP.NET Core é um provedor completo de autenticação completa para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="645e7-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="645e7-210">No entanto, um provedor de autenticação de autenticação baseada em cookie sem ASP.NET Core Identity pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="645e7-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="645e7-211">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="645e7-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="645e7-212">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="645e7-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="645e7-213">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="645e7-214">Use o endereço de **email** `maria.rodriguez@contoso.com` e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="645e7-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="645e7-215">O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="645e7-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="645e7-216">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="645e7-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="645e7-217">Configuração</span><span class="sxs-lookup"><span data-stu-id="645e7-217">Configuration</span></span>

<span data-ttu-id="645e7-218">Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="645e7-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="645e7-219">No `Startup.ConfigureServices` método, crie o serviço de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos e:</span><span class="sxs-lookup"><span data-stu-id="645e7-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="645e7-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="645e7-221">`AuthenticationScheme`é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="645e7-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="645e7-222">A definição de `AuthenticationScheme` para [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="645e7-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="645e7-223">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="645e7-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="645e7-224">O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="645e7-225">Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , ele usa `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").</span><span class="sxs-lookup"><span data-stu-id="645e7-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="645e7-226">A propriedade do cookie de autenticação <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> é definida como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="645e7-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="645e7-227">Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="645e7-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="645e7-228">Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="645e7-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="645e7-229">No `Startup.Configure` método, chame o `UseAuthentication` método para invocar o middleware de autenticação que define a `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="645e7-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="645e7-230">Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="645e7-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="645e7-231">A <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="645e7-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="645e7-232">Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="645e7-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="645e7-233">Middleware de política de cookie</span><span class="sxs-lookup"><span data-stu-id="645e7-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="645e7-234">O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie.</span><span class="sxs-lookup"><span data-stu-id="645e7-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="645e7-235">A adição do middleware ao pipeline de processamento do aplicativo é a diferenciação de ordem &mdash; que afeta apenas os componentes de downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="645e7-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="645e7-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="645e7-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="645e7-237">O <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="645e7-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="645e7-238">Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict` , defina o `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="645e7-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="645e7-239">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="645e7-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="645e7-240">A configuração de middleware de política de cookie para `MinimumSameSitePolicy` pode afetar a configuração de `Cookie.SameSite` em `CookieAuthenticationOptions` configurações de acordo com a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="645e7-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="645e7-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="645e7-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="645e7-242">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="645e7-242">Cookie.SameSite</span></span> | <span data-ttu-id="645e7-243">Cookie resultante. configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="645e7-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="645e7-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-244">SameSiteMode.None</span></span>     | <span data-ttu-id="645e7-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-245">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-246">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-247">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="645e7-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-248">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-250">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="645e7-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="645e7-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-252">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="645e7-255">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-257">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="645e7-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="645e7-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="645e7-259">SameSiteMode.None</span></span><br><span data-ttu-id="645e7-260">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="645e7-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="645e7-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="645e7-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="645e7-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="645e7-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="645e7-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="645e7-265">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="645e7-265">Create an authentication cookie</span></span>

<span data-ttu-id="645e7-266">Para criar um cookie que contém informações do usuário, construa um <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="645e7-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="645e7-267">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="645e7-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="645e7-268">Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer <xref:System.Security.Claims.Claim> s necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="645e7-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="645e7-269">`SignInAsync`Cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="645e7-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="645e7-270">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="645e7-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="645e7-271">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="645e7-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="645e7-272">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="645e7-273">Sair</span><span class="sxs-lookup"><span data-stu-id="645e7-273">Sign out</span></span>

<span data-ttu-id="645e7-274">Para sair do usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="645e7-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="645e7-275">Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="645e7-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="645e7-276">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="645e7-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="645e7-277">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="645e7-277">React to back-end changes</span></span>

<span data-ttu-id="645e7-278">Depois que um cookie é criado, o cookie é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="645e7-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="645e7-279">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="645e7-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="645e7-280">O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="645e7-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="645e7-281">O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.</span><span class="sxs-lookup"><span data-stu-id="645e7-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="645e7-282">O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="645e7-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="645e7-283">A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="645e7-284">Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="645e7-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="645e7-285">Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="645e7-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="645e7-286">No aplicativo de exemplo, o banco de dados é implementado no `IUserRepository` e armazena um `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="645e7-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="645e7-287">Quando um usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="645e7-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="645e7-288">Para invalidar um cookie quando o banco de dados é alterado com base no `LastChanged` valor, crie o cookie com uma `LastChanged` declaração contendo o `LastChanged` valor atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="645e7-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="645e7-289">Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="645e7-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="645e7-290">Veja a seguir um exemplo de implementação de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="645e7-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="645e7-291">Registre a instância de eventos durante o registro do serviço de cookie no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="645e7-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="645e7-292">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para sua `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="645e7-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="645e7-293">Considere uma situação em que o nome do usuário é atualizado &mdash; uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="645e7-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="645e7-294">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` propriedade como `true` .</span><span class="sxs-lookup"><span data-stu-id="645e7-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="645e7-295">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="645e7-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="645e7-296">A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="645e7-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="645e7-297">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="645e7-297">Persistent cookies</span></span>

<span data-ttu-id="645e7-298">Talvez você queira que o cookie persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="645e7-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="645e7-299">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="645e7-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="645e7-300">O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="645e7-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="645e7-301">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="645e7-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="645e7-302">Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="645e7-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="645e7-303">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="645e7-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="645e7-304">Expiração de cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="645e7-304">Absolute cookie expiration</span></span>

<span data-ttu-id="645e7-305">Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="645e7-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="645e7-306">Para criar um cookie persistente, `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="645e7-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="645e7-307">Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="645e7-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="645e7-308">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , se definido.</span><span class="sxs-lookup"><span data-stu-id="645e7-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="645e7-309">O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="645e7-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="645e7-310">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="645e7-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="645e7-311">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="645e7-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="645e7-312">Verificações de função baseadas em política</span><span class="sxs-lookup"><span data-stu-id="645e7-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
