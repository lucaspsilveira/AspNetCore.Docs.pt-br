---
title: Autorização baseada em políticas no ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar manipuladores de política de autorização para impor requisitos de autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074156"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="0f53f-103">Autorização baseada em políticas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f53f-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f53f-104">Sob os bastidores, a autorização [baseada em função](xref:security/authorization/roles) e a [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="0f53f-105">Esses blocos de construção dão suporte à expressão de avaliações de autorização no código.</span><span class="sxs-lookup"><span data-stu-id="0f53f-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="0f53f-106">O resultado é uma estrutura de autorização mais rica, reutilizável e que pode ser testada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="0f53f-107">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="0f53f-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="0f53f-108">Ele é registrado como parte da configuração do serviço de autorização, no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="0f53f-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="0f53f-109">No exemplo anterior, uma política "AtLeast21" é criada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="0f53f-110">Ele tem um requisito único &mdash; de uma idade mínima, que é fornecida como um parâmetro para o requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="0f53f-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="0f53f-111">IAuthorizationService</span></span> 

<span data-ttu-id="0f53f-112">O serviço primário que determina se a autorização é bem-sucedida é <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="0f53f-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="0f53f-113">O código anterior realça os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="0f53f-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="0f53f-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>é um serviço de marcador sem métodos e o mecanismo para controlar se a autorização é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="0f53f-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="0f53f-115">Cada uma <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> é responsável por verificar se os requisitos são atendidos:</span><span class="sxs-lookup"><span data-stu-id="0f53f-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="0f53f-116">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="0f53f-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="0f53f-117">O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:</span><span class="sxs-lookup"><span data-stu-id="0f53f-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="0f53f-118">O código a seguir mostra um típico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0f53f-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="0f53f-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` para autorização.</span><span class="sxs-lookup"><span data-stu-id="0f53f-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="0f53f-120">Aplicar políticas a controladores MVC</span><span class="sxs-lookup"><span data-stu-id="0f53f-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="0f53f-121">Se você estiver usando Razor páginas, consulte [aplicar políticas a Razor páginas](#apply-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="0f53f-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="0f53f-122">As políticas são aplicadas aos controladores usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="0f53f-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0f53f-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f53f-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="0f53f-124">Aplicar políticas a Razor páginas</span><span class="sxs-lookup"><span data-stu-id="0f53f-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="0f53f-125">As políticas são aplicadas às Razor páginas usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="0f53f-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0f53f-126">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f53f-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="0f53f-127">As políticas também podem ser aplicadas a Razor páginas usando uma [Convenção de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="0f53f-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="0f53f-128">Requisitos</span><span class="sxs-lookup"><span data-stu-id="0f53f-128">Requirements</span></span>

<span data-ttu-id="0f53f-129">Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="0f53f-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="0f53f-130">Em nossa política de "AtLeast21", o requisito é um único parâmetro &mdash; de idade mínima.</span><span class="sxs-lookup"><span data-stu-id="0f53f-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="0f53f-131">Um requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia.</span><span class="sxs-lookup"><span data-stu-id="0f53f-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="0f53f-132">Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0f53f-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="0f53f-133">Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos deverão ser aprovados para que a avaliação da política seja realizada com êxito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="0f53f-134">Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados em uma base **e** .</span><span class="sxs-lookup"><span data-stu-id="0f53f-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="0f53f-135">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="0f53f-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="0f53f-136">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="0f53f-136">Authorization handlers</span></span>

<span data-ttu-id="0f53f-137">Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="0f53f-138">O manipulador de autorização avalia os requisitos em relação a um [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornecido para determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="0f53f-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="0f53f-139">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="0f53f-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="0f53f-140">Um manipulador pode herdar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), em que `TRequirement` é o requisito a ser manipulado.</span><span class="sxs-lookup"><span data-stu-id="0f53f-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="0f53f-141">Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="0f53f-142">Usar um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="0f53f-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="0f53f-143">Veja a seguir um exemplo de uma relação um-para-um na qual um manipulador de idade mínima utiliza um único requisito:</span><span class="sxs-lookup"><span data-stu-id="0f53f-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="0f53f-144">O código anterior determina se a entidade de usuário atual tem uma declaração de data de nascimento que foi emitida por um emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="0f53f-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="0f53f-145">A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída é retornada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="0f53f-146">Quando uma declaração está presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="0f53f-147">Se o usuário atender à idade mínima definida pelo requisito, a autorização será considerada bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="0f53f-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="0f53f-148">Quando a autorização é bem-sucedida, `context.Succeed` é invocada com o requisito atendido como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0f53f-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="0f53f-149">Usar um manipulador para vários requisitos</span><span class="sxs-lookup"><span data-stu-id="0f53f-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="0f53f-150">Veja a seguir um exemplo de uma relação um-para-muitos na qual um manipulador de permissão pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="0f53f-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="0f53f-151">O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; uma propriedade que contém os requisitos não marcados como bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="0f53f-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="0f53f-152">Para um `ReadPermission` requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="0f53f-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="0f53f-153">No caso de um `EditPermission` requisito ou `DeletePermission` , ele deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="0f53f-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="0f53f-154">Registro de manipulador</span><span class="sxs-lookup"><span data-stu-id="0f53f-154">Handler registration</span></span>

<span data-ttu-id="0f53f-155">Os manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="0f53f-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="0f53f-156">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f53f-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="0f53f-157">O código anterior é registrado `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="0f53f-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="0f53f-158">Os manipuladores podem ser registrados usando qualquer tempo de vida de [serviço](xref:fundamentals/dependency-injection#service-lifetimes)interno.</span><span class="sxs-lookup"><span data-stu-id="0f53f-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="0f53f-159">O que um manipulador deve retornar?</span><span class="sxs-lookup"><span data-stu-id="0f53f-159">What should a handler return?</span></span>

<span data-ttu-id="0f53f-160">Observe que o `Handle` método no [exemplo do manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="0f53f-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="0f53f-161">Como é indicado um status de êxito ou falha?</span><span class="sxs-lookup"><span data-stu-id="0f53f-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="0f53f-162">Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)` , passando o requisito que foi validado com êxito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="0f53f-163">Um manipulador não precisa lidar com falhas geralmente, já que outros manipuladores para o mesmo requisito podem ser bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="0f53f-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="0f53f-164">Para garantir a falha, mesmo que outros manipuladores de requisitos tenham êxito, chame `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="0f53f-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="0f53f-165">Se um manipulador chamar `context.Succeed` ou `context.Fail` , todos os outros manipuladores ainda serão chamados.</span><span class="sxs-lookup"><span data-stu-id="0f53f-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="0f53f-166">Isso permite que os requisitos produzam efeitos colaterais, como registro em log, que ocorre mesmo se outro manipulador tiver validado ou reprovado com êxito um requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="0f53f-167">Quando definido como `false` , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Core 1,1 e posterior) circuitos curtos a execução de manipuladores quando `context.Fail` é chamada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="0f53f-168">`InvokeHandlersAfterFailure``true`o padrão é, nesse caso, todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="0f53f-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="0f53f-169">Os manipuladores de autorização são chamados mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="0f53f-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="0f53f-170">Por que desejo vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="0f53f-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="0f53f-171">Nos casos em que você deseja que a avaliação seja em um **ou** base, implemente vários manipuladores para um único requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="0f53f-172">Por exemplo, a Microsoft tem portas que só são abertas com cartões-chave.</span><span class="sxs-lookup"><span data-stu-id="0f53f-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="0f53f-173">Se você deixar o cartão-chave em casa, o recepcionista imprime um adesivo temporário e abre a porta para você.</span><span class="sxs-lookup"><span data-stu-id="0f53f-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="0f53f-174">Nesse cenário, você teria um único requisito, *BuildingEntry*, mas vários manipuladores, cada um examinando um único requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="0f53f-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="0f53f-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="0f53f-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0f53f-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="0f53f-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0f53f-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="0f53f-178">Verifique se ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="0f53f-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="0f53f-179">Se um dos manipuladores for executado com sucesso quando uma política avaliar o `BuildingEntryRequirement` , a avaliação da política será realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="0f53f-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="0f53f-180">Usar um Func para atender a uma política</span><span class="sxs-lookup"><span data-stu-id="0f53f-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="0f53f-181">Pode haver situações em que o cumprimento de uma política é simples de expressar no código.</span><span class="sxs-lookup"><span data-stu-id="0f53f-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="0f53f-182">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua política com o `RequireAssertion` Policy Builder.</span><span class="sxs-lookup"><span data-stu-id="0f53f-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="0f53f-183">Por exemplo, o anterior `BadgeEntryHandler` poderia ser reescrito da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0f53f-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="0f53f-184">Acessar o contexto de solicitação MVC nos manipuladores</span><span class="sxs-lookup"><span data-stu-id="0f53f-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="0f53f-185">O `HandleRequirementAsync` método que você implementa em um manipulador de autorização tem dois parâmetros: um `AuthorizationHandlerContext` e o `TRequirement` que você está lidando.</span><span class="sxs-lookup"><span data-stu-id="0f53f-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="0f53f-186">Estruturas como o MVC ou SignalR são livres para adicionar qualquer objeto à `Resource` propriedade no `AuthorizationHandlerContext` para passar informações extras.</span><span class="sxs-lookup"><span data-stu-id="0f53f-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="0f53f-187">Ao usar o roteamento de ponto de extremidade, a autorização normalmente é manipulada pelo middleware de autorização.</span><span class="sxs-lookup"><span data-stu-id="0f53f-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="0f53f-188">Nesse caso, a `Resource` propriedade é uma instância do <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="0f53f-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="0f53f-189">O ponto de extremidade pode ser usado para investigar o recurso subjacente ao qual você está encaminhando.</span><span class="sxs-lookup"><span data-stu-id="0f53f-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="0f53f-190">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f53f-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="0f53f-191">O ponto de extremidade não fornece acesso ao atual `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="0f53f-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="0f53f-192">Ao usar o roteamento de ponto de extremidade, use `IHttpContextAcessor` para acessar `HttpContext` dentro de um manipulador de autorização.</span><span class="sxs-lookup"><span data-stu-id="0f53f-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="0f53f-193">Para obter mais informações, consulte [usar HttpContext de componentes personalizados](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="0f53f-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="0f53f-194">Com o roteamento tradicional, ou quando a autorização acontece como parte do filtro de autorização do MVC, o valor de `Resource` é uma <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instância.</span><span class="sxs-lookup"><span data-stu-id="0f53f-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="0f53f-195">Essa propriedade fornece acesso a `HttpContext` , `RouteData` e a todos os outros fornecidos pelo MVC e por Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="0f53f-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="0f53f-196">O uso da `Resource` propriedade é específico da estrutura.</span><span class="sxs-lookup"><span data-stu-id="0f53f-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="0f53f-197">O uso de informações na `Resource` Propriedade limita suas políticas de autorização a estruturas específicas.</span><span class="sxs-lookup"><span data-stu-id="0f53f-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="0f53f-198">Você deve converter a `Resource` propriedade usando a `is` palavra-chave e, em seguida, confirmar se a conversão foi bem-sucedida para garantir que seu código não falhe com um `InvalidCastException` quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="0f53f-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0f53f-199">Sob os bastidores, a autorização [baseada em função](xref:security/authorization/roles) e a [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="0f53f-200">Esses blocos de construção dão suporte à expressão de avaliações de autorização no código.</span><span class="sxs-lookup"><span data-stu-id="0f53f-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="0f53f-201">O resultado é uma estrutura de autorização mais rica, reutilizável e que pode ser testada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="0f53f-202">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="0f53f-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="0f53f-203">Ele é registrado como parte da configuração do serviço de autorização, no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="0f53f-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="0f53f-204">No exemplo anterior, uma política "AtLeast21" é criada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="0f53f-205">Ele tem um requisito único &mdash; de uma idade mínima, que é fornecida como um parâmetro para o requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="0f53f-206">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="0f53f-206">IAuthorizationService</span></span> 

<span data-ttu-id="0f53f-207">O serviço primário que determina se a autorização é bem-sucedida é <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="0f53f-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="0f53f-208">O código anterior realça os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="0f53f-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="0f53f-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>é um serviço de marcador sem métodos e o mecanismo para controlar se a autorização é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="0f53f-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="0f53f-210">Cada uma <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> é responsável por verificar se os requisitos são atendidos:</span><span class="sxs-lookup"><span data-stu-id="0f53f-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="0f53f-211">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="0f53f-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="0f53f-212">O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:</span><span class="sxs-lookup"><span data-stu-id="0f53f-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="0f53f-213">O código a seguir mostra um típico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0f53f-213">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="0f53f-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` para autorização.</span><span class="sxs-lookup"><span data-stu-id="0f53f-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="0f53f-215">Aplicar políticas a controladores MVC</span><span class="sxs-lookup"><span data-stu-id="0f53f-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="0f53f-216">Se você estiver usando Razor páginas, consulte [aplicar políticas a Razor páginas](#apply-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="0f53f-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="0f53f-217">As políticas são aplicadas aos controladores usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="0f53f-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0f53f-218">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f53f-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="0f53f-219">Aplicar políticas a Razor páginas</span><span class="sxs-lookup"><span data-stu-id="0f53f-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="0f53f-220">As políticas são aplicadas às Razor páginas usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="0f53f-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0f53f-221">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f53f-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="0f53f-222">As políticas também podem ser aplicadas a Razor páginas usando uma [Convenção de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="0f53f-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="0f53f-223">Requisitos</span><span class="sxs-lookup"><span data-stu-id="0f53f-223">Requirements</span></span>

<span data-ttu-id="0f53f-224">Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="0f53f-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="0f53f-225">Em nossa política de "AtLeast21", o requisito é um único parâmetro &mdash; de idade mínima.</span><span class="sxs-lookup"><span data-stu-id="0f53f-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="0f53f-226">Um requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia.</span><span class="sxs-lookup"><span data-stu-id="0f53f-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="0f53f-227">Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0f53f-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="0f53f-228">Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos deverão ser aprovados para que a avaliação da política seja realizada com êxito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="0f53f-229">Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados em uma base **e** .</span><span class="sxs-lookup"><span data-stu-id="0f53f-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="0f53f-230">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="0f53f-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="0f53f-231">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="0f53f-231">Authorization handlers</span></span>

<span data-ttu-id="0f53f-232">Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="0f53f-233">O manipulador de autorização avalia os requisitos em relação a um [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornecido para determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="0f53f-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="0f53f-234">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="0f53f-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="0f53f-235">Um manipulador pode herdar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), em que `TRequirement` é o requisito a ser manipulado.</span><span class="sxs-lookup"><span data-stu-id="0f53f-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="0f53f-236">Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="0f53f-237">Usar um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="0f53f-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="0f53f-238">Veja a seguir um exemplo de uma relação um-para-um na qual um manipulador de idade mínima utiliza um único requisito:</span><span class="sxs-lookup"><span data-stu-id="0f53f-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="0f53f-239">O código anterior determina se a entidade de usuário atual tem uma declaração de data de nascimento que foi emitida por um emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="0f53f-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="0f53f-240">A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída é retornada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="0f53f-241">Quando uma declaração está presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="0f53f-242">Se o usuário atender à idade mínima definida pelo requisito, a autorização será considerada bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="0f53f-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="0f53f-243">Quando a autorização é bem-sucedida, `context.Succeed` é invocada com o requisito atendido como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0f53f-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="0f53f-244">Usar um manipulador para vários requisitos</span><span class="sxs-lookup"><span data-stu-id="0f53f-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="0f53f-245">Veja a seguir um exemplo de uma relação um-para-muitos na qual um manipulador de permissão pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="0f53f-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="0f53f-246">O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; uma propriedade que contém os requisitos não marcados como bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="0f53f-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="0f53f-247">Para um `ReadPermission` requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="0f53f-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="0f53f-248">No caso de um `EditPermission` requisito ou `DeletePermission` , ele deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="0f53f-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="0f53f-249">Registro de manipulador</span><span class="sxs-lookup"><span data-stu-id="0f53f-249">Handler registration</span></span>

<span data-ttu-id="0f53f-250">Os manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="0f53f-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="0f53f-251">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f53f-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="0f53f-252">O código anterior é registrado `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="0f53f-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="0f53f-253">Os manipuladores podem ser registrados usando qualquer tempo de vida de [serviço](xref:fundamentals/dependency-injection#service-lifetimes)interno.</span><span class="sxs-lookup"><span data-stu-id="0f53f-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="0f53f-254">O que um manipulador deve retornar?</span><span class="sxs-lookup"><span data-stu-id="0f53f-254">What should a handler return?</span></span>

<span data-ttu-id="0f53f-255">Observe que o `Handle` método no [exemplo do manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="0f53f-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="0f53f-256">Como é indicado um status de êxito ou falha?</span><span class="sxs-lookup"><span data-stu-id="0f53f-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="0f53f-257">Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)` , passando o requisito que foi validado com êxito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="0f53f-258">Um manipulador não precisa lidar com falhas geralmente, já que outros manipuladores para o mesmo requisito podem ser bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="0f53f-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="0f53f-259">Para garantir a falha, mesmo que outros manipuladores de requisitos tenham êxito, chame `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="0f53f-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="0f53f-260">Se um manipulador chamar `context.Succeed` ou `context.Fail` , todos os outros manipuladores ainda serão chamados.</span><span class="sxs-lookup"><span data-stu-id="0f53f-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="0f53f-261">Isso permite que os requisitos produzam efeitos colaterais, como registro em log, que ocorre mesmo se outro manipulador tiver validado ou reprovado com êxito um requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="0f53f-262">Quando definido como `false` , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Core 1,1 e posterior) circuitos curtos a execução de manipuladores quando `context.Fail` é chamada.</span><span class="sxs-lookup"><span data-stu-id="0f53f-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="0f53f-263">`InvokeHandlersAfterFailure``true`o padrão é, nesse caso, todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="0f53f-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="0f53f-264">Os manipuladores de autorização são chamados mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="0f53f-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="0f53f-265">Por que desejo vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="0f53f-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="0f53f-266">Nos casos em que você deseja que a avaliação seja em um **ou** base, implemente vários manipuladores para um único requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="0f53f-267">Por exemplo, a Microsoft tem portas que só são abertas com cartões-chave.</span><span class="sxs-lookup"><span data-stu-id="0f53f-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="0f53f-268">Se você deixar o cartão-chave em casa, o recepcionista imprime um adesivo temporário e abre a porta para você.</span><span class="sxs-lookup"><span data-stu-id="0f53f-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="0f53f-269">Nesse cenário, você teria um único requisito, *BuildingEntry*, mas vários manipuladores, cada um examinando um único requisito.</span><span class="sxs-lookup"><span data-stu-id="0f53f-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="0f53f-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="0f53f-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="0f53f-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0f53f-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="0f53f-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0f53f-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="0f53f-273">Verifique se ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="0f53f-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="0f53f-274">Se um dos manipuladores for executado com sucesso quando uma política avaliar o `BuildingEntryRequirement` , a avaliação da política será realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="0f53f-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="0f53f-275">Usar um Func para atender a uma política</span><span class="sxs-lookup"><span data-stu-id="0f53f-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="0f53f-276">Pode haver situações em que o cumprimento de uma política é simples de expressar no código.</span><span class="sxs-lookup"><span data-stu-id="0f53f-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="0f53f-277">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua política com o `RequireAssertion` Policy Builder.</span><span class="sxs-lookup"><span data-stu-id="0f53f-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="0f53f-278">Por exemplo, o anterior `BadgeEntryHandler` poderia ser reescrito da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0f53f-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="0f53f-279">Acessar o contexto de solicitação MVC nos manipuladores</span><span class="sxs-lookup"><span data-stu-id="0f53f-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="0f53f-280">O `HandleRequirementAsync` método que você implementa em um manipulador de autorização tem dois parâmetros: um `AuthorizationHandlerContext` e o `TRequirement` que você está lidando.</span><span class="sxs-lookup"><span data-stu-id="0f53f-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="0f53f-281">Estruturas como o MVC ou SignalR são livres para adicionar qualquer objeto à `Resource` propriedade no `AuthorizationHandlerContext` para passar informações extras.</span><span class="sxs-lookup"><span data-stu-id="0f53f-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="0f53f-282">Por exemplo, o MVC passa uma instância de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) na `Resource` propriedade.</span><span class="sxs-lookup"><span data-stu-id="0f53f-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="0f53f-283">Essa propriedade fornece acesso a `HttpContext` , `RouteData` e a todos os outros fornecidos pelo MVC e por Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="0f53f-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="0f53f-284">O uso da `Resource` propriedade é específico da estrutura.</span><span class="sxs-lookup"><span data-stu-id="0f53f-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="0f53f-285">O uso de informações na `Resource` Propriedade limita suas políticas de autorização a estruturas específicas.</span><span class="sxs-lookup"><span data-stu-id="0f53f-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="0f53f-286">Você deve converter a `Resource` propriedade usando a `is` palavra-chave e, em seguida, confirmar se a conversão foi bem-sucedida para garantir que seu código não falhe com um `InvalidCastException` quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="0f53f-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
