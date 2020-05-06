---
title: Migrar de ClaimsPrincipal. Current
author: mjrousos
description: Saiba como migrar para fora de ClaimsPrincipal. Current para recuperar a identidade e as declarações atuais do usuário autenticado no ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776084"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrar de ClaimsPrincipal. Current

Em projetos ASP.NET 4. x, era comum usar [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) para recuperar a identidade e as declarações atuais do usuário autenticado. No ASP.NET Core, essa propriedade não é mais definida. O código que estava dependendo dele precisa ser atualizado para obter a identidade do usuário autenticado atual por meio de um meio diferente.

## <a name="context-specific-data-instead-of-static-data"></a>Dados específicos de contexto em vez de dados estáticos

Ao usar ASP.NET Core, os valores de ambos `ClaimsPrincipal.Current` e `Thread.CurrentPrincipal` não são definidos. Essas propriedades representam o estado estático, que ASP.NET Core geralmente evita. Em vez disso, a arquitetura de ASP.NET Core é recuperar dependências (como a identidade do usuário atual) de coleções de serviço específicas de contexto (usando seu modelo de [injeção de dependência](xref:fundamentals/dependency-injection) (di)). Além do mais, `Thread.CurrentPrincipal` o thread é estático, portanto, ele pode não persistir alterações em alguns cenários `ClaimsPrincipal.Current` assíncronos `Thread.CurrentPrincipal` (e apenas chamadas por padrão).

Para entender os tipos de problemas que os membros estáticos do thread podem levar em cenários assíncronos, considere o seguinte trecho de código:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

O código de exemplo anterior `Thread.CurrentPrincipal` define e verifica seu valor antes e depois de aguardar uma chamada assíncrona. `Thread.CurrentPrincipal`é específico para o *thread* no qual ele está definido e o método provavelmente retomará a execução em um thread diferente após o Await. Consequentemente `Thread.CurrentPrincipal` , está presente quando é marcado pela primeira vez, mas é nulo após `await Task.Yield()`a chamada para.

Obter a identidade do usuário atual da coleção de serviços de DI do aplicativo também é mais comtestável, pois as identidades de teste podem ser facilmente injetadas.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Recuperar o usuário atual em um aplicativo ASP.NET Core

Há várias opções para recuperar os usuários autenticados atuais `ClaimsPrincipal` no ASP.NET Core em vez de: `ClaimsPrincipal.Current`

* **ControllerBase. User**. Os controladores MVC podem acessar o usuário autenticado atual com a propriedade [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Os componentes com acesso ao atual `HttpContext` (middleware, por exemplo) podem obter o usuário `ClaimsPrincipal` atual do [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Passado do chamador**. As bibliotecas sem acesso ao atual `HttpContext` geralmente são chamadas de controladores ou componentes de middleware e podem ter a identidade do usuário atual passada como um argumento.
* **IHttpContextAccessor**. O projeto que está sendo migrado para ASP.NET Core pode ser muito grande para passar facilmente a identidade do usuário atual para todos os locais necessários. Nesses casos, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) pode ser usado como uma solução alternativa. `IHttpContextAccessor`é capaz de acessar o atual `HttpContext` (se houver). Se DI estiver sendo usado, consulte <xref:fundamentals/httpcontext>. Uma solução de curto prazo para obter a identidade do usuário atual no código que ainda não foi atualizada para trabalhar com a arquitetura controlada por injeção de ASP.NET Core seria:

  * Torne `IHttpContextAccessor` disponível no contêiner de di chamando [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) em `Startup.ConfigureServices`.
  * Obtenha uma instância do `IHttpContextAccessor` durante a inicialização e armazene-a em uma variável estática. A instância é disponibilizada para o código que estava recuperando anteriormente o usuário atual de uma propriedade estática.
  * Recupere o uso `ClaimsPrincipal` `HttpContextAccessor.HttpContext?.User`do usuário atual. Se esse código for usado fora do contexto de uma solicitação HTTP, o `HttpContext` será nulo.

A opção final, usando uma `IHttpContextAccessor` instância armazenada em uma variável estática, é contrária aos princípios de ASP.NET Core (preferir dependências injetadas a dependências estáticas). Planeje eventualmente recuperar `IHttpContextAccessor` instâncias da injeção de dependência em vez disso. Um auxiliar estático pode ser uma ponte útil, no entanto, ao migrar grandes aplicativos ASP.NET existentes que `ClaimsPrincipal.Current`estavam usando anteriormente.
