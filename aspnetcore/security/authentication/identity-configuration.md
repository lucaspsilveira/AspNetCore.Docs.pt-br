---
title: Configurar ASP.NET CoreIdentity
author: AdrienTorris
description: Entenda ASP.NET Core Identity valores padrão e saiba como configurar Identity Propriedades para usar valores personalizados.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408962"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="50acb-103">Configurar ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="50acb-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="50acb-104">ASP.NET Core Identity usa valores padrão para configurações como política de senha, bloqueio e configuração de cookie.</span><span class="sxs-lookup"><span data-stu-id="50acb-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="50acb-105">Essas configurações podem ser substituídas na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="50acb-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a>Identity<span data-ttu-id="50acb-106">Opções</span><span class="sxs-lookup"><span data-stu-id="50acb-106"> options</span></span>

<span data-ttu-id="50acb-107">A classe [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) representa as opções que podem ser usadas para configurar o Identity sistema.</span><span class="sxs-lookup"><span data-stu-id="50acb-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="50acb-108">`IdentityOptions`deve ser definido **após** a chamada `AddIdentity` ou `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="50acb-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="50acb-109">DeclaraçõesIdentity</span><span class="sxs-lookup"><span data-stu-id="50acb-109">Claims Identity</span></span>

<span data-ttu-id="50acb-110">[Identityoptions. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) especifica o [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) com as propriedades mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="50acb-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="50acb-111">Propriedade</span><span class="sxs-lookup"><span data-stu-id="50acb-111">Property</span></span> | <span data-ttu-id="50acb-112">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-112">Description</span></span> | <span data-ttu-id="50acb-113">Padrão</span><span class="sxs-lookup"><span data-stu-id="50acb-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="50acb-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="50acb-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="50acb-115">Obtém ou define o tipo de declaração usado para uma declaração de função.</span><span class="sxs-lookup"><span data-stu-id="50acb-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="50acb-116">ClaimTypes. Role</span><span class="sxs-lookup"><span data-stu-id="50acb-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="50acb-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="50acb-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="50acb-118">Obtém ou define o tipo de declaração usado para a declaração de carimbo de segurança.</span><span class="sxs-lookup"><span data-stu-id="50acb-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="50acb-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="50acb-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="50acb-120">Obtém ou define o tipo de declaração usado para a declaração de identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="50acb-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="50acb-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="50acb-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="50acb-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="50acb-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="50acb-123">Obtém ou define o tipo de declaração usado para a declaração de nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="50acb-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="50acb-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="50acb-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="50acb-125">Bloquear</span><span class="sxs-lookup"><span data-stu-id="50acb-125">Lockout</span></span>

<span data-ttu-id="50acb-126">O bloqueio é definido no método [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :</span><span class="sxs-lookup"><span data-stu-id="50acb-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="50acb-127">O código anterior se baseia no `Login` Identity modelo.</span><span class="sxs-lookup"><span data-stu-id="50acb-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="50acb-128">As opções de bloqueio são definidas em `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50acb-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="50acb-129">O código anterior define o [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com valores padrão.</span><span class="sxs-lookup"><span data-stu-id="50acb-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="50acb-130">Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio.</span><span class="sxs-lookup"><span data-stu-id="50acb-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="50acb-131">[Identityoptions. Lock](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) especifica o [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="50acb-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="50acb-132">Propriedade</span><span class="sxs-lookup"><span data-stu-id="50acb-132">Property</span></span> | <span data-ttu-id="50acb-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-133">Description</span></span> | <span data-ttu-id="50acb-134">Padrão</span><span class="sxs-lookup"><span data-stu-id="50acb-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="50acb-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="50acb-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="50acb-136">Determina se um novo usuário pode ser bloqueado.</span><span class="sxs-lookup"><span data-stu-id="50acb-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="50acb-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="50acb-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="50acb-138">A quantidade de tempo que um usuário é bloqueado quando ocorre um bloqueio.</span><span class="sxs-lookup"><span data-stu-id="50acb-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="50acb-139">5 minutos</span><span class="sxs-lookup"><span data-stu-id="50acb-139">5 minutes</span></span> |
| [<span data-ttu-id="50acb-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="50acb-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="50acb-141">O número de tentativas de acesso com falha até que um usuário seja bloqueado, se o bloqueio estiver habilitado.</span><span class="sxs-lookup"><span data-stu-id="50acb-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="50acb-142">5</span><span class="sxs-lookup"><span data-stu-id="50acb-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="50acb-143">Senha</span><span class="sxs-lookup"><span data-stu-id="50acb-143">Password</span></span>

<span data-ttu-id="50acb-144">Por padrão, Identity o requer que as senhas contenham um caractere maiúsculo, um caractere minúsculo, um dígito e um caractere não alfanumérico.</span><span class="sxs-lookup"><span data-stu-id="50acb-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="50acb-145">As senhas devem ter pelo menos seis caracteres.</span><span class="sxs-lookup"><span data-stu-id="50acb-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="50acb-146">As [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) podem ser definidas em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50acb-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="50acb-147">[Identidadeoptions. senha](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) especifica a [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="50acb-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="50acb-148">Propriedade</span><span class="sxs-lookup"><span data-stu-id="50acb-148">Property</span></span> | <span data-ttu-id="50acb-149">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-149">Description</span></span> | <span data-ttu-id="50acb-150">Padrão</span><span class="sxs-lookup"><span data-stu-id="50acb-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="50acb-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="50acb-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="50acb-152">Requer um número entre 0-9 na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="50acb-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="50acb-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="50acb-154">O comprimento mínimo da senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-154">The minimum length of the password.</span></span> | <span data-ttu-id="50acb-155">6</span><span class="sxs-lookup"><span data-stu-id="50acb-155">6</span></span> |
| [<span data-ttu-id="50acb-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="50acb-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="50acb-157">Requer um caractere minúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="50acb-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="50acb-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="50acb-159">Requer um caractere não alfanumérico na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="50acb-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="50acb-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="50acb-161">Aplica-se somente ao ASP.NET Core 2,0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="50acb-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="50acb-162">Requer o número de caracteres distintos na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="50acb-163">1</span><span class="sxs-lookup"><span data-stu-id="50acb-163">1</span></span> |
| [<span data-ttu-id="50acb-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="50acb-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="50acb-165">Requer um caractere maiúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="50acb-166">Propriedade</span><span class="sxs-lookup"><span data-stu-id="50acb-166">Property</span></span> | <span data-ttu-id="50acb-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-167">Description</span></span> | <span data-ttu-id="50acb-168">Padrão</span><span class="sxs-lookup"><span data-stu-id="50acb-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="50acb-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="50acb-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="50acb-170">Requer um número entre 0-9 na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="50acb-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="50acb-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="50acb-172">O comprimento mínimo da senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-172">The minimum length of the password.</span></span> | <span data-ttu-id="50acb-173">6</span><span class="sxs-lookup"><span data-stu-id="50acb-173">6</span></span> |
| [<span data-ttu-id="50acb-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="50acb-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="50acb-175">Requer um caractere minúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="50acb-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="50acb-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="50acb-177">Requer um caractere não alfanumérico na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="50acb-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="50acb-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="50acb-179">Requer um caractere maiúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="50acb-180">Conexão</span><span class="sxs-lookup"><span data-stu-id="50acb-180">Sign-in</span></span>

<span data-ttu-id="50acb-181">O código a seguir define `SignIn` as configurações (para valores padrão):</span><span class="sxs-lookup"><span data-stu-id="50acb-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="50acb-182">[Identityoptions. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) especifica o [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="50acb-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="50acb-183">Propriedade</span><span class="sxs-lookup"><span data-stu-id="50acb-183">Property</span></span> | <span data-ttu-id="50acb-184">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-184">Description</span></span> | <span data-ttu-id="50acb-185">Padrão</span><span class="sxs-lookup"><span data-stu-id="50acb-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="50acb-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="50acb-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="50acb-187">Requer um email confirmado para entrar.</span><span class="sxs-lookup"><span data-stu-id="50acb-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="50acb-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="50acb-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="50acb-189">Requer um número de telefone confirmado para entrar.</span><span class="sxs-lookup"><span data-stu-id="50acb-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="50acb-190">Tokens</span><span class="sxs-lookup"><span data-stu-id="50acb-190">Tokens</span></span>

<span data-ttu-id="50acb-191">O [identityoptions. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) especifica o [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="50acb-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="50acb-192">Propriedade</span><span class="sxs-lookup"><span data-stu-id="50acb-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="50acb-193">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="50acb-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="50acb-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="50acb-195">Obtém ou define o `AuthenticatorTokenProvider` usado para validar as entradas de dois fatores com um autenticador.</span><span class="sxs-lookup"><span data-stu-id="50acb-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="50acb-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="50acb-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="50acb-197">Obtém ou define o `ChangeEmailTokenProvider` usado para gerar tokens usados em emails de confirmação de alteração de e-mail.</span><span class="sxs-lookup"><span data-stu-id="50acb-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="50acb-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="50acb-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="50acb-199">Obtém ou define o `ChangePhoneNumberTokenProvider` usado para gerar tokens usados ao alterar números de telefone.</span><span class="sxs-lookup"><span data-stu-id="50acb-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="50acb-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="50acb-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="50acb-201">Obtém ou define o provedor de token usado para gerar tokens usados em emails de confirmação de conta.</span><span class="sxs-lookup"><span data-stu-id="50acb-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="50acb-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="50acb-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="50acb-203">Obtém ou define o [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) usado para gerar tokens usados em emails de redefinição de senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="50acb-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="50acb-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="50acb-205">Usado para construir um [provedor de token de usuário](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) com a chave usada como o nome do provedor.</span><span class="sxs-lookup"><span data-stu-id="50acb-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="50acb-206">Usuário</span><span class="sxs-lookup"><span data-stu-id="50acb-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="50acb-207">[Identityoptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) especifica o [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="50acb-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="50acb-208">Propriedade</span><span class="sxs-lookup"><span data-stu-id="50acb-208">Property</span></span> | <span data-ttu-id="50acb-209">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-209">Description</span></span> | <span data-ttu-id="50acb-210">Padrão</span><span class="sxs-lookup"><span data-stu-id="50acb-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="50acb-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="50acb-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="50acb-212">Caracteres permitidos no nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="50acb-212">Allowed characters in the username.</span></span> | <span data-ttu-id="50acb-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="50acb-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="50acb-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="50acb-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="50acb-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="50acb-215">0123456789</span></span><br><span data-ttu-id="50acb-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="50acb-216">-.\_@+</span></span> |
| [<span data-ttu-id="50acb-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="50acb-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="50acb-218">Requer que cada usuário tenha um email exclusivo.</span><span class="sxs-lookup"><span data-stu-id="50acb-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="50acb-219">Configurações de cookie</span><span class="sxs-lookup"><span data-stu-id="50acb-219">Cookie settings</span></span>

<span data-ttu-id="50acb-220">Configure o cookie do aplicativo no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50acb-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="50acb-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) deve ser chamado **depois** `AddIdentity` de chamar `AddDefaultIdentity` ou.</span><span class="sxs-lookup"><span data-stu-id="50acb-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="50acb-222">Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="50acb-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="50acb-223">Opções de hash de senha</span><span class="sxs-lookup"><span data-stu-id="50acb-223">Password Hasher options</span></span>

<span data-ttu-id="50acb-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Obtém e define opções para o hash de senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="50acb-225">Opção</span><span class="sxs-lookup"><span data-stu-id="50acb-225">Option</span></span> | <span data-ttu-id="50acb-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="50acb-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="50acb-227">O modo de compatibilidade usado ao aplicar o hash de novas senhas.</span><span class="sxs-lookup"><span data-stu-id="50acb-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="50acb-228">O padrão é <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="50acb-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="50acb-229">O primeiro byte de uma senha com hash, chamado de *marcador de formato*, especifica a versão do algoritmo de hash usado para fazer o hash da senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="50acb-230">Ao verificar uma senha em relação a um hash, o <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> método seleciona o algoritmo correto com base no primeiro byte.</span><span class="sxs-lookup"><span data-stu-id="50acb-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="50acb-231">Um cliente é capaz de autenticar, independentemente de qual versão do algoritmo foi usada para fazer o hash da senha.</span><span class="sxs-lookup"><span data-stu-id="50acb-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="50acb-232">Definir o modo de compatibilidade afeta o hash de *novas senhas*.</span><span class="sxs-lookup"><span data-stu-id="50acb-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="50acb-233">O número de iterações usadas ao aplicar o hash de senhas usando PBKDF2.</span><span class="sxs-lookup"><span data-stu-id="50acb-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="50acb-234">Esse valor é usado somente quando o <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> é definido como <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="50acb-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="50acb-235">O valor deve ser um inteiro positivo e o padrão é `10000` .</span><span class="sxs-lookup"><span data-stu-id="50acb-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="50acb-236">No exemplo a seguir, o <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> é definido como `12000` em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50acb-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
