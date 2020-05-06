---
title: Acessar o HttpContext no ASP.NET Core
author: coderandhiker
description: Saiba como acessar o HttpContext no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: ac0982f144c98bf1540b16013497fabeb6683e63
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773972"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Acessar o HttpContext no ASP.NET Core

ASP.NET Core aplicativos `HttpContext` acessam <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> por meio da interface e <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>sua implementação padrão. Só é necessário usar o `IHttpContextAccessor` quando você precisar acessar o `HttpContext` em um serviço.

## <a name="use-httpcontext-from-razor-pages"></a>Usar HttpContext de Razor páginas

As Razor páginas <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> expõem <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> a propriedade:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Usar HttpContext de uma Razor exibição

Razoras `HttpContext` exibições expõem diretamente por meio de uma propriedade [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) na exibição. O exemplo a seguir recupera o nome de usuário atual em um aplicativo de intranet usando a autenticação do Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Usar o HttpContext de um controlador

Os controladores expõem a propriedade [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):

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

## <a name="use-httpcontext-from-middleware"></a>Usar o HttpContext de middleware

Ao trabalhar com componentes personalizados de middleware, o `HttpContext` é passado para o método `Invoke` ou `InvokeAsync` e pode ser acessado quando o middleware for configurado:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Usar o HttpContext de componentes personalizados

Para outras estruturas e componentes personalizados que exigem acesso ao `HttpContext`, a abordagem recomendada é registrar uma dependência usando o contêiner integrado de [injeção de dependência](xref:fundamentals/dependency-injection). O contêiner de injeção de dependência `IHttpContextAccessor` fornece o a todas as classes que a declaram como uma dependência em seus construtores:

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

No exemplo a seguir:

* o `UserRepository` declara sua dependência no `IHttpContextAccessor`.
* A dependência é fornecida quando a injeção de dependência resolve a cadeia de dependências e cria uma instância do `UserRepository`.

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

## <a name="httpcontext-access-from-a-background-thread"></a>Acesso a HttpContext de um thread em segundo plano

`HttpContext`Não é thread-safe. Ler ou gravar propriedades de `HttpContext` fora do processamento de uma solicitação pode resultar em um <xref:System.NullReferenceException>.

> [!NOTE]
> Se seu aplicativo gerar erros `NullReferenceException` esporádicos, examine partes do código que iniciam o processamento em segundo plano ou que continuam o processamento após a conclusão de uma solicitação. Procure erros, como a definição de um método de controlador `async void`como.

Para executar com segurança o trabalho em segundo plano com os dados de `HttpContext`:

* Copie os dados necessários durante o processamento da solicitação.
* Passe os dados copiados para uma tarefa em segundo plano.

Para evitar código não seguro, nunca passe o `HttpContext` para um método que executa o trabalho em segundo plano. Em vez disso, passe os dados necessários. No exemplo a seguir, `SendEmailCore` é chamado para começar a enviar um email. O `correlationId` é passado para `SendEmailCore`, não para `HttpContext`o. A execução de `SendEmailCore` código não aguarda para concluir:

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
