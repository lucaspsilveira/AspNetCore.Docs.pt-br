---
title: Autorização baseada em declarações no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar verificações de declarações para autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/claims
ms.openlocfilehash: de8ab915e6a8529c7401f89fad067ec33d5d0713
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774412"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Autorização baseada em declarações no ASP.NET Core

<a name="security-authorization-claims-based"></a>

Quando uma identidade é criada, ela pode ser atribuída a uma ou mais declarações emitidas por uma parte confiável. Uma declaração é um par de valor de nome que representa qual é o assunto, não o que o assunto pode fazer. Por exemplo, você pode ter uma licença de driver, emitida por uma autoridade de licença de condução local. A licença do seu driver tem sua data de nascimento. Nesse caso `DateOfBirth`, o nome da declaração seria, o valor da declaração seria a sua data de nascimento, por `8th June 1970` exemplo, e o emissor seria a autoridade de certificação de condução. A autorização baseada em declarações, em sua mais simples, verifica o valor de uma declaração e permite o acesso a um recurso com base no valor. Por exemplo, se você quiser acesso a um clube noturno, o processo de autorização poderá ser:

O diretor de segurança de porta avaliaria o valor de sua declaração de data de nascimento e se confiará no emissor (a autoridade de licença de condução) antes de conceder acesso.

Uma identidade pode conter várias declarações com vários valores e pode conter várias declarações do mesmo tipo.

## <a name="adding-claims-checks"></a>Adicionando verificações de declarações

As verificações de autorização baseadas em declarações são declarativas-o desenvolvedor as incorpora em seu código, em um controlador ou em uma ação dentro de um controlador, especificando as declarações que o usuário atual deve possuir e, opcionalmente, o valor que a declaração deve manter para acessar o recurso solicitado. Os requisitos de declarações são baseados em políticas, o desenvolvedor deve criar e registrar uma política que expresse os requisitos de declarações.

O tipo mais simples de política de declaração procura a presença de uma declaração e não verifica o valor.

Primeiro, você precisa criar e registrar a política. Isso ocorre como parte da configuração do serviço de autorização, que normalmente faz parte `ConfigureServices()` do seu arquivo *Startup.cs* .

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

Nesse caso, a `EmployeeOnly` política verifica a presença de uma `EmployeeNumber` declaração na identidade atual.

Em seguida, aplique a política usando `Policy` a propriedade no `AuthorizeAttribute` atributo para especificar o nome da política;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

O `AuthorizeAttribute` atributo pode ser aplicado a um controlador inteiro, nessa instância, somente as identidades correspondentes à política terão permissão de acesso a qualquer ação no controlador.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Se você tiver um controlador protegido pelo `AuthorizeAttribute` atributo, mas quiser permitir o acesso anônimo a determinadas ações, aplique o `AllowAnonymousAttribute` atributo.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

A maioria das declarações vem com um valor. Você pode especificar uma lista de valores permitidos ao criar a política. O exemplo a seguir só terá êxito para funcionários cujo número de funcionário fosse 1, 2, 3, 4 ou 5.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Adicionar uma verificação de declaração genérica

Se o valor da declaração não for um valor único ou uma transformação for necessária, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Para obter mais informações, consulte [usando um Func para atender a uma política](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Avaliação de várias políticas

Se você aplicar várias políticas a um controlador ou uma ação, todas as políticas deverão passar antes que o acesso seja concedido. Por exemplo:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

No exemplo acima, qualquer identidade que atenda à `EmployeeOnly` política pode acessar a `Payslip` ação, pois essa política é imposta no controlador. No entanto, para chamar `UpdateSalary` a ação, a identidade deve atender `EmployeeOnly` *tanto* à política `HumanResources` quanto à política.

Se você quiser políticas mais complicadas, como a obtenção de uma declaração de data de nascimento, calcular uma idade a partir dela, verificando se a idade é 21 ou mais antiga, você precisa escrever [manipuladores de política personalizados](xref:security/authorization/policies).
