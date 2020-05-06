---
title: Autorização baseada em exibição no ASP.NET Core MVC
author: rick-anderson
description: Este documento demonstra como injetar e utilizar o serviço de autorização dentro de uma Razor exibição de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775616"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Autorização baseada em exibição no ASP.NET Core MVC

Geralmente, um desenvolvedor deseja mostrar, ocultar ou modificar uma interface do usuário com base na identidade atual. Você pode acessar o serviço de autorização nas exibições do MVC por meio da [injeção de dependência](xref:fundamentals/dependency-injection). Para injetar o serviço de autorização Razor em uma exibição, `@inject` use a diretiva:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Se você quiser o serviço de autorização em cada exibição, coloque `@inject` a diretiva no arquivo *_ViewImports. cshtml* do diretório *views* . Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).

Use o serviço de autorização injetado para `AuthorizeAsync` invocar exatamente da mesma maneira que você verificaria durante [a autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

Em alguns casos, o recurso será seu modelo de exibição. Invoque `AuthorizeAsync` exatamente da mesma maneira que você verificaria durante [a autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

No código anterior, o modelo é passado como um recurso que a avaliação da política deve levar em consideração.

> [!WARNING]
> Não confie na alternância de visibilidade dos elementos da interface do usuário de seu aplicativo como a única verificação de autorização. Ocultar um elemento de interface do usuário pode não impedir completamente o acesso à sua ação de controlador associada. Por exemplo, considere o botão no trecho de código anterior. Um usuário poderá invocar `Edit` o método de ação se ele souber que a URL de recurso relativa é */Document/Edit/1*. Por esse motivo, o `Edit` método de ação deve executar sua própria verificação de autorização.
