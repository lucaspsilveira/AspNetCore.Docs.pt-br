---
title: Autorização baseada em políticas no Núcleo ASP.NET
author: rick-anderson
description: Saiba como criar e usar manipuladores de políticas de autorização para aplicar os requisitos de autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488755"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="afb3c-103">Autorização baseada em políticas no Núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="afb3c-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afb3c-104">Abaixo das capas, [a autorização baseada em papéis](xref:security/authorization/roles) e a autorização baseada em [sinistros](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="afb3c-105">Esses blocos de construção suportam a expressão de avaliações de autorização em código.</span><span class="sxs-lookup"><span data-stu-id="afb3c-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="afb3c-106">O resultado é uma estrutura de autorização mais rica, reutilizável e testável.</span><span class="sxs-lookup"><span data-stu-id="afb3c-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="afb3c-107">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="afb3c-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="afb3c-108">Está registrado como parte da configuração do `Startup.ConfigureServices` serviço de autorização, no método:</span><span class="sxs-lookup"><span data-stu-id="afb3c-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="afb3c-109">No exemplo anterior, uma política "Pelo menos21" é criada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="afb3c-110">Tem um único&mdash;requisito que de uma idade mínima, que é fornecido como parâmetro para a exigência.</span><span class="sxs-lookup"><span data-stu-id="afb3c-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="afb3c-111">Serviço de IAutorização</span><span class="sxs-lookup"><span data-stu-id="afb3c-111">IAuthorizationService</span></span> 

<span data-ttu-id="afb3c-112">O serviço principal que determina se <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>a autorização é bem sucedida é:</span><span class="sxs-lookup"><span data-stu-id="afb3c-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="afb3c-113">O código anterior destaca os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="afb3c-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="afb3c-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>é um serviço marcador sem métodos, e o mecanismo para rastrear se a autorização é bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="afb3c-115">Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> um é responsável por verificar se os requisitos são atendidos:</span><span class="sxs-lookup"><span data-stu-id="afb3c-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="afb3c-116">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="afb3c-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="afb3c-117">O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:</span><span class="sxs-lookup"><span data-stu-id="afb3c-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="afb3c-118">O código a `ConfigureServices`seguir mostra um típico:</span><span class="sxs-lookup"><span data-stu-id="afb3c-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="afb3c-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou para autorização.</span><span class="sxs-lookup"><span data-stu-id="afb3c-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="afb3c-120">Aplicação de políticas aos controladores MVC</span><span class="sxs-lookup"><span data-stu-id="afb3c-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="afb3c-121">Se você estiver usando páginas de barbear, consulte [Aplicar políticas a Páginas de Barbear](#applying-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="afb3c-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="afb3c-122">As políticas são aplicadas aos `[Authorize]` controladores usando o atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="afb3c-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="afb3c-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="afb3c-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="afb3c-124">Aplicação de políticas em Páginas de Barbear</span><span class="sxs-lookup"><span data-stu-id="afb3c-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="afb3c-125">As políticas são aplicadas às `[Authorize]` Páginas de Navalha usando o atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="afb3c-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="afb3c-126">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="afb3c-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="afb3c-127">As políticas também podem ser aplicadas às Páginas de Navalha usando uma [convenção de autorização.](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="afb3c-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="afb3c-128">Requisitos</span><span class="sxs-lookup"><span data-stu-id="afb3c-128">Requirements</span></span>

<span data-ttu-id="afb3c-129">Um requisito de autorização é uma coleta de parâmetros de dados que uma política pode usar para avaliar o principal usuário atual.</span><span class="sxs-lookup"><span data-stu-id="afb3c-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="afb3c-130">Em nossa política "Pelo menos21", a exigência&mdash;é um único parâmetro da idade mínima.</span><span class="sxs-lookup"><span data-stu-id="afb3c-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="afb3c-131">Um requisito implementa [iAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia.</span><span class="sxs-lookup"><span data-stu-id="afb3c-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="afb3c-132">Um requisito de idade mínima parametrizado poderia ser implementado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="afb3c-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="afb3c-133">Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos devem ser aprovados para que a avaliação da política seja bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="afb3c-134">Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados com base em **And.**</span><span class="sxs-lookup"><span data-stu-id="afb3c-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="afb3c-135">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="afb3c-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="afb3c-136">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="afb3c-136">Authorization handlers</span></span>

<span data-ttu-id="afb3c-137">Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="afb3c-138">O manipulador de autorização avalia os requisitos em relação a um [Site de AutorizaçãoDeHandlerpara](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="afb3c-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="afb3c-139">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="afb3c-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="afb3c-140">Um manipulador pode herdar o `TRequirement` [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)onde está a exigência a ser tratada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="afb3c-141">Alternativamente, um manipulador pode implementar [o IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="afb3c-142">Use um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="afb3c-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="afb3c-143">A seguir, um exemplo de uma relação um-para-um em que um manipulador de idade mínima utiliza um único requisito:</span><span class="sxs-lookup"><span data-stu-id="afb3c-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="afb3c-144">O código anterior determina se o atual principal do usuário tem uma declaração de data de nascimento que foi emitida por um Emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="afb3c-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="afb3c-145">A autorização não pode ocorrer quando a reclamação está faltando, nesse caso uma tarefa concluída é devolvida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="afb3c-146">Quando uma reclamação está presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="afb3c-147">Se o usuário cumprir a idade mínima definida pelo requisito, a autorização será considerada bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="afb3c-148">Quando a autorização `context.Succeed` é bem sucedida, é invocado com o requisito satisfeito como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="afb3c-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="afb3c-149">Use um manipulador para vários requisitos</span><span class="sxs-lookup"><span data-stu-id="afb3c-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="afb3c-150">A seguir, um exemplo de um relacionamento de um a muitos em que um manipulador de permissões pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="afb3c-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="afb3c-151">O código anterior atravessa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém requisitos não marcados como bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="afb3c-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="afb3c-152">Para `ReadPermission` um requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="afb3c-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="afb3c-153">Em caso de `EditPermission` `DeletePermission` exigência ou exigência, ele ou ela deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="afb3c-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="afb3c-154">Registro do manipulador</span><span class="sxs-lookup"><span data-stu-id="afb3c-154">Handler registration</span></span>

<span data-ttu-id="afb3c-155">Os manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="afb3c-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="afb3c-156">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="afb3c-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="afb3c-157">O código precedente `MinimumAgeHandler` registra-se como `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton invocando .</span><span class="sxs-lookup"><span data-stu-id="afb3c-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="afb3c-158">Os manipuladores podem ser registrados usando qualquer uma das [vidas de serviço incorporadas](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="afb3c-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="afb3c-159">O que um manipulador deve retornar?</span><span class="sxs-lookup"><span data-stu-id="afb3c-159">What should a handler return?</span></span>

<span data-ttu-id="afb3c-160">Observe que `Handle` o método no exemplo do [manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="afb3c-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="afb3c-161">Como é indicado um status de sucesso ou fracasso?</span><span class="sxs-lookup"><span data-stu-id="afb3c-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="afb3c-162">Um manipulador indica `context.Succeed(IAuthorizationRequirement requirement)`o sucesso chamando, passando o requisito que foi validado com sucesso.</span><span class="sxs-lookup"><span data-stu-id="afb3c-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="afb3c-163">Um manipulador não precisa lidar com falhas em geral, pois outros manipuladores para o mesmo requisito podem ter sucesso.</span><span class="sxs-lookup"><span data-stu-id="afb3c-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="afb3c-164">Para garantir a falha, mesmo que `context.Fail`outros manipuladores de requisitos tenham sucesso, ligue .</span><span class="sxs-lookup"><span data-stu-id="afb3c-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="afb3c-165">Se um `context.Succeed` manipulador `context.Fail`ligar ou , todos os outros manipuladores ainda são chamados.</span><span class="sxs-lookup"><span data-stu-id="afb3c-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="afb3c-166">Isso permite que os requisitos produzam efeitos colaterais, como o registro, que ocorre mesmo que outro manipulador tenha validado ou falhado com sucesso em um requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="afb3c-167">Quando definido `false`para , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Núcleo 1.1 `context.Fail` e posterior) faz curto-circuito na execução dos manipuladores quando é chamada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="afb3c-168">`InvokeHandlersAfterFailure`padrão para `true`, nesse caso todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="afb3c-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="afb3c-169">Os manipuladores de autorização são chamados mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="afb3c-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="afb3c-170">Por que eu iria querer vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="afb3c-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="afb3c-171">Nos casos em que você deseja que a avaliação seja em uma **base DE,** implemente vários manipuladores para um único requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="afb3c-172">Por exemplo, a Microsoft tem portas que só abrem com cartões-chave.</span><span class="sxs-lookup"><span data-stu-id="afb3c-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="afb3c-173">Se você deixar seu cartão-chave em casa, a recepcionista imprime um adesivo temporário e abre a porta para você.</span><span class="sxs-lookup"><span data-stu-id="afb3c-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="afb3c-174">Neste cenário, você teria um único requisito, *BuildingEntry,* mas vários manipuladores, cada um examinando um único requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="afb3c-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="afb3c-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="afb3c-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="afb3c-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="afb3c-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="afb3c-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="afb3c-178">Certifique-se de que ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="afb3c-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="afb3c-179">Se qualquer um dos manipuladores `BuildingEntryRequirement`tiver sucesso quando uma política avaliar o , a avaliação da política será bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="afb3c-180">Usando um func para cumprir uma política</span><span class="sxs-lookup"><span data-stu-id="afb3c-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="afb3c-181">Pode haver situações em que o cumprimento de uma política é simples de expressar em código.</span><span class="sxs-lookup"><span data-stu-id="afb3c-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="afb3c-182">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua `RequireAssertion` política com o construtor de políticas.</span><span class="sxs-lookup"><span data-stu-id="afb3c-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="afb3c-183">Por exemplo, `BadgeEntryHandler` o anterior pode ser reescrito da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="afb3c-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="afb3c-184">Acessando o contexto de solicitação de MVC em manipuladores</span><span class="sxs-lookup"><span data-stu-id="afb3c-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="afb3c-185">O `HandleRequirementAsync` método que você implementa em `AuthorizationHandlerContext` um `TRequirement` manipulador de autorização tem dois parâmetros: um e o que você está manipulando.</span><span class="sxs-lookup"><span data-stu-id="afb3c-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="afb3c-186">Frameworks como MVC ou Jabbr são livres `Resource` para adicionar `AuthorizationHandlerContext` qualquer objeto à propriedade no para passar informações extras.</span><span class="sxs-lookup"><span data-stu-id="afb3c-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="afb3c-187">Por exemplo, o MVC passa uma `Resource` instância de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) na propriedade.</span><span class="sxs-lookup"><span data-stu-id="afb3c-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="afb3c-188">Esta propriedade fornece `HttpContext` `RouteData`acesso a , e todo o resto fornecido por MVC e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="afb3c-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="afb3c-189">O uso `Resource` da propriedade é específico da estrutura.</span><span class="sxs-lookup"><span data-stu-id="afb3c-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="afb3c-190">O uso `Resource` de informações na propriedade limita suas políticas de autorização a estruturas específicas.</span><span class="sxs-lookup"><span data-stu-id="afb3c-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="afb3c-191">Você deve `Resource` lançar a `is` propriedade usando a palavra-chave e, em seguida, confirmar `InvalidCastException` que o elenco conseguiu garantir que seu código não caia com um quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="afb3c-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="afb3c-192">Abaixo das capas, [a autorização baseada em papéis](xref:security/authorization/roles) e a autorização baseada em [sinistros](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="afb3c-193">Esses blocos de construção suportam a expressão de avaliações de autorização em código.</span><span class="sxs-lookup"><span data-stu-id="afb3c-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="afb3c-194">O resultado é uma estrutura de autorização mais rica, reutilizável e testável.</span><span class="sxs-lookup"><span data-stu-id="afb3c-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="afb3c-195">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="afb3c-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="afb3c-196">Está registrado como parte da configuração do `Startup.ConfigureServices` serviço de autorização, no método:</span><span class="sxs-lookup"><span data-stu-id="afb3c-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="afb3c-197">No exemplo anterior, uma política "Pelo menos21" é criada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="afb3c-198">Tem um único&mdash;requisito que de uma idade mínima, que é fornecido como parâmetro para a exigência.</span><span class="sxs-lookup"><span data-stu-id="afb3c-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="afb3c-199">Serviço de IAutorização</span><span class="sxs-lookup"><span data-stu-id="afb3c-199">IAuthorizationService</span></span> 

<span data-ttu-id="afb3c-200">O serviço principal que determina se <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>a autorização é bem sucedida é:</span><span class="sxs-lookup"><span data-stu-id="afb3c-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="afb3c-201">O código anterior destaca os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="afb3c-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="afb3c-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>é um serviço marcador sem métodos, e o mecanismo para rastrear se a autorização é bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="afb3c-203">Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> um é responsável por verificar se os requisitos são atendidos:</span><span class="sxs-lookup"><span data-stu-id="afb3c-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="afb3c-204">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="afb3c-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="afb3c-205">O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:</span><span class="sxs-lookup"><span data-stu-id="afb3c-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="afb3c-206">O código a `ConfigureServices`seguir mostra um típico:</span><span class="sxs-lookup"><span data-stu-id="afb3c-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="afb3c-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou para autorização.</span><span class="sxs-lookup"><span data-stu-id="afb3c-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="afb3c-208">Aplicação de políticas aos controladores MVC</span><span class="sxs-lookup"><span data-stu-id="afb3c-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="afb3c-209">Se você estiver usando páginas de barbear, consulte [Aplicar políticas a Páginas de Barbear](#applying-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="afb3c-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="afb3c-210">As políticas são aplicadas aos `[Authorize]` controladores usando o atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="afb3c-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="afb3c-211">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="afb3c-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="afb3c-212">Aplicação de políticas em Páginas de Barbear</span><span class="sxs-lookup"><span data-stu-id="afb3c-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="afb3c-213">As políticas são aplicadas às `[Authorize]` Páginas de Navalha usando o atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="afb3c-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="afb3c-214">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="afb3c-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="afb3c-215">As políticas também podem ser aplicadas às Páginas de Navalha usando uma [convenção de autorização.](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="afb3c-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="afb3c-216">Requisitos</span><span class="sxs-lookup"><span data-stu-id="afb3c-216">Requirements</span></span>

<span data-ttu-id="afb3c-217">Um requisito de autorização é uma coleta de parâmetros de dados que uma política pode usar para avaliar o principal usuário atual.</span><span class="sxs-lookup"><span data-stu-id="afb3c-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="afb3c-218">Em nossa política "Pelo menos21", a exigência&mdash;é um único parâmetro da idade mínima.</span><span class="sxs-lookup"><span data-stu-id="afb3c-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="afb3c-219">Um requisito implementa [iAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia.</span><span class="sxs-lookup"><span data-stu-id="afb3c-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="afb3c-220">Um requisito de idade mínima parametrizado poderia ser implementado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="afb3c-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="afb3c-221">Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos devem ser aprovados para que a avaliação da política seja bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="afb3c-222">Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados com base em **And.**</span><span class="sxs-lookup"><span data-stu-id="afb3c-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="afb3c-223">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="afb3c-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="afb3c-224">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="afb3c-224">Authorization handlers</span></span>

<span data-ttu-id="afb3c-225">Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="afb3c-226">O manipulador de autorização avalia os requisitos em relação a um [Site de AutorizaçãoDeHandlerpara](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="afb3c-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="afb3c-227">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="afb3c-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="afb3c-228">Um manipulador pode herdar o `TRequirement` [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)onde está a exigência a ser tratada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="afb3c-229">Alternativamente, um manipulador pode implementar [o IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="afb3c-230">Use um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="afb3c-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="afb3c-231">A seguir, um exemplo de uma relação um-para-um em que um manipulador de idade mínima utiliza um único requisito:</span><span class="sxs-lookup"><span data-stu-id="afb3c-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="afb3c-232">O código anterior determina se o atual principal do usuário tem uma declaração de data de nascimento que foi emitida por um Emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="afb3c-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="afb3c-233">A autorização não pode ocorrer quando a reclamação está faltando, nesse caso uma tarefa concluída é devolvida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="afb3c-234">Quando uma reclamação está presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="afb3c-235">Se o usuário cumprir a idade mínima definida pelo requisito, a autorização será considerada bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="afb3c-236">Quando a autorização `context.Succeed` é bem sucedida, é invocado com o requisito satisfeito como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="afb3c-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="afb3c-237">Use um manipulador para vários requisitos</span><span class="sxs-lookup"><span data-stu-id="afb3c-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="afb3c-238">A seguir, um exemplo de um relacionamento de um a muitos em que um manipulador de permissões pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="afb3c-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="afb3c-239">O código anterior atravessa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém requisitos não marcados como bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="afb3c-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="afb3c-240">Para `ReadPermission` um requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="afb3c-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="afb3c-241">Em caso de `EditPermission` `DeletePermission` exigência ou exigência, ele ou ela deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="afb3c-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="afb3c-242">Registro do manipulador</span><span class="sxs-lookup"><span data-stu-id="afb3c-242">Handler registration</span></span>

<span data-ttu-id="afb3c-243">Os manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="afb3c-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="afb3c-244">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="afb3c-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="afb3c-245">O código precedente `MinimumAgeHandler` registra-se como `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton invocando .</span><span class="sxs-lookup"><span data-stu-id="afb3c-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="afb3c-246">Os manipuladores podem ser registrados usando qualquer uma das [vidas de serviço incorporadas](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="afb3c-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="afb3c-247">O que um manipulador deve retornar?</span><span class="sxs-lookup"><span data-stu-id="afb3c-247">What should a handler return?</span></span>

<span data-ttu-id="afb3c-248">Observe que `Handle` o método no exemplo do [manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="afb3c-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="afb3c-249">Como é indicado um status de sucesso ou fracasso?</span><span class="sxs-lookup"><span data-stu-id="afb3c-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="afb3c-250">Um manipulador indica `context.Succeed(IAuthorizationRequirement requirement)`o sucesso chamando, passando o requisito que foi validado com sucesso.</span><span class="sxs-lookup"><span data-stu-id="afb3c-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="afb3c-251">Um manipulador não precisa lidar com falhas em geral, pois outros manipuladores para o mesmo requisito podem ter sucesso.</span><span class="sxs-lookup"><span data-stu-id="afb3c-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="afb3c-252">Para garantir a falha, mesmo que `context.Fail`outros manipuladores de requisitos tenham sucesso, ligue .</span><span class="sxs-lookup"><span data-stu-id="afb3c-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="afb3c-253">Se um `context.Succeed` manipulador `context.Fail`ligar ou , todos os outros manipuladores ainda são chamados.</span><span class="sxs-lookup"><span data-stu-id="afb3c-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="afb3c-254">Isso permite que os requisitos produzam efeitos colaterais, como o registro, que ocorre mesmo que outro manipulador tenha validado ou falhado com sucesso em um requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="afb3c-255">Quando definido `false`para , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Núcleo 1.1 `context.Fail` e posterior) faz curto-circuito na execução dos manipuladores quando é chamada.</span><span class="sxs-lookup"><span data-stu-id="afb3c-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="afb3c-256">`InvokeHandlersAfterFailure`padrão para `true`, nesse caso todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="afb3c-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="afb3c-257">Os manipuladores de autorização são chamados mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="afb3c-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="afb3c-258">Por que eu iria querer vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="afb3c-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="afb3c-259">Nos casos em que você deseja que a avaliação seja em uma **base DE,** implemente vários manipuladores para um único requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="afb3c-260">Por exemplo, a Microsoft tem portas que só abrem com cartões-chave.</span><span class="sxs-lookup"><span data-stu-id="afb3c-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="afb3c-261">Se você deixar seu cartão-chave em casa, a recepcionista imprime um adesivo temporário e abre a porta para você.</span><span class="sxs-lookup"><span data-stu-id="afb3c-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="afb3c-262">Neste cenário, você teria um único requisito, *BuildingEntry,* mas vários manipuladores, cada um examinando um único requisito.</span><span class="sxs-lookup"><span data-stu-id="afb3c-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="afb3c-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="afb3c-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="afb3c-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="afb3c-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="afb3c-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="afb3c-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="afb3c-266">Certifique-se de que ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="afb3c-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="afb3c-267">Se qualquer um dos manipuladores `BuildingEntryRequirement`tiver sucesso quando uma política avaliar o , a avaliação da política será bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="afb3c-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="afb3c-268">Usando um func para cumprir uma política</span><span class="sxs-lookup"><span data-stu-id="afb3c-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="afb3c-269">Pode haver situações em que o cumprimento de uma política é simples de expressar em código.</span><span class="sxs-lookup"><span data-stu-id="afb3c-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="afb3c-270">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua `RequireAssertion` política com o construtor de políticas.</span><span class="sxs-lookup"><span data-stu-id="afb3c-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="afb3c-271">Por exemplo, `BadgeEntryHandler` o anterior pode ser reescrito da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="afb3c-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="afb3c-272">Acessando o contexto de solicitação de MVC em manipuladores</span><span class="sxs-lookup"><span data-stu-id="afb3c-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="afb3c-273">O `HandleRequirementAsync` método que você implementa em `AuthorizationHandlerContext` um `TRequirement` manipulador de autorização tem dois parâmetros: um e o que você está manipulando.</span><span class="sxs-lookup"><span data-stu-id="afb3c-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="afb3c-274">Frameworks como MVC ou SignalR são livres `Resource` para adicionar `AuthorizationHandlerContext` qualquer objeto à propriedade no para passar informações extras.</span><span class="sxs-lookup"><span data-stu-id="afb3c-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="afb3c-275">Ao usar o roteamento de ponto final, a autorização é normalmente tratada pelo Middleware de autorização.</span><span class="sxs-lookup"><span data-stu-id="afb3c-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="afb3c-276">Neste caso, `Resource` a propriedade é <xref:Microsoft.AspNetCore.Http.Endpoint>uma instância de .</span><span class="sxs-lookup"><span data-stu-id="afb3c-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="afb3c-277">O ponto final pode ser usado para sondar o recurso subjacente ao qual você está roteando.</span><span class="sxs-lookup"><span data-stu-id="afb3c-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="afb3c-278">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="afb3c-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="afb3c-279">Com o roteamento tradicional, ou quando a autorização acontece como `Resource` parte <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> do filtro de autorização da MVC, o valor é uma instância.</span><span class="sxs-lookup"><span data-stu-id="afb3c-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="afb3c-280">Esta propriedade fornece `HttpContext` `RouteData`acesso a , e todo o resto fornecido por MVC e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="afb3c-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="afb3c-281">O uso `Resource` da propriedade é específico da estrutura.</span><span class="sxs-lookup"><span data-stu-id="afb3c-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="afb3c-282">O uso `Resource` de informações na propriedade limita suas políticas de autorização a estruturas específicas.</span><span class="sxs-lookup"><span data-stu-id="afb3c-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="afb3c-283">Você deve `Resource` lançar a `is` propriedade usando a palavra-chave e, em seguida, confirmar `InvalidCastException` que o elenco conseguiu garantir que seu código não caia com um quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="afb3c-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
