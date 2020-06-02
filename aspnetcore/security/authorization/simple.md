---
<span data-ttu-id="8386a-101">Título: autorização simples no autor do ASP.NET Core: Rick-Anderson Descrição: saiba como usar o atributo Authorize para restringir o acesso a controladores e ações de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8386a-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="8386a-102">MS. Author: Riande MS. Date: 10/14/2016 no-loc:</span><span class="sxs-lookup"><span data-stu-id="8386a-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="8386a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8386a-103">'Blazor'</span></span>
- <span data-ttu-id="8386a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8386a-104">'Identity'</span></span>
- <span data-ttu-id="8386a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8386a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8386a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8386a-106">'Razor'</span></span>
- <span data-ttu-id="8386a-107">SignalRUID: segurança/autorização/simples</span><span class="sxs-lookup"><span data-stu-id="8386a-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="8386a-108">Autorização simples no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8386a-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="8386a-109">A autorização no ASP.NET Core é controlada com <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> e seus vários parâmetros.</span><span class="sxs-lookup"><span data-stu-id="8386a-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="8386a-110">Em sua forma mais simples, aplicar o `[Authorize]` atributo a um controlador, uma ação ou uma Razor página, limita o acesso a esse componente a qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="8386a-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="8386a-111">Por exemplo, o código a seguir limita o acesso ao `AccountController` para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="8386a-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="8386a-112">Se você quiser aplicar a autorização a uma ação em vez de ao controlador, aplique o `AuthorizeAttribute` atributo à própria ação:</span><span class="sxs-lookup"><span data-stu-id="8386a-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="8386a-113">Agora, somente usuários autenticados podem acessar a `Logout` função.</span><span class="sxs-lookup"><span data-stu-id="8386a-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="8386a-114">Você também pode usar o `AllowAnonymous` atributo para permitir o acesso por usuários não autenticados a ações individuais.</span><span class="sxs-lookup"><span data-stu-id="8386a-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="8386a-115">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8386a-115">For example:</span></span>

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

<span data-ttu-id="8386a-116">Isso permitiria apenas usuários autenticados para o `AccountController` , exceto para a `Login` ação, que é acessível por todos, independentemente de seu status autenticado ou não autenticado/anônimo.</span><span class="sxs-lookup"><span data-stu-id="8386a-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="8386a-117">`[AllowAnonymous]`ignora todas as instruções de autorização.</span><span class="sxs-lookup"><span data-stu-id="8386a-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="8386a-118">Se você combinar `[AllowAnonymous]` e qualquer `[Authorize]` atributo, os `[Authorize]` atributos serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="8386a-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="8386a-119">Por exemplo, se você aplicar `[AllowAnonymous]` no nível do controlador, todos os `[Authorize]` atributos no mesmo controlador (ou em qualquer ação dentro dele) serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="8386a-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
