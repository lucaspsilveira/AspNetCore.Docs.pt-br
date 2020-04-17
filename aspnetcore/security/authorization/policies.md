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
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorização baseada em políticas no Núcleo ASP.NET

::: moniker range=">= aspnetcore-3.0"

Abaixo das capas, [a autorização baseada em papéis](xref:security/authorization/roles) e a autorização baseada em [sinistros](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada. Esses blocos de construção suportam a expressão de avaliações de autorização em código. O resultado é uma estrutura de autorização mais rica, reutilizável e testável.

Uma política de autorização consiste em um ou mais requisitos. Está registrado como parte da configuração do `Startup.ConfigureServices` serviço de autorização, no método:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

No exemplo anterior, uma política "Pelo menos21" é criada. Tem um único&mdash;requisito que de uma idade mínima, que é fornecido como parâmetro para a exigência.

## <a name="iauthorizationservice"></a>Serviço de IAutorização 

O serviço principal que determina se <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>a autorização é bem sucedida é:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

O código anterior destaca os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>é um serviço marcador sem métodos, e o mecanismo para rastrear se a autorização é bem sucedida.

Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> um é responsável por verificar se os requisitos são atendidos:
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

A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:

```csharp
 context.Succeed(requirement)
```

O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:

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

O código a `ConfigureServices`seguir mostra um típico:

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

Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou para autorização.

## <a name="applying-policies-to-mvc-controllers"></a>Aplicação de políticas aos controladores MVC

Se você estiver usando páginas de barbear, consulte [Aplicar políticas a Páginas de Barbear](#applying-policies-to-razor-pages) neste documento.

As políticas são aplicadas aos `[Authorize]` controladores usando o atributo com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Aplicação de políticas em Páginas de Barbear

As políticas são aplicadas às `[Authorize]` Páginas de Navalha usando o atributo com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

As políticas também podem ser aplicadas às Páginas de Navalha usando uma [convenção de autorização.](xref:security/authorization/razor-pages-authorization)

## <a name="requirements"></a>Requisitos

Um requisito de autorização é uma coleta de parâmetros de dados que uma política pode usar para avaliar o principal usuário atual. Em nossa política "Pelo menos21", a exigência&mdash;é um único parâmetro da idade mínima. Um requisito implementa [iAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia. Um requisito de idade mínima parametrizado poderia ser implementado da seguinte forma:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos devem ser aprovados para que a avaliação da política seja bem sucedida. Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados com base em **And.**

> [!NOTE]
> Um requisito não precisa ter dados ou propriedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Manipuladores de autorização

Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito. O manipulador de autorização avalia os requisitos em relação a um [Site de AutorizaçãoDeHandlerpara](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) determinar se o acesso é permitido.

Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers). Um manipulador pode herdar o `TRequirement` [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)onde está a exigência a ser tratada. Alternativamente, um manipulador pode implementar [o IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Use um manipulador para um requisito

<a name="security-authorization-handler-example"></a>

A seguir, um exemplo de uma relação um-para-um em que um manipulador de idade mínima utiliza um único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

O código anterior determina se o atual principal do usuário tem uma declaração de data de nascimento que foi emitida por um Emissor conhecido e confiável. A autorização não pode ocorrer quando a reclamação está faltando, nesse caso uma tarefa concluída é devolvida. Quando uma reclamação está presente, a idade do usuário é calculada. Se o usuário cumprir a idade mínima definida pelo requisito, a autorização será considerada bem sucedida. Quando a autorização `context.Succeed` é bem sucedida, é invocado com o requisito satisfeito como seu único parâmetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Use um manipulador para vários requisitos

A seguir, um exemplo de um relacionamento de um a muitos em que um manipulador de permissões pode lidar com três tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

O código anterior atravessa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém requisitos não marcados como bem-sucedidos. Para `ReadPermission` um requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado. Em caso de `EditPermission` `DeletePermission` exigência ou exigência, ele ou ela deve ser um proprietário para acessar o recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro do manipulador

Os manipuladores são registrados na coleção de serviços durante a configuração. Por exemplo:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

O código precedente `MinimumAgeHandler` registra-se como `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton invocando . Os manipuladores podem ser registrados usando qualquer uma das [vidas de serviço incorporadas](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>O que um manipulador deve retornar?

Observe que `Handle` o método no exemplo do [manipulador](#security-authorization-handler-example) não retorna nenhum valor. Como é indicado um status de sucesso ou fracasso?

* Um manipulador indica `context.Succeed(IAuthorizationRequirement requirement)`o sucesso chamando, passando o requisito que foi validado com sucesso.

* Um manipulador não precisa lidar com falhas em geral, pois outros manipuladores para o mesmo requisito podem ter sucesso.

* Para garantir a falha, mesmo que `context.Fail`outros manipuladores de requisitos tenham sucesso, ligue .

Se um `context.Succeed` manipulador `context.Fail`ligar ou , todos os outros manipuladores ainda são chamados. Isso permite que os requisitos produzam efeitos colaterais, como o registro, que ocorre mesmo que outro manipulador tenha validado ou falhado com sucesso em um requisito. Quando definido `false`para , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Núcleo 1.1 `context.Fail` e posterior) faz curto-circuito na execução dos manipuladores quando é chamada. `InvokeHandlersAfterFailure`padrão para `true`, nesse caso todos os manipuladores são chamados.

> [!NOTE]
> Os manipuladores de autorização são chamados mesmo se a autenticação falhar.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Por que eu iria querer vários manipuladores para um requisito?

Nos casos em que você deseja que a avaliação seja em uma **base DE,** implemente vários manipuladores para um único requisito. Por exemplo, a Microsoft tem portas que só abrem com cartões-chave. Se você deixar seu cartão-chave em casa, a recepcionista imprime um adesivo temporário e abre a porta para você. Neste cenário, você teria um único requisito, *BuildingEntry,* mas vários manipuladores, cada um examinando um único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Certifique-se de que ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Se qualquer um dos manipuladores `BuildingEntryRequirement`tiver sucesso quando uma política avaliar o , a avaliação da política será bem sucedida.

## <a name="using-a-func-to-fulfill-a-policy"></a>Usando um func para cumprir uma política

Pode haver situações em que o cumprimento de uma política é simples de expressar em código. É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua `RequireAssertion` política com o construtor de políticas.

Por exemplo, `BadgeEntryHandler` o anterior pode ser reescrito da seguinte forma:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Acessando o contexto de solicitação de MVC em manipuladores

O `HandleRequirementAsync` método que você implementa em `AuthorizationHandlerContext` um `TRequirement` manipulador de autorização tem dois parâmetros: um e o que você está manipulando. Frameworks como MVC ou Jabbr são livres `Resource` para adicionar `AuthorizationHandlerContext` qualquer objeto à propriedade no para passar informações extras.

Por exemplo, o MVC passa uma `Resource` instância de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) na propriedade. Esta propriedade fornece `HttpContext` `RouteData`acesso a , e todo o resto fornecido por MVC e Razor Pages.

O uso `Resource` da propriedade é específico da estrutura. O uso `Resource` de informações na propriedade limita suas políticas de autorização a estruturas específicas. Você deve `Resource` lançar a `is` propriedade usando a palavra-chave e, em seguida, confirmar `InvalidCastException` que o elenco conseguiu garantir que seu código não caia com um quando executado em outras estruturas:

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

Abaixo das capas, [a autorização baseada em papéis](xref:security/authorization/roles) e a autorização baseada em [sinistros](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada. Esses blocos de construção suportam a expressão de avaliações de autorização em código. O resultado é uma estrutura de autorização mais rica, reutilizável e testável.

Uma política de autorização consiste em um ou mais requisitos. Está registrado como parte da configuração do `Startup.ConfigureServices` serviço de autorização, no método:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

No exemplo anterior, uma política "Pelo menos21" é criada. Tem um único&mdash;requisito que de uma idade mínima, que é fornecido como parâmetro para a exigência.

## <a name="iauthorizationservice"></a>Serviço de IAutorização 

O serviço principal que determina se <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>a autorização é bem sucedida é:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

O código anterior destaca os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>é um serviço marcador sem métodos, e o mecanismo para rastrear se a autorização é bem sucedida.

Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> um é responsável por verificar se os requisitos são atendidos:
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

A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:

```csharp
 context.Succeed(requirement)
```

O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:

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

O código a `ConfigureServices`seguir mostra um típico:

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

Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou para autorização.

## <a name="applying-policies-to-mvc-controllers"></a>Aplicação de políticas aos controladores MVC

Se você estiver usando páginas de barbear, consulte [Aplicar políticas a Páginas de Barbear](#applying-policies-to-razor-pages) neste documento.

As políticas são aplicadas aos `[Authorize]` controladores usando o atributo com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Aplicação de políticas em Páginas de Barbear

As políticas são aplicadas às `[Authorize]` Páginas de Navalha usando o atributo com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

As políticas também podem ser aplicadas às Páginas de Navalha usando uma [convenção de autorização.](xref:security/authorization/razor-pages-authorization)

## <a name="requirements"></a>Requisitos

Um requisito de autorização é uma coleta de parâmetros de dados que uma política pode usar para avaliar o principal usuário atual. Em nossa política "Pelo menos21", a exigência&mdash;é um único parâmetro da idade mínima. Um requisito implementa [iAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia. Um requisito de idade mínima parametrizado poderia ser implementado da seguinte forma:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos devem ser aprovados para que a avaliação da política seja bem sucedida. Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados com base em **And.**

> [!NOTE]
> Um requisito não precisa ter dados ou propriedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Manipuladores de autorização

Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito. O manipulador de autorização avalia os requisitos em relação a um [Site de AutorizaçãoDeHandlerpara](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) determinar se o acesso é permitido.

Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers). Um manipulador pode herdar o `TRequirement` [AuthorizationHandler\<TRequirement>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)onde está a exigência a ser tratada. Alternativamente, um manipulador pode implementar [o IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Use um manipulador para um requisito

<a name="security-authorization-handler-example"></a>

A seguir, um exemplo de uma relação um-para-um em que um manipulador de idade mínima utiliza um único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

O código anterior determina se o atual principal do usuário tem uma declaração de data de nascimento que foi emitida por um Emissor conhecido e confiável. A autorização não pode ocorrer quando a reclamação está faltando, nesse caso uma tarefa concluída é devolvida. Quando uma reclamação está presente, a idade do usuário é calculada. Se o usuário cumprir a idade mínima definida pelo requisito, a autorização será considerada bem sucedida. Quando a autorização `context.Succeed` é bem sucedida, é invocado com o requisito satisfeito como seu único parâmetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Use um manipulador para vários requisitos

A seguir, um exemplo de um relacionamento de um a muitos em que um manipulador de permissões pode lidar com três tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

O código anterior atravessa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém requisitos não marcados como bem-sucedidos. Para `ReadPermission` um requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado. Em caso de `EditPermission` `DeletePermission` exigência ou exigência, ele ou ela deve ser um proprietário para acessar o recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro do manipulador

Os manipuladores são registrados na coleção de serviços durante a configuração. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

O código precedente `MinimumAgeHandler` registra-se como `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton invocando . Os manipuladores podem ser registrados usando qualquer uma das [vidas de serviço incorporadas](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>O que um manipulador deve retornar?

Observe que `Handle` o método no exemplo do [manipulador](#security-authorization-handler-example) não retorna nenhum valor. Como é indicado um status de sucesso ou fracasso?

* Um manipulador indica `context.Succeed(IAuthorizationRequirement requirement)`o sucesso chamando, passando o requisito que foi validado com sucesso.

* Um manipulador não precisa lidar com falhas em geral, pois outros manipuladores para o mesmo requisito podem ter sucesso.

* Para garantir a falha, mesmo que `context.Fail`outros manipuladores de requisitos tenham sucesso, ligue .

Se um `context.Succeed` manipulador `context.Fail`ligar ou , todos os outros manipuladores ainda são chamados. Isso permite que os requisitos produzam efeitos colaterais, como o registro, que ocorre mesmo que outro manipulador tenha validado ou falhado com sucesso em um requisito. Quando definido `false`para , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Núcleo 1.1 `context.Fail` e posterior) faz curto-circuito na execução dos manipuladores quando é chamada. `InvokeHandlersAfterFailure`padrão para `true`, nesse caso todos os manipuladores são chamados.

> [!NOTE]
> Os manipuladores de autorização são chamados mesmo se a autenticação falhar.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Por que eu iria querer vários manipuladores para um requisito?

Nos casos em que você deseja que a avaliação seja em uma **base DE,** implemente vários manipuladores para um único requisito. Por exemplo, a Microsoft tem portas que só abrem com cartões-chave. Se você deixar seu cartão-chave em casa, a recepcionista imprime um adesivo temporário e abre a porta para você. Neste cenário, você teria um único requisito, *BuildingEntry,* mas vários manipuladores, cada um examinando um único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Certifique-se de que ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Se qualquer um dos manipuladores `BuildingEntryRequirement`tiver sucesso quando uma política avaliar o , a avaliação da política será bem sucedida.

## <a name="using-a-func-to-fulfill-a-policy"></a>Usando um func para cumprir uma política

Pode haver situações em que o cumprimento de uma política é simples de expressar em código. É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua `RequireAssertion` política com o construtor de políticas.

Por exemplo, `BadgeEntryHandler` o anterior pode ser reescrito da seguinte forma:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Acessando o contexto de solicitação de MVC em manipuladores

O `HandleRequirementAsync` método que você implementa em `AuthorizationHandlerContext` um `TRequirement` manipulador de autorização tem dois parâmetros: um e o que você está manipulando. Frameworks como MVC ou SignalR são livres `Resource` para adicionar `AuthorizationHandlerContext` qualquer objeto à propriedade no para passar informações extras.

Ao usar o roteamento de ponto final, a autorização é normalmente tratada pelo Middleware de autorização. Neste caso, `Resource` a propriedade é <xref:Microsoft.AspNetCore.Http.Endpoint>uma instância de . O ponto final pode ser usado para sondar o recurso subjacente ao qual você está roteando. Por exemplo:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Com o roteamento tradicional, ou quando a autorização acontece como `Resource` parte <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> do filtro de autorização da MVC, o valor é uma instância. Esta propriedade fornece `HttpContext` `RouteData`acesso a , e todo o resto fornecido por MVC e Razor Pages.

O uso `Resource` da propriedade é específico da estrutura. O uso `Resource` de informações na propriedade limita suas políticas de autorização a estruturas específicas. Você deve `Resource` lançar a `is` propriedade usando a palavra-chave e, em seguida, confirmar `InvalidCastException` que o elenco conseguiu garantir que seu código não caia com um quando executado em outras estruturas:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
