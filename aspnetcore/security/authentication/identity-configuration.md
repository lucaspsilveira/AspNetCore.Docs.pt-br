---
title: Configurar ASP.NET CoreIdentity
author: AdrienTorris
description: Entenda ASP.NET Core Identity valores padrão e saiba como configurar Identity Propriedades para usar valores personalizados.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: b88f2627eabc536f2d3b8e677020a67bfd1a40ba
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775642"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="919ea-103">Configurar identidade de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="919ea-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="919ea-104">ASP.NET Core identidade usa valores padrão para configurações como política de senha, bloqueio e configuração de cookie.</span><span class="sxs-lookup"><span data-stu-id="919ea-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="919ea-105">Essas configurações podem ser substituídas na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="919ea-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="919ea-106">Opções de identidade</span><span class="sxs-lookup"><span data-stu-id="919ea-106">Identity options</span></span>

<span data-ttu-id="919ea-107">A classe [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) representa as opções que podem ser usadas para configurar o sistema de identidade.</span><span class="sxs-lookup"><span data-stu-id="919ea-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="919ea-108">`IdentityOptions`deve ser definido **após** a `AddIdentity` chamada `AddDefaultIdentity`ou.</span><span class="sxs-lookup"><span data-stu-id="919ea-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="919ea-109">Identidade de declarações</span><span class="sxs-lookup"><span data-stu-id="919ea-109">Claims Identity</span></span>

<span data-ttu-id="919ea-110">[Identityoptions. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) especifica o [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) com as propriedades mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="919ea-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="919ea-111">Propriedade</span><span class="sxs-lookup"><span data-stu-id="919ea-111">Property</span></span> | <span data-ttu-id="919ea-112">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-112">Description</span></span> | <span data-ttu-id="919ea-113">Padrão</span><span class="sxs-lookup"><span data-stu-id="919ea-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="919ea-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="919ea-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="919ea-115">Obtém ou define o tipo de declaração usado para uma declaração de função.</span><span class="sxs-lookup"><span data-stu-id="919ea-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="919ea-116">ClaimTypes. Role</span><span class="sxs-lookup"><span data-stu-id="919ea-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="919ea-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="919ea-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="919ea-118">Obtém ou define o tipo de declaração usado para a declaração de carimbo de segurança.</span><span class="sxs-lookup"><span data-stu-id="919ea-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="919ea-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="919ea-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="919ea-120">Obtém ou define o tipo de declaração usado para a declaração de identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="919ea-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="919ea-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="919ea-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="919ea-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="919ea-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="919ea-123">Obtém ou define o tipo de declaração usado para a declaração de nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="919ea-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="919ea-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="919ea-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="919ea-125">Bloquear</span><span class="sxs-lookup"><span data-stu-id="919ea-125">Lockout</span></span>

<span data-ttu-id="919ea-126">O bloqueio é definido no método [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :</span><span class="sxs-lookup"><span data-stu-id="919ea-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="919ea-127">O código anterior é baseado no modelo `Login` de identidade.</span><span class="sxs-lookup"><span data-stu-id="919ea-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="919ea-128">As opções de bloqueio são `StartUp.ConfigureServices`definidas em:</span><span class="sxs-lookup"><span data-stu-id="919ea-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="919ea-129">O código anterior define o [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com valores padrão.</span><span class="sxs-lookup"><span data-stu-id="919ea-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="919ea-130">Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio.</span><span class="sxs-lookup"><span data-stu-id="919ea-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="919ea-131">[Identityoptions. Lock](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) especifica o [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="919ea-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="919ea-132">Propriedade</span><span class="sxs-lookup"><span data-stu-id="919ea-132">Property</span></span> | <span data-ttu-id="919ea-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-133">Description</span></span> | <span data-ttu-id="919ea-134">Padrão</span><span class="sxs-lookup"><span data-stu-id="919ea-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="919ea-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="919ea-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="919ea-136">Determina se um novo usuário pode ser bloqueado.</span><span class="sxs-lookup"><span data-stu-id="919ea-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="919ea-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="919ea-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="919ea-138">A quantidade de tempo que um usuário é bloqueado quando ocorre um bloqueio.</span><span class="sxs-lookup"><span data-stu-id="919ea-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="919ea-139">5 minutos</span><span class="sxs-lookup"><span data-stu-id="919ea-139">5 minutes</span></span> |
| [<span data-ttu-id="919ea-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="919ea-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="919ea-141">O número de tentativas de acesso com falha até que um usuário seja bloqueado, se o bloqueio estiver habilitado.</span><span class="sxs-lookup"><span data-stu-id="919ea-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="919ea-142">5</span><span class="sxs-lookup"><span data-stu-id="919ea-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="919ea-143">Senha</span><span class="sxs-lookup"><span data-stu-id="919ea-143">Password</span></span>

<span data-ttu-id="919ea-144">Por padrão, a identidade requer que as senhas contenham um caractere maiúsculo, um caractere minúsculo, um dígito e um caractere não alfanumérico.</span><span class="sxs-lookup"><span data-stu-id="919ea-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="919ea-145">As senhas devem ter pelo menos seis caracteres.</span><span class="sxs-lookup"><span data-stu-id="919ea-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="919ea-146">As [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) podem ser definidas `Startup.ConfigureServices`em.</span><span class="sxs-lookup"><span data-stu-id="919ea-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="919ea-147">[Identidadeoptions. senha](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) especifica a [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="919ea-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="919ea-148">Propriedade</span><span class="sxs-lookup"><span data-stu-id="919ea-148">Property</span></span> | <span data-ttu-id="919ea-149">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-149">Description</span></span> | <span data-ttu-id="919ea-150">Padrão</span><span class="sxs-lookup"><span data-stu-id="919ea-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="919ea-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="919ea-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="919ea-152">Requer um número entre 0-9 na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="919ea-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="919ea-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="919ea-154">O comprimento mínimo da senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-154">The minimum length of the password.</span></span> | <span data-ttu-id="919ea-155">6</span><span class="sxs-lookup"><span data-stu-id="919ea-155">6</span></span> |
| [<span data-ttu-id="919ea-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="919ea-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="919ea-157">Requer um caractere minúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="919ea-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="919ea-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="919ea-159">Requer um caractere não alfanumérico na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="919ea-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="919ea-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="919ea-161">Aplica-se somente ao ASP.NET Core 2,0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="919ea-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="919ea-162">Requer o número de caracteres distintos na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="919ea-163">1</span><span class="sxs-lookup"><span data-stu-id="919ea-163">1</span></span> |
| [<span data-ttu-id="919ea-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="919ea-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="919ea-165">Requer um caractere maiúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="919ea-166">Propriedade</span><span class="sxs-lookup"><span data-stu-id="919ea-166">Property</span></span> | <span data-ttu-id="919ea-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-167">Description</span></span> | <span data-ttu-id="919ea-168">Padrão</span><span class="sxs-lookup"><span data-stu-id="919ea-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="919ea-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="919ea-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="919ea-170">Requer um número entre 0-9 na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="919ea-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="919ea-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="919ea-172">O comprimento mínimo da senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-172">The minimum length of the password.</span></span> | <span data-ttu-id="919ea-173">6</span><span class="sxs-lookup"><span data-stu-id="919ea-173">6</span></span> |
| [<span data-ttu-id="919ea-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="919ea-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="919ea-175">Requer um caractere minúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="919ea-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="919ea-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="919ea-177">Requer um caractere não alfanumérico na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="919ea-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="919ea-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="919ea-179">Requer um caractere maiúsculo na senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="919ea-180">Conexão</span><span class="sxs-lookup"><span data-stu-id="919ea-180">Sign-in</span></span>

<span data-ttu-id="919ea-181">O código a seguir `SignIn` define as configurações (para valores padrão):</span><span class="sxs-lookup"><span data-stu-id="919ea-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="919ea-182">[Identityoptions. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) especifica o [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="919ea-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="919ea-183">Propriedade</span><span class="sxs-lookup"><span data-stu-id="919ea-183">Property</span></span> | <span data-ttu-id="919ea-184">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-184">Description</span></span> | <span data-ttu-id="919ea-185">Padrão</span><span class="sxs-lookup"><span data-stu-id="919ea-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="919ea-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="919ea-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="919ea-187">Requer um email confirmado para entrar.</span><span class="sxs-lookup"><span data-stu-id="919ea-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="919ea-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="919ea-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="919ea-189">Requer um número de telefone confirmado para entrar.</span><span class="sxs-lookup"><span data-stu-id="919ea-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="919ea-190">Tokens</span><span class="sxs-lookup"><span data-stu-id="919ea-190">Tokens</span></span>

<span data-ttu-id="919ea-191">O [identityoptions. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) especifica o [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="919ea-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="919ea-192">Propriedade</span><span class="sxs-lookup"><span data-stu-id="919ea-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="919ea-193">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="919ea-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="919ea-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="919ea-195">Obtém ou define o `AuthenticatorTokenProvider` usado para validar as entradas de dois fatores com um autenticador.</span><span class="sxs-lookup"><span data-stu-id="919ea-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="919ea-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="919ea-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="919ea-197">Obtém ou define o `ChangeEmailTokenProvider` usado para gerar tokens usados em emails de confirmação de alteração de e-mail.</span><span class="sxs-lookup"><span data-stu-id="919ea-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="919ea-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="919ea-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="919ea-199">Obtém ou define o `ChangePhoneNumberTokenProvider` usado para gerar tokens usados ao alterar números de telefone.</span><span class="sxs-lookup"><span data-stu-id="919ea-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="919ea-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="919ea-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="919ea-201">Obtém ou define o provedor de token usado para gerar tokens usados em emails de confirmação de conta.</span><span class="sxs-lookup"><span data-stu-id="919ea-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="919ea-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="919ea-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="919ea-203">Obtém ou define o [>\<de TUser IUserTwoFactorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) usado para gerar tokens usados em emails de redefinição de senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="919ea-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="919ea-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="919ea-205">Usado para construir um [provedor de token de usuário](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) com a chave usada como o nome do provedor.</span><span class="sxs-lookup"><span data-stu-id="919ea-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="919ea-206">Usuário</span><span class="sxs-lookup"><span data-stu-id="919ea-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="919ea-207">[Identityoptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) especifica o [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) com as propriedades mostradas na tabela.</span><span class="sxs-lookup"><span data-stu-id="919ea-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="919ea-208">Propriedade</span><span class="sxs-lookup"><span data-stu-id="919ea-208">Property</span></span> | <span data-ttu-id="919ea-209">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-209">Description</span></span> | <span data-ttu-id="919ea-210">Padrão</span><span class="sxs-lookup"><span data-stu-id="919ea-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="919ea-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="919ea-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="919ea-212">Caracteres permitidos no nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="919ea-212">Allowed characters in the username.</span></span> | <span data-ttu-id="919ea-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="919ea-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="919ea-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="919ea-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="919ea-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="919ea-215">0123456789</span></span><br><span data-ttu-id="919ea-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="919ea-216">-.\_@+</span></span> |
| [<span data-ttu-id="919ea-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="919ea-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="919ea-218">Requer que cada usuário tenha um email exclusivo.</span><span class="sxs-lookup"><span data-stu-id="919ea-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="919ea-219">Configurações de cookie</span><span class="sxs-lookup"><span data-stu-id="919ea-219">Cookie settings</span></span>

<span data-ttu-id="919ea-220">Configure o cookie do aplicativo no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="919ea-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="919ea-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) deve ser chamado **depois** de `AddIdentity` chamar `AddDefaultIdentity`ou.</span><span class="sxs-lookup"><span data-stu-id="919ea-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="919ea-222">Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="919ea-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="919ea-223">Opções de hash de senha</span><span class="sxs-lookup"><span data-stu-id="919ea-223">Password Hasher options</span></span>

<span data-ttu-id="919ea-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Obtém e define opções para o hash de senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="919ea-225">Opção</span><span class="sxs-lookup"><span data-stu-id="919ea-225">Option</span></span> | <span data-ttu-id="919ea-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="919ea-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="919ea-227">O modo de compatibilidade usado ao aplicar o hash de novas senhas.</span><span class="sxs-lookup"><span data-stu-id="919ea-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="919ea-228">O padrão é <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="919ea-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="919ea-229">O primeiro byte de uma senha com hash, chamado de *marcador de formato*, especifica a versão do algoritmo de hash usado para fazer o hash da senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="919ea-230">Ao verificar uma senha em relação a um hash, <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> o método seleciona o algoritmo correto com base no primeiro byte.</span><span class="sxs-lookup"><span data-stu-id="919ea-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="919ea-231">Um cliente é capaz de autenticar, independentemente de qual versão do algoritmo foi usada para fazer o hash da senha.</span><span class="sxs-lookup"><span data-stu-id="919ea-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="919ea-232">Definir o modo de compatibilidade afeta o hash de *novas senhas*.</span><span class="sxs-lookup"><span data-stu-id="919ea-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="919ea-233">O número de iterações usadas ao aplicar o hash de senhas usando PBKDF2.</span><span class="sxs-lookup"><span data-stu-id="919ea-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="919ea-234">Esse valor é usado somente quando o <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> é definido como <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="919ea-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="919ea-235">O valor deve ser um inteiro positivo e o padrão é `10000`.</span><span class="sxs-lookup"><span data-stu-id="919ea-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="919ea-236">No exemplo a seguir, o <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> é definido como `12000` em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="919ea-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
