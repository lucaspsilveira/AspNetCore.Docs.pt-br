---
title: Acessar o HttpContext no ASP.NET Core
author: coderandhiker
description: Saiba como acessar o HttpContext no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: 716e74551b95455c99abbac58b712f013acfde56
ms.sourcegitcommit: d4527df91f2c15bbe1cbf5a541adbea5747897aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82852373"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="94e7f-103">Acessar o HttpContext no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94e7f-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="94e7f-104">ASP.NET Core aplicativos `HttpContext` acessam <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> por meio da interface e <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>sua implementação padrão.</span><span class="sxs-lookup"><span data-stu-id="94e7f-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="94e7f-105">Só é necessário usar o `IHttpContextAccessor` quando você precisar acessar o `HttpContext` em um serviço.</span><span class="sxs-lookup"><span data-stu-id="94e7f-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="94e7f-106">Usar HttpContext de Razor páginas</span><span class="sxs-lookup"><span data-stu-id="94e7f-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="94e7f-107">As Razor páginas <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> expõem <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> a propriedade:</span><span class="sxs-lookup"><span data-stu-id="94e7f-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="94e7f-108">Usar HttpContext de uma Razor exibição</span><span class="sxs-lookup"><span data-stu-id="94e7f-108">Use HttpContext from a Razor view</span></span>

Razor<span data-ttu-id="94e7f-109">as `HttpContext` exibições expõem diretamente por meio de uma propriedade [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) na exibição.</span><span class="sxs-lookup"><span data-stu-id="94e7f-109"> views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="94e7f-110">O exemplo a seguir recupera o nome de usuário atual em um aplicativo de intranet usando a autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="94e7f-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="94e7f-111">Usar o HttpContext de um controlador</span><span class="sxs-lookup"><span data-stu-id="94e7f-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="94e7f-112">Os controladores expõem a propriedade [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):</span><span class="sxs-lookup"><span data-stu-id="94e7f-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="94e7f-113">Usar o HttpContext de middleware</span><span class="sxs-lookup"><span data-stu-id="94e7f-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="94e7f-114">Ao trabalhar com componentes personalizados de middleware, o `HttpContext` é passado para o método `Invoke` ou `InvokeAsync` e pode ser acessado quando o middleware for configurado:</span><span class="sxs-lookup"><span data-stu-id="94e7f-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="94e7f-115">Usar o HttpContext de componentes personalizados</span><span class="sxs-lookup"><span data-stu-id="94e7f-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="94e7f-116">Para outras estruturas e componentes personalizados que exigem acesso ao `HttpContext`, a abordagem recomendada é registrar uma dependência usando o contêiner integrado de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="94e7f-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="94e7f-117">O contêiner de injeção de dependência `IHttpContextAccessor` fornece o a todas as classes que a declaram como uma dependência em seus construtores:</span><span class="sxs-lookup"><span data-stu-id="94e7f-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="94e7f-118">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="94e7f-118">In the following example:</span></span>

* <span data-ttu-id="94e7f-119">o `UserRepository` declara sua dependência no `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="94e7f-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="94e7f-120">A dependência é fornecida quando a injeção de dependência resolve a cadeia de dependências e cria uma instância do `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="94e7f-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="94e7f-121">Acesso a HttpContext de um thread em segundo plano</span><span class="sxs-lookup"><span data-stu-id="94e7f-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="94e7f-122">`HttpContext`Não é thread-safe.</span><span class="sxs-lookup"><span data-stu-id="94e7f-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="94e7f-123">Ler ou gravar propriedades de `HttpContext` fora do processamento de uma solicitação pode resultar em um <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="94e7f-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="94e7f-124">Se seu aplicativo gerar erros `NullReferenceException` esporádicos, examine partes do código que iniciam o processamento em segundo plano ou que continuam o processamento após a conclusão de uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="94e7f-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="94e7f-125">Procure erros, como a definição de um método de controlador `async void`como.</span><span class="sxs-lookup"><span data-stu-id="94e7f-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="94e7f-126">Para executar com segurança o trabalho em segundo plano com os dados de `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="94e7f-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="94e7f-127">Copie os dados necessários durante o processamento da solicitação.</span><span class="sxs-lookup"><span data-stu-id="94e7f-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="94e7f-128">Passe os dados copiados para uma tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="94e7f-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="94e7f-129">Para evitar código não seguro, nunca passe o `HttpContext` para um método que executa o trabalho em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="94e7f-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="94e7f-130">Em vez disso, passe os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="94e7f-130">Pass the required data instead.</span></span> <span data-ttu-id="94e7f-131">No exemplo a seguir, `SendEmailCore` é chamado para começar a enviar um email.</span><span class="sxs-lookup"><span data-stu-id="94e7f-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="94e7f-132">O `correlationId` é passado para `SendEmailCore`, não para `HttpContext`o.</span><span class="sxs-lookup"><span data-stu-id="94e7f-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="94e7f-133">A execução de `SendEmailCore` código não aguarda para concluir:</span><span class="sxs-lookup"><span data-stu-id="94e7f-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="blazor-and-shared-state"></a>Blazor<span data-ttu-id="94e7f-134">e estado compartilhado</span><span class="sxs-lookup"><span data-stu-id="94e7f-134"> and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
