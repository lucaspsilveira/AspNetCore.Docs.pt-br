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
ms.openlocfilehash: b5f97038145ed479c315af50a35d6c64d85425a7
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652955"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="35f5e-103">Autorização simples no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35f5e-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="35f5e-104">A autorização no ASP.NET Core é controlada com <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> e seus vários parâmetros.</span><span class="sxs-lookup"><span data-stu-id="35f5e-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="35f5e-105">Em sua forma mais simples, aplicar o `[Authorize]` atributo a um controlador, uma ação ou uma Razor página, limita o acesso a esse componente a qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="35f5e-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="35f5e-106">Por exemplo, o código a seguir limita o acesso ao `AccountController` para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="35f5e-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="35f5e-107">Se você quiser aplicar a autorização a uma ação em vez de ao controlador, aplique o `AuthorizeAttribute` atributo à própria ação:</span><span class="sxs-lookup"><span data-stu-id="35f5e-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="35f5e-108">Agora, somente usuários autenticados podem acessar a `Logout` função.</span><span class="sxs-lookup"><span data-stu-id="35f5e-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="35f5e-109">Você também pode usar o `AllowAnonymous` atributo para permitir o acesso por usuários não autenticados a ações individuais.</span><span class="sxs-lookup"><span data-stu-id="35f5e-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="35f5e-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="35f5e-110">For example:</span></span>

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

<span data-ttu-id="35f5e-111">Isso permitiria apenas usuários autenticados para o `AccountController` , exceto para a `Login` ação, que é acessível por todos, independentemente de seu status autenticado ou não autenticado/anônimo.</span><span class="sxs-lookup"><span data-stu-id="35f5e-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="35f5e-112">`[AllowAnonymous]`ignora todas as instruções de autorização.</span><span class="sxs-lookup"><span data-stu-id="35f5e-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="35f5e-113">Se você combinar `[AllowAnonymous]` e qualquer `[Authorize]` atributo, os `[Authorize]` atributos serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="35f5e-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="35f5e-114">Por exemplo, se você aplicar `[AllowAnonymous]` no nível do controlador, todos os `[Authorize]` atributos no mesmo controlador (ou em qualquer ação dentro dele) serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="35f5e-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a><span data-ttu-id="35f5e-115">Autorizar atributo e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="35f5e-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="35f5e-116">O <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ***não*** pode ser aplicado a Razor manipuladores de página.</span><span class="sxs-lookup"><span data-stu-id="35f5e-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="35f5e-117">Por exemplo, `[Authorize]` não pode ser aplicado a `OnGet` , `OnPost` ou a qualquer outro manipulador de página.</span><span class="sxs-lookup"><span data-stu-id="35f5e-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span>

<span data-ttu-id="35f5e-118">As duas abordagens a seguir podem ser usadas para aplicar a autorização aos Razor métodos do manipulador de página:</span><span class="sxs-lookup"><span data-stu-id="35f5e-118">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="35f5e-119">Use páginas separadas para manipuladores de página que exigem autorização diferente.</span><span class="sxs-lookup"><span data-stu-id="35f5e-119">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="35f5e-120">Moveu o conteúdo compartilhado em uma ou mais [exibições parciais](xref:mvc/views/partial).</span><span class="sxs-lookup"><span data-stu-id="35f5e-120">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="35f5e-121">Quando possível, essa é a abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="35f5e-121">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="35f5e-122">Para o conteúdo que deve compartilhar uma página comum, escreva um filtro que execute a autorização como parte do [IAsyncPageFilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="35f5e-122">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="35f5e-123">O projeto GitHub [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) demonstra essa abordagem:</span><span class="sxs-lookup"><span data-stu-id="35f5e-123">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="35f5e-124">O [AuthorizePageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) implementa o filtro de autorização:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="35f5e-124">The [AuthorizePageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="35f5e-125">O atributo [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) é aplicado ao `OnGet` manipulador de página:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="35f5e-125">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="35f5e-126">A [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) abordagem de exemplo PageHandlerAuth ***não:***</span><span class="sxs-lookup"><span data-stu-id="35f5e-126">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="35f5e-127">Compor com atributos de autorização aplicados à página, ao modelo de página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="35f5e-127">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="35f5e-128">A composição de atributos de autorização resulta em autenticação e autorização em execução várias vezes quando você tem mais `AuthorizeAttribute` ou `AuthorizeFilter` instâncias também aplicadas à página.</span><span class="sxs-lookup"><span data-stu-id="35f5e-128">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="35f5e-129">Trabalhe em conjunto com o restante de ASP.NET Core sistema de autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="35f5e-129">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="35f5e-130">Você deve verificar se o uso dessa abordagem funciona corretamente para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35f5e-130">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="35f5e-131">Não há planos para dar suporte aos `AuthorizeAttribute` Razor manipuladores de página.</span><span class="sxs-lookup"><span data-stu-id="35f5e-131">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
