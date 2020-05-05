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
# <a name="simple-authorization-in-aspnet-core"></a>Autorização simples no ASP.NET Core

<a name="security-authorization-simple"></a>

A autorização no MVC é controlada por `AuthorizeAttribute` meio do atributo e de seus vários parâmetros. Em sua forma mais simples, aplicar `AuthorizeAttribute` o atributo a um controlador ou ação limita o acesso ao controlador ou à ação para qualquer usuário autenticado.

Por exemplo, o código a seguir limita o acesso `AccountController` ao para qualquer usuário autenticado.

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

Se você quiser aplicar a autorização a uma ação em vez de ao controlador, aplique `AuthorizeAttribute` o atributo à própria ação:

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

Agora, somente usuários autenticados podem acessar `Logout` a função.

Você também pode usar o `AllowAnonymous` atributo para permitir o acesso por usuários não autenticados a ações individuais. Por exemplo: 

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

Isso permitiria apenas usuários autenticados para o `AccountController`, exceto para a `Login` ação, que é acessível por todos, independentemente de seu status autenticado ou não autenticado/anônimo.

> [!WARNING]
> `[AllowAnonymous]`ignora todas as instruções de autorização. Se você combinar `[AllowAnonymous]` e qualquer `[Authorize]` atributo, os `[Authorize]` atributos serão ignorados. Por exemplo, se você `[AllowAnonymous]` aplicar no nível do controlador, `[Authorize]` todos os atributos no mesmo controlador (ou em qualquer ação dentro dele) serão ignorados.
