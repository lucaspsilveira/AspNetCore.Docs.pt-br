---
title: Escrever middleware do ASP.NET Core personalizado
author: rick-anderson
description: Saiba como escrever middleware do ASP.NET Core personalizado.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: bd0f172a558115b69e0dc1d10bb9065eea4db28b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775967"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="82ae2-103">Escrever middleware do ASP.NET Core personalizado</span><span class="sxs-lookup"><span data-stu-id="82ae2-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="82ae2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="82ae2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="82ae2-105">O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas.</span><span class="sxs-lookup"><span data-stu-id="82ae2-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="82ae2-106">O ASP.NET Core fornece um rico conjunto de componentes de middleware internos, mas em alguns cenários, talvez seja conveniente que você escreva um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="82ae2-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="82ae2-107">Classe de middleware</span><span class="sxs-lookup"><span data-stu-id="82ae2-107">Middleware class</span></span>

<span data-ttu-id="82ae2-108">O middleware geralmente é encapsulado em uma classe e exposto com um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="82ae2-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="82ae2-109">Considere o middleware a seguir, que define a cultura para a solicitação atual de uma cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="82ae2-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="82ae2-110">O código de exemplo anterior é usado para demonstrar a criação de um componente de middleware.</span><span class="sxs-lookup"><span data-stu-id="82ae2-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="82ae2-111">Para suporte de localização interna do ASP.NET Core, veja <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="82ae2-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="82ae2-112">Teste o middleware ao transmitir a cultura.</span><span class="sxs-lookup"><span data-stu-id="82ae2-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="82ae2-113">Por exemplo, solicite `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="82ae2-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="82ae2-114">O código a seguir move o delegado de middleware para uma classe:</span><span class="sxs-lookup"><span data-stu-id="82ae2-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="82ae2-115">A classe de middleware deve incluir:</span><span class="sxs-lookup"><span data-stu-id="82ae2-115">The middleware class must include:</span></span>

* <span data-ttu-id="82ae2-116">Um construtor público com um parâmetro de tipo <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="82ae2-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="82ae2-117">Um método público chamado `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="82ae2-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="82ae2-118">Esse método precisa:</span><span class="sxs-lookup"><span data-stu-id="82ae2-118">This method must:</span></span>
  * <span data-ttu-id="82ae2-119">Retornar um `Task`.</span><span class="sxs-lookup"><span data-stu-id="82ae2-119">Return a `Task`.</span></span>
  * <span data-ttu-id="82ae2-120">Aceitar um primeiro parâmetro do tipo <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="82ae2-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="82ae2-121">Os parâmetros adicionais para o construtor e `Invoke`/`InvokeAsync` são preenchidos pela [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="82ae2-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="82ae2-122">Dependências de middleware</span><span class="sxs-lookup"><span data-stu-id="82ae2-122">Middleware dependencies</span></span>

<span data-ttu-id="82ae2-123">O middleware deve seguir o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) ao expor suas dependências em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="82ae2-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="82ae2-124">O middleware é construído uma vez por *tempo de vida do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="82ae2-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="82ae2-125">Confira a seção [Dependências de middleware por solicitação](#per-request-middleware-dependencies) se você precisar compartilhar serviços com middleware em uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="82ae2-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="82ae2-126">Os componentes de middleware podem resolver suas dependências, utilizando a [DI (injeção de dependência)](xref:fundamentals/dependency-injection) por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="82ae2-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="82ae2-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) também pode aceitar parâmetros adicionais diretamente.</span><span class="sxs-lookup"><span data-stu-id="82ae2-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="82ae2-128">Dependências de middleware por solicitação</span><span class="sxs-lookup"><span data-stu-id="82ae2-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="82ae2-129">Uma vez que o middleware é construído durante a inicialização do aplicativo, e não por solicitação, os serviços de tempo de vida *com escopo* usados pelos construtores do middleware não são compartilhados com outros tipos de dependência inseridos durante cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="82ae2-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="82ae2-130">Se você tiver que compartilhar um serviço *com escopo* entre seu serviço de middleware e serviços de outros tipos, adicione esses serviços à assinatura do método `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="82ae2-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="82ae2-131">O método `Invoke` pode aceitar parâmetros adicionais que são preenchidos pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="82ae2-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="82ae2-132">Método de extensão de middleware</span><span class="sxs-lookup"><span data-stu-id="82ae2-132">Middleware extension method</span></span>

<span data-ttu-id="82ae2-133">O seguinte método de extensão expõe o middleware por meio do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="82ae2-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="82ae2-134">O código a seguir chama o middleware de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="82ae2-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="82ae2-135">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="82ae2-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
