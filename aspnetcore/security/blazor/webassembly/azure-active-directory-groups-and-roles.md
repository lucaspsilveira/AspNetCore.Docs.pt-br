---
title: ASP.NET Core Blazor Webassembly com grupos e funções de Azure Active Directory
author: guardrex
description: Saiba como configurar o Blazor Webassembly para usar grupos de Azure Active Directory e funções.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 87cdf02a6f6babc869d90658e6a7cd54db73bb68
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756022"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Grupos do Azure AD, funções administrativas e funções definidas pelo usuário

De [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) fornece várias abordagens de autorização que podem ser combinadas com ASP.NET Core Identity :

* Grupos definidos pelo usuário
  * Segurança
  * O365
  * Distribuição
* Funções
  * Funções administrativas internas
  * Funções definidas pelo usuário

As diretrizes neste artigo se aplicam aos Blazor cenários de implantação do AAD do Webassembly descritos nos tópicos a seguir:

* [Aplicativo autônomo com contas Microsoft](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [Aplicativo autônomo com o AAD](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [Aplicativo hospedado com o AAD](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Grupos definidos pelo usuário e funções administrativas internas

Para configurar o aplicativo no portal do Azure para fornecer uma `groups` declaração de associação, consulte os seguintes artigos do Azure. Atribua usuários a grupos do AAD definidos pelo usuário e funções administrativas internas.

* [As funções que usam grupos de segurança do Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [atributo groupMembershipClaims](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Os exemplos a seguir pressupõem que um usuário é atribuído à função de *administrador de cobrança* interna do AAD.

A única `groups` declaração enviada pelo AAD apresenta os grupos e as funções do usuário como IDs de objeto (GUIDs) em uma matriz JSON. O aplicativo deve converter a matriz JSON de grupos e funções em declarações individuais nas `group` quais o aplicativo pode criar [políticas](xref:security/authorization/policies) .

Estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir propriedades de matriz para grupos e funções.

*CustomUserAccount.cs*:

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

Crie uma fábrica de usuário personalizada no aplicativo autônomo ou aplicativo cliente de uma solução hospedada. A fábrica a seguir também é configurada para lidar com `roles` matrizes de declaração, que são abordadas na seção [funções definidas pelo usuário](#user-defined-roles) :

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

Não é necessário fornecer código para remover a `groups` declaração original porque ela é automaticamente removida pela estrutura.

Registre a fábrica no `Program.Main` (*Program.cs*) do aplicativo autônomo ou aplicativo cliente de uma solução hospedada:

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

Crie uma [política](xref:security/authorization/policies) para cada grupo ou função no `Program.Main` . O exemplo a seguir cria uma política para a função de *administrador de cobrança* interna do AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Para obter a lista completa de IDs de objeto de função do AAD, consulte a seção [IDs do grupo de funções administrativas do AAD](#aad-adminstrative-role-group-ids) .

Nos exemplos a seguir, o aplicativo usa a política anterior para autorizar o usuário.

O [componente AuthorizeView](xref:security/blazor/index#authorizeview-component) funciona com a política:

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

O acesso a um componente inteiro pode ser baseado na política usando a [ `[Authorize]` diretiva de atributo](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Se o usuário não estiver conectado, ele será redirecionado para a página de entrada do AAD e, em seguida, de volta para o componente depois de entrar.

Uma verificação de política também pode ser [executada no código com lógica de procedimento](xref:security/blazor/index#procedural-logic):

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

### <a name="user-defined-roles"></a>Funções definidas pelo usuário

Um aplicativo registrado no AAD também pode ser configurado para usar funções definidas pelo usuário.

Para configurar o aplicativo no portal do Azure para fornecer uma `roles` declaração de associação, consulte [como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) na documentação do Azure.

O exemplo a seguir pressupõe que um aplicativo está configurado com duas funções:

* `admin`
* `developer`

> [!NOTE]
> Embora não seja possível atribuir funções a grupos de segurança sem uma conta de Azure AD Premium, você pode atribuir usuários a funções e receber uma `roles` declaração para usuários com uma conta padrão do Azure. As diretrizes nesta seção não exigem uma conta de Azure AD Premium.
>
> Várias funções são atribuídas no portal do Azure ao **_adicionar novamente um usuário_** para cada atribuição de função adicional.

A única `roles` declaração enviada pelo AAD apresenta as funções definidas pelo usuário como `appRoles` `value` s em uma matriz JSON. O aplicativo deve converter a matriz JSON de funções em `role` declarações individuais.

O `CustomUserFactory` mostrado na seção [grupos definidos pelo usuário e funções administrativas internas do AAD](#user-defined-groups-and-built-in-administrative-roles) é configurado para agir em uma `roles` declaração com um valor de matriz JSON. Adicione e registre o `CustomUserFactory` no aplicativo cliente ou aplicativo autônomo de uma solução hospedada, conforme mostrado na seção [grupos definidos pelo usuário e funções administrativas internas do AAD](#user-defined-groups-and-built-in-administrative-roles) . Não é necessário fornecer código para remover a `roles` declaração original porque ela é automaticamente removida pela estrutura.

No aplicativo `Program.Main` autônomo ou aplicativo cliente de uma solução hospedada, especifique a declaração denominada " `role` " como a declaração de função:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

As abordagens de autorização de componente são funcionais neste ponto. Qualquer um dos mecanismos de autorização nos componentes pode usar a `admin` função para autorizar o usuário:

* [Componente AuthorizeView](xref:security/blazor/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` diretiva de atributo](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )
* [Lógica de procedimento](xref:security/blazor/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )

  Há suporte para vários testes de função:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>IDs do grupo de funções administrativas do AAD

As IDs de objeto apresentadas na tabela a seguir são usadas para criar [políticas](xref:security/authorization/policies) para `group` declarações. As políticas permitem que um aplicativo autorize usuários para várias atividades em um aplicativo. Para obter mais informações, consulte a seção [grupos definidos pelo usuário e funções administrativas internas do AAD](#user-defined-groups-and-built-in-administrative-roles) .

Função administrativa do AAD | ID de objeto
--- | ---
Administrador de aplicativos | fa11557b-4f15-4ddd-85d5-313c7cd74047
Desenvolvedor de aplicativos | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Administrador de autenticação | 02d110a1-96b1-419e-af87-746461b60ed7
Administrador do Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Administrador da Proteção de Informações do Azure | 18632dce-f9b5-4f01-abb5-37051f06860e
Administrador do conjunto de chaves B2C IEF | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Administrador da política IEF B2C | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Administrador de fluxo de usuário B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Administrador de atributos de fluxo de usuário B2C | dd0baca0-a535-48c1-b871-8431abe16452
Administrador de cobrança | 69ff516a-b57d-4697-a429-9de4af7b5609
Administrador de aplicativos de nuvem | 250b5fe3-b553-458d-9a53-b782c13c34bf
Administrador de dispositivo em nuvem | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Administrador de conformidade | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Administrador de dados de conformidade | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrador de acesso condicional | 8f71a611-137d-49af-87ad-e97f1fd5da76
Aprovador de acesso do Sistema de Proteção de Dados do Cliente | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Administrador do desktop Analytics | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Leitores de diretórios | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Administrador do Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Administrador do Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAdministrador de provedor externo | febfaeb4-e478-407a-b4b3-f4d9716618a2
Administrador global | a45ba61b-44db-462c-924b-3b2719152588
Leitor global | f6903b21-6aba-4124-b44c-76671796b9d5
Administrador de grupos | 158b3e5a-d89d-460b-92b5-3b34985f0197
Emissor do convite ao convidado | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Administrador de assistência técnica | 108678c8-6628-44e1-8d01-caf598a6a5f5
Administrador do Intune | 79950741-23fa-4189-b2cb-46640601c497
Administrador do Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Administrador de licenças | 3355458a-e423-44bf-8b98-4ac5e572cea5
Leitor de privacidade do centro de mensagens | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Leitor do Centro de Mensagens | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Administrador de aplicativos do Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Administrador de senha | 466e48b7-5d66-4ae5-8911-1a118de74941
Administrador do Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Administrador do Power Platform | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Administrador de autenticação privilegiada | 0829f731-b46d-419F-9742-aeb122367d11
Administrador de função com privilégios | f20a725a-d1c8-4107-83ea-1171c97d00c7
Leitor de relatórios | 54635450-e8ed-4f2d-9632-07db2517b4de
Administrador de pesquisas | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Editor de pesquisa | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Administrador de segurança | 20fa50e3-6531-44d8-bd39-b251420568ad
Operador de segurança | 43aae017-8e51-4188-91ab-e6debd572800
Leitor de segurança | 45035cd3-fd97-4250-8197-3a53d3562d9b
Administrador de suporte a serviço | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Administrador do SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Administrador do Skype for Business | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Administrador de Comunicações do Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Engenheiro de Suporte de Comunicações do Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Especialista em comunicações de equipes | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Administrador de Serviços do Teams | 8846a0be-197b-443a-b13c-11192691fa24
Administrador de usuários | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
