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
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775629"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="6d5f0-103">Autorização simples no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d5f0-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="6d5f0-104">A autorização no MVC é controlada por `AuthorizeAttribute` meio do atributo e de seus vários parâmetros.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="6d5f0-105">Em sua forma mais simples, aplicar `AuthorizeAttribute` o atributo a um controlador ou ação limita o acesso ao controlador ou à ação para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="6d5f0-106">Por exemplo, o código a seguir limita o acesso `AccountController` ao para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="6d5f0-107">Se você quiser aplicar a autorização a uma ação em vez de ao controlador, aplique `AuthorizeAttribute` o atributo à própria ação:</span><span class="sxs-lookup"><span data-stu-id="6d5f0-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="6d5f0-108">Agora, somente usuários autenticados podem acessar `Logout` a função.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="6d5f0-109">Você também pode usar o `AllowAnonymous` atributo para permitir o acesso por usuários não autenticados a ações individuais.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="6d5f0-110">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="6d5f0-110">For example:</span></span>

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

<span data-ttu-id="6d5f0-111">Isso permitiria apenas usuários autenticados para o `AccountController`, exceto para a `Login` ação, que é acessível por todos, independentemente de seu status autenticado ou não autenticado/anônimo.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="6d5f0-112">`[AllowAnonymous]`ignora todas as instruções de autorização.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="6d5f0-113">Se você combinar `[AllowAnonymous]` e qualquer `[Authorize]` atributo, os `[Authorize]` atributos serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="6d5f0-114">Por exemplo, se você `[AllowAnonymous]` aplicar no nível do controlador, `[Authorize]` todos os atributos no mesmo controlador (ou em qualquer ação dentro dele) serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="6d5f0-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
