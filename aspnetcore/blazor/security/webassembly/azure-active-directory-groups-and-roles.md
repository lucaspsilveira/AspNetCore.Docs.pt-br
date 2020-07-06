---
title: ASP.NET Core Blazor WebAssembly com grupos e funções de Azure Active Directory
author: guardrex
description: Saiba como configurar o Blazor WebAssembly para usar grupos de Azure Active Directory e funções.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 6e27b062d7b5a1b72804fe5d4ea31ec65358ce45
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402150"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="5bc36-103">Grupos do Azure AD, funções administrativas e funções definidas pelo usuário</span><span class="sxs-lookup"><span data-stu-id="5bc36-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="5bc36-104">De [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="5bc36-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="5bc36-105">Azure Active Directory (AAD) fornece várias abordagens de autorização que podem ser combinadas com ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="5bc36-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="5bc36-106">Grupos definidos pelo usuário</span><span class="sxs-lookup"><span data-stu-id="5bc36-106">User-defined groups</span></span>
  * <span data-ttu-id="5bc36-107">Segurança</span><span class="sxs-lookup"><span data-stu-id="5bc36-107">Security</span></span>
  * <span data-ttu-id="5bc36-108">O365</span><span class="sxs-lookup"><span data-stu-id="5bc36-108">O365</span></span>
  * <span data-ttu-id="5bc36-109">Distribuição</span><span class="sxs-lookup"><span data-stu-id="5bc36-109">Distribution</span></span>
* <span data-ttu-id="5bc36-110">Funções</span><span class="sxs-lookup"><span data-stu-id="5bc36-110">Roles</span></span>
  * <span data-ttu-id="5bc36-111">Funções administrativas internas</span><span class="sxs-lookup"><span data-stu-id="5bc36-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="5bc36-112">Funções definidas pelo usuário</span><span class="sxs-lookup"><span data-stu-id="5bc36-112">User-defined roles</span></span>

<span data-ttu-id="5bc36-113">As diretrizes neste artigo se aplicam aos Blazor WebAssembly cenários de implantação do AAD descritos nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="5bc36-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="5bc36-114">Aplicativo autônomo com contas Microsoft</span><span class="sxs-lookup"><span data-stu-id="5bc36-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="5bc36-115">Aplicativo autônomo com o AAD</span><span class="sxs-lookup"><span data-stu-id="5bc36-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="5bc36-116">Aplicativo hospedado com o AAD</span><span class="sxs-lookup"><span data-stu-id="5bc36-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="5bc36-117">Grupos definidos pelo usuário e funções administrativas internas</span><span class="sxs-lookup"><span data-stu-id="5bc36-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="5bc36-118">Para configurar o aplicativo no portal do Azure para fornecer uma `groups` declaração de associação, consulte os seguintes artigos do Azure.</span><span class="sxs-lookup"><span data-stu-id="5bc36-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="5bc36-119">Atribua usuários a grupos do AAD definidos pelo usuário e funções administrativas internas.</span><span class="sxs-lookup"><span data-stu-id="5bc36-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="5bc36-120">As funções que usam grupos de segurança do Azure AD</span><span class="sxs-lookup"><span data-stu-id="5bc36-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="5bc36-121">`groupMembershipClaims`Attribute</span><span class="sxs-lookup"><span data-stu-id="5bc36-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="5bc36-122">Os exemplos a seguir pressupõem que um usuário é atribuído à função de *administrador de cobrança* interna do AAD.</span><span class="sxs-lookup"><span data-stu-id="5bc36-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="5bc36-123">A única `groups` declaração enviada pelo AAD apresenta os grupos e as funções do usuário como IDs de objeto (GUIDs) em uma matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="5bc36-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="5bc36-124">O aplicativo deve converter a matriz JSON de grupos e funções em declarações individuais nas `group` quais o aplicativo pode criar [políticas](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="5bc36-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="5bc36-125">Estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir propriedades de matriz para grupos e funções.</span><span class="sxs-lookup"><span data-stu-id="5bc36-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="5bc36-126">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="5bc36-126">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="5bc36-127">Crie uma fábrica de usuário personalizada no aplicativo autônomo ou aplicativo cliente de uma solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="5bc36-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="5bc36-128">A fábrica a seguir também é configurada para lidar com `roles` matrizes de declaração, que são abordadas na seção [funções definidas pelo usuário](#user-defined-roles) :</span><span class="sxs-lookup"><span data-stu-id="5bc36-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="5bc36-129">Não é necessário fornecer código para remover a `groups` declaração original porque ela é automaticamente removida pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="5bc36-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="5bc36-130">Registre a fábrica no `Program.Main` ( `Program.cs` ) do aplicativo autônomo ou aplicativo cliente de uma solução hospedada:</span><span class="sxs-lookup"><span data-stu-id="5bc36-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="5bc36-131">Crie uma [política](xref:security/authorization/policies) para cada grupo ou função no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="5bc36-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="5bc36-132">O exemplo a seguir cria uma política para a função de *administrador de cobrança* interna do AAD:</span><span class="sxs-lookup"><span data-stu-id="5bc36-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="5bc36-133">Para obter a lista completa de IDs de objeto de função do AAD, consulte a seção [IDs do grupo de funções administrativas do AAD](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="5bc36-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="5bc36-134">Nos exemplos a seguir, o aplicativo usa a política anterior para autorizar o usuário.</span><span class="sxs-lookup"><span data-stu-id="5bc36-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="5bc36-135">O [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funciona com a política:</span><span class="sxs-lookup"><span data-stu-id="5bc36-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="5bc36-136">O acesso a um componente inteiro pode ser baseado na política usando a [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="5bc36-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="5bc36-137">Se o usuário não estiver conectado, ele será redirecionado para a página de entrada do AAD e, em seguida, de volta para o componente depois de entrar.</span><span class="sxs-lookup"><span data-stu-id="5bc36-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="5bc36-138">Uma verificação de política também pode ser [executada no código com lógica de procedimento](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="5bc36-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a><span data-ttu-id="5bc36-139">Funções definidas pelo usuário</span><span class="sxs-lookup"><span data-stu-id="5bc36-139">User-defined roles</span></span>

<span data-ttu-id="5bc36-140">Um aplicativo registrado no AAD também pode ser configurado para usar funções definidas pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="5bc36-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="5bc36-141">Para configurar o aplicativo no portal do Azure para fornecer uma `roles` declaração de associação, consulte [como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="5bc36-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="5bc36-142">O exemplo a seguir pressupõe que um aplicativo está configurado com duas funções:</span><span class="sxs-lookup"><span data-stu-id="5bc36-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="5bc36-143">Embora não seja possível atribuir funções a grupos de segurança sem uma conta de Azure AD Premium, você pode atribuir usuários a funções e receber uma `roles` declaração para usuários com uma conta padrão do Azure.</span><span class="sxs-lookup"><span data-stu-id="5bc36-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="5bc36-144">As diretrizes nesta seção não exigem uma conta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="5bc36-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="5bc36-145">Várias funções são atribuídas no portal do Azure ao **_adicionar novamente um usuário_** para cada atribuição de função adicional.</span><span class="sxs-lookup"><span data-stu-id="5bc36-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="5bc36-146">A única `roles` declaração enviada pelo AAD apresenta as funções definidas pelo usuário como `appRoles` `value` s em uma matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="5bc36-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="5bc36-147">O aplicativo deve converter a matriz JSON de funções em `role` declarações individuais.</span><span class="sxs-lookup"><span data-stu-id="5bc36-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="5bc36-148">O `CustomUserFactory` mostrado na seção [grupos definidos pelo usuário e funções administrativas internas do AAD](#user-defined-groups-and-built-in-administrative-roles) é configurado para agir em uma `roles` declaração com um valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="5bc36-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="5bc36-149">Adicione e registre o `CustomUserFactory` no aplicativo cliente ou aplicativo autônomo de uma solução hospedada, conforme mostrado na seção [grupos definidos pelo usuário e funções administrativas internas do AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="5bc36-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="5bc36-150">Não é necessário fornecer código para remover a `roles` declaração original porque ela é automaticamente removida pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="5bc36-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="5bc36-151">No aplicativo `Program.Main` autônomo ou aplicativo cliente de uma solução hospedada, especifique a declaração denominada " `role` " como a declaração de função:</span><span class="sxs-lookup"><span data-stu-id="5bc36-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="5bc36-152">As abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="5bc36-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="5bc36-153">Qualquer um dos mecanismos de autorização nos componentes pode usar a `admin` função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="5bc36-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="5bc36-154">[ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="5bc36-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="5bc36-155">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="5bc36-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="5bc36-156">[Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="5bc36-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="5bc36-157">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="5bc36-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="5bc36-158">IDs do grupo de funções administrativas do AAD</span><span class="sxs-lookup"><span data-stu-id="5bc36-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="5bc36-159">As IDs de objeto apresentadas na tabela a seguir são usadas para criar [políticas](xref:security/authorization/policies) para `group` declarações.</span><span class="sxs-lookup"><span data-stu-id="5bc36-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="5bc36-160">As políticas permitem que um aplicativo autorize usuários para várias atividades em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5bc36-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="5bc36-161">Para obter mais informações, consulte a seção [grupos definidos pelo usuário e funções administrativas internas do AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="5bc36-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="5bc36-162">Função administrativa do AAD</span><span class="sxs-lookup"><span data-stu-id="5bc36-162">AAD Administrative Role</span></span> | <span data-ttu-id="5bc36-163">ID de objeto</span><span class="sxs-lookup"><span data-stu-id="5bc36-163">Object ID</span></span>
--- | ---
<span data-ttu-id="5bc36-164">Administrador de aplicativos</span><span class="sxs-lookup"><span data-stu-id="5bc36-164">Application administrator</span></span> | <span data-ttu-id="5bc36-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="5bc36-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="5bc36-166">Desenvolvedor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="5bc36-166">Application developer</span></span> | <span data-ttu-id="5bc36-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="5bc36-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="5bc36-168">Administrador de autenticação</span><span class="sxs-lookup"><span data-stu-id="5bc36-168">Authentication administrator</span></span> | <span data-ttu-id="5bc36-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="5bc36-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="5bc36-170">Administrador do Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="5bc36-170">Azure DevOps administrator</span></span> | <span data-ttu-id="5bc36-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="5bc36-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="5bc36-172">Administrador da Proteção de Informações do Azure</span><span class="sxs-lookup"><span data-stu-id="5bc36-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="5bc36-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="5bc36-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="5bc36-174">Administrador do conjunto de chaves B2C IEF</span><span class="sxs-lookup"><span data-stu-id="5bc36-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="5bc36-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="5bc36-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="5bc36-176">Administrador da política IEF B2C</span><span class="sxs-lookup"><span data-stu-id="5bc36-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="5bc36-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="5bc36-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="5bc36-178">Administrador de fluxo de usuário B2C</span><span class="sxs-lookup"><span data-stu-id="5bc36-178">B2C user flow administrator</span></span> | <span data-ttu-id="5bc36-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="5bc36-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="5bc36-180">Administrador de atributos de fluxo de usuário B2C</span><span class="sxs-lookup"><span data-stu-id="5bc36-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="5bc36-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="5bc36-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="5bc36-182">Administrador de cobrança</span><span class="sxs-lookup"><span data-stu-id="5bc36-182">Billing administrator</span></span> | <span data-ttu-id="5bc36-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="5bc36-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="5bc36-184">Administrador de aplicativos de nuvem</span><span class="sxs-lookup"><span data-stu-id="5bc36-184">Cloud application administrator</span></span> | <span data-ttu-id="5bc36-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="5bc36-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="5bc36-186">Administrador de dispositivo em nuvem</span><span class="sxs-lookup"><span data-stu-id="5bc36-186">Cloud device administrator</span></span> | <span data-ttu-id="5bc36-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="5bc36-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="5bc36-188">Administrador de conformidade</span><span class="sxs-lookup"><span data-stu-id="5bc36-188">Compliance administrator</span></span> | <span data-ttu-id="5bc36-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="5bc36-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="5bc36-190">Administrador de dados de conformidade</span><span class="sxs-lookup"><span data-stu-id="5bc36-190">Compliance data administrator</span></span> | <span data-ttu-id="5bc36-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="5bc36-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="5bc36-192">Administrador de acesso condicional</span><span class="sxs-lookup"><span data-stu-id="5bc36-192">Conditional Access administrator</span></span> | <span data-ttu-id="5bc36-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="5bc36-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="5bc36-194">Aprovador de acesso do Sistema de Proteção de Dados do Cliente</span><span class="sxs-lookup"><span data-stu-id="5bc36-194">Customer LockBox access approver</span></span> | <span data-ttu-id="5bc36-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="5bc36-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="5bc36-196">Administrador do desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="5bc36-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="5bc36-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="5bc36-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="5bc36-198">Leitores de diretórios</span><span class="sxs-lookup"><span data-stu-id="5bc36-198">Directory readers</span></span> | <span data-ttu-id="5bc36-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="5bc36-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="5bc36-200">Administrador do Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="5bc36-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="5bc36-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="5bc36-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="5bc36-202">Administrador do Exchange</span><span class="sxs-lookup"><span data-stu-id="5bc36-202">Exchange administrator</span></span> | <span data-ttu-id="5bc36-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="5bc36-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="5bc36-204">IdentityAdministrador de provedor externo</span><span class="sxs-lookup"><span data-stu-id="5bc36-204">External Identity Provider administrator</span></span> | <span data-ttu-id="5bc36-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="5bc36-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="5bc36-206">Administrador global</span><span class="sxs-lookup"><span data-stu-id="5bc36-206">Global administrator</span></span> | <span data-ttu-id="5bc36-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="5bc36-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="5bc36-208">Leitor global</span><span class="sxs-lookup"><span data-stu-id="5bc36-208">Global reader</span></span> | <span data-ttu-id="5bc36-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="5bc36-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="5bc36-210">Administrador de grupos</span><span class="sxs-lookup"><span data-stu-id="5bc36-210">Groups administrator</span></span> | <span data-ttu-id="5bc36-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="5bc36-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="5bc36-212">Emissor do convite ao convidado</span><span class="sxs-lookup"><span data-stu-id="5bc36-212">Guest inviter</span></span> | <span data-ttu-id="5bc36-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="5bc36-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="5bc36-214">Administrador de assistência técnica</span><span class="sxs-lookup"><span data-stu-id="5bc36-214">Helpdesk administrator</span></span> | <span data-ttu-id="5bc36-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="5bc36-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="5bc36-216">Administrador do Intune</span><span class="sxs-lookup"><span data-stu-id="5bc36-216">Intune administrator</span></span> | <span data-ttu-id="5bc36-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="5bc36-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="5bc36-218">Administrador do Kaizala</span><span class="sxs-lookup"><span data-stu-id="5bc36-218">Kaizala administrator</span></span> | <span data-ttu-id="5bc36-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="5bc36-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="5bc36-220">Administrador de licenças</span><span class="sxs-lookup"><span data-stu-id="5bc36-220">License administrator</span></span> | <span data-ttu-id="5bc36-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="5bc36-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="5bc36-222">Leitor de privacidade do centro de mensagens</span><span class="sxs-lookup"><span data-stu-id="5bc36-222">Message center privacy reader</span></span> | <span data-ttu-id="5bc36-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="5bc36-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="5bc36-224">Leitor do Centro de Mensagens</span><span class="sxs-lookup"><span data-stu-id="5bc36-224">Message center reader</span></span> | <span data-ttu-id="5bc36-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="5bc36-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="5bc36-226">Administrador de aplicativos do Office</span><span class="sxs-lookup"><span data-stu-id="5bc36-226">Office apps administrator</span></span> | <span data-ttu-id="5bc36-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="5bc36-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="5bc36-228">Administrador de senha</span><span class="sxs-lookup"><span data-stu-id="5bc36-228">Password administrator</span></span> | <span data-ttu-id="5bc36-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="5bc36-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="5bc36-230">Administrador do Power BI</span><span class="sxs-lookup"><span data-stu-id="5bc36-230">Power BI administrator</span></span> | <span data-ttu-id="5bc36-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="5bc36-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="5bc36-232">Administrador do Power Platform</span><span class="sxs-lookup"><span data-stu-id="5bc36-232">Power platform administrator</span></span> | <span data-ttu-id="5bc36-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="5bc36-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="5bc36-234">Administrador de autenticação privilegiada</span><span class="sxs-lookup"><span data-stu-id="5bc36-234">Privileged authentication administrator</span></span> | <span data-ttu-id="5bc36-235">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="5bc36-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="5bc36-236">Administrador de função com privilégios</span><span class="sxs-lookup"><span data-stu-id="5bc36-236">Privileged role administrator</span></span> | <span data-ttu-id="5bc36-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="5bc36-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="5bc36-238">Leitor de relatórios</span><span class="sxs-lookup"><span data-stu-id="5bc36-238">Reports reader</span></span> | <span data-ttu-id="5bc36-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="5bc36-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="5bc36-240">Administrador de pesquisas</span><span class="sxs-lookup"><span data-stu-id="5bc36-240">Search administrator</span></span> | <span data-ttu-id="5bc36-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="5bc36-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="5bc36-242">Editor de pesquisa</span><span class="sxs-lookup"><span data-stu-id="5bc36-242">Search editor</span></span> | <span data-ttu-id="5bc36-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="5bc36-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="5bc36-244">Administrador de segurança</span><span class="sxs-lookup"><span data-stu-id="5bc36-244">Security administrator</span></span> | <span data-ttu-id="5bc36-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="5bc36-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="5bc36-246">Operador de segurança</span><span class="sxs-lookup"><span data-stu-id="5bc36-246">Security operator</span></span> | <span data-ttu-id="5bc36-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="5bc36-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="5bc36-248">Leitor de segurança</span><span class="sxs-lookup"><span data-stu-id="5bc36-248">Security reader</span></span> | <span data-ttu-id="5bc36-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="5bc36-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="5bc36-250">Administrador de suporte a serviço</span><span class="sxs-lookup"><span data-stu-id="5bc36-250">Service support administrator</span></span> | <span data-ttu-id="5bc36-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="5bc36-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="5bc36-252">Administrador do SharePoint</span><span class="sxs-lookup"><span data-stu-id="5bc36-252">SharePoint administrator</span></span> | <span data-ttu-id="5bc36-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="5bc36-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="5bc36-254">Administrador do Skype for Business</span><span class="sxs-lookup"><span data-stu-id="5bc36-254">Skype for Business administrator</span></span> | <span data-ttu-id="5bc36-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="5bc36-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="5bc36-256">Administrador de Comunicações do Teams</span><span class="sxs-lookup"><span data-stu-id="5bc36-256">Teams Communications Administrator</span></span> | <span data-ttu-id="5bc36-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="5bc36-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="5bc36-258">Engenheiro de Suporte de Comunicações do Teams</span><span class="sxs-lookup"><span data-stu-id="5bc36-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="5bc36-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="5bc36-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="5bc36-260">Especialista em comunicações de equipes</span><span class="sxs-lookup"><span data-stu-id="5bc36-260">Teams Communications Specialist</span></span> | <span data-ttu-id="5bc36-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="5bc36-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="5bc36-262">Administrador de Serviços do Teams</span><span class="sxs-lookup"><span data-stu-id="5bc36-262">Teams Service Administrator</span></span> | <span data-ttu-id="5bc36-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="5bc36-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="5bc36-264">Administrador de usuários</span><span class="sxs-lookup"><span data-stu-id="5bc36-264">User administrator</span></span> | <span data-ttu-id="5bc36-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="5bc36-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bc36-266">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5bc36-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>