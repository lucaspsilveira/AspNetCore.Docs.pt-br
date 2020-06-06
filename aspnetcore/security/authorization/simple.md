---
title: Autorização simples no ASP.NET Core
author: rick-anderson
description: Saiba como usar o atributo Authorize para restringir o acesso a ASP.NET Core de controladores e ações.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272119"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="a99a0-103">Autorização simples no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a99a0-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="a99a0-104">A autorização no ASP.NET Core é controlada com <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> e seus vários parâmetros.</span><span class="sxs-lookup"><span data-stu-id="a99a0-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="a99a0-105">Em sua forma mais simples, aplicar o `[Authorize]` atributo a um controlador, uma ação ou uma Razor página, limita o acesso a esse componente a qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="a99a0-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="a99a0-106">Por exemplo, o código a seguir limita o acesso ao `AccountController` para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="a99a0-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="a99a0-107">Se você quiser aplicar a autorização a uma ação em vez de ao controlador, aplique o `AuthorizeAttribute` atributo à própria ação:</span><span class="sxs-lookup"><span data-stu-id="a99a0-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="a99a0-108">Agora, somente usuários autenticados podem acessar a `Logout` função.</span><span class="sxs-lookup"><span data-stu-id="a99a0-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="a99a0-109">Você também pode usar o `AllowAnonymous` atributo para permitir o acesso por usuários não autenticados a ações individuais.</span><span class="sxs-lookup"><span data-stu-id="a99a0-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="a99a0-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="a99a0-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="a99a0-111">Isso permitiria apenas usuários autenticados para o `AccountController` , exceto para a `Login` ação, que é acessível por todos, independentemente de seu status autenticado ou não autenticado/anônimo.</span><span class="sxs-lookup"><span data-stu-id="a99a0-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="a99a0-112">`[AllowAnonymous]`ignora todas as instruções de autorização.</span><span class="sxs-lookup"><span data-stu-id="a99a0-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="a99a0-113">Se você combinar `[AllowAnonymous]` e qualquer `[Authorize]` atributo, os `[Authorize]` atributos serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="a99a0-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="a99a0-114">Por exemplo, se você aplicar `[AllowAnonymous]` no nível do controlador, todos os `[Authorize]` atributos no mesmo controlador (ou em qualquer ação dentro dele) serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="a99a0-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
