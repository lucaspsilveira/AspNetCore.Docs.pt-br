---
title: Autorização baseada em função no ASP.NET Core
author: rick-anderson
description: Saiba como restringir o acesso ao controlador de ASP.NET Core e à ação passando funções para o atributo Authorize.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/roles
ms.openlocfilehash: 01d4239377b128f711a110a821e1afea58ca14a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776533"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Autorização baseada em função no ASP.NET Core

<a name="security-authorization-role-based"></a>

Quando uma identidade é criada, ela pode pertencer a uma ou mais funções. Por exemplo, Tracy pode pertencer ao administrador e às funções de usuário, enquanto Scott só pode pertencer à função de usuário. A forma como essas funções são criadas e gerenciadas depende do armazenamento de backup do processo de autorização. As funções são expostas ao desenvolvedor por meio do método [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) na classe [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .

## <a name="adding-role-checks"></a>Adicionando verificações de função

As verificações de autorização baseadas em função&mdash;são declarativas que o desenvolvedor as insere em seu código, em um controlador ou em uma ação dentro de um controlador, especificando funções das quais o usuário atual deve ser membro para acessar o recurso solicitado.

Por exemplo, o código a seguir limita o acesso a qualquer ação `AdministrationController` no para os usuários que são membros da `Administrator` função:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Você pode especificar várias funções como uma lista separada por vírgulas:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Esse controlador só poderá ser acessado por usuários que são membros `HRManager` da função ou `Finance` da função.

Se você aplicar vários atributos, um usuário de acesso deverá ser um membro de todas as funções especificadas; o exemplo a seguir requer que um usuário seja membro de ambas as `PowerUser` funções e `ControlPanelUser` .

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Você pode limitar ainda mais o acesso aplicando atributos de autorização de função adicionais no nível de ação:

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

No trecho de código anterior, os membros `Administrator` da função ou `PowerUser` a função podem acessar o controlador e `SetTime` a ação, mas somente os membros `Administrator` da função podem acessar `ShutDown` a ação.

Você também pode bloquear um controlador, mas permitir acesso anônimo e não autenticado a ações individuais.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

Para Razor páginas, o `AuthorizeAttribute` pode ser aplicado por um dos dois:

* Usando uma [Convenção](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)ou
* Aplicando `AuthorizeAttribute` o à `PageModel` instância:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> Os atributos de filtro `AuthorizeAttribute`, incluindo, só podem ser aplicados a PageModel e não podem ser aplicados a métodos de manipuladores de páginas específicos.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Verificações de função baseadas em política

Os requisitos de função também podem ser expressos usando a nova sintaxe de política, em que um desenvolvedor registra uma política na inicialização como parte da configuração do serviço de autorização. Isso normalmente ocorre em `ConfigureServices()` no arquivo *Startup.cs* .

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

As políticas são aplicadas usando `Policy` a propriedade no `AuthorizeAttribute` atributo:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Se você quiser especificar várias funções permitidas em um requisito, poderá especificá-las como parâmetros para `RequireRole` o método:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Este exemplo autoriza os usuários que pertencem às `Administrator`funções `PowerUser` ou. `BackupAdministrator`

### <a name="add-role-services-to-identity"></a>Adicionar serviços de função aIdentity

Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

