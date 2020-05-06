---
title: IP do cliente da assafe para ASP.NET Core
author: damienbod
description: Saiba como escrever filtros de middleware ou de ação para validar endereços IP remotos em uma lista de endereços IP aprovados.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 7923a81e72124cfb0e11e3c1ac327c1e32194b21
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776494"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>IP do cliente da assafe para ASP.NET Core

Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)
 
Este artigo mostra três maneiras de implementar uma lista de permissões de endereço IP (também conhecida como List de permissão) em um aplicativo ASP.NET Core. Um aplicativo de exemplo que o acompanha demonstra todas as três abordagens. Você pode usar:

* Middleware para verificar o endereço IP remoto de cada solicitação.
* Filtros de ação MVC para verificar o endereço IP remoto de solicitações para controladores ou métodos de ação específicos.
* RazorPáginas filtra para verificar o endereço IP remoto de solicitações de Razor páginas.

Em cada caso, uma cadeia de caracteres contendo endereços IP de cliente aprovados é armazenada em uma configuração de aplicativo. O middleware ou o filtro:

* Analisa a cadeia de caracteres em uma matriz. 
* Verifica se o endereço IP remoto existe na matriz.

O acesso será permitido se a matriz contiver o endereço IP. Caso contrário, um código de status HTTP 403 proibido será retornado.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Endereço IP do SafeList

No aplicativo de exemplo, a SafeList de endereço IP é:

* Definido pela `AdminSafeList` Propriedade no arquivo *appSettings. JSON* .
* Uma cadeia de caracteres delimitada por ponto-e-vírgula que pode conter endereços IP [versão 4 (IPv4)](https://wikipedia.org/wiki/IPv4) e [protocolo IP versão 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

No exemplo anterior, os endereços IPv4 `127.0.0.1` de e `192.168.1.5` e o endereço de loopback IPv6 de `::1` (formato compactado `0:0:0:0:0:0:0:1`) são permitidos.

## <a name="middleware"></a>Middleware

O `Startup.Configure` método adiciona o tipo `AdminSafeListMiddleware` de middleware personalizado ao pipeline de solicitação do aplicativo. A SafeList é recuperada com o provedor de configuração do .NET Core e é passada como um parâmetro de construtor.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

O middleware analisa a cadeia de caracteres em uma matriz e procura o endereço IP remoto na matriz. Se o endereço IP remoto não for encontrado, o middleware retornará HTTP 403 Proibido. Esse processo de validação é ignorado para solicitações HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtro de ação

Se você quiser o controle de acesso controlado por forma segura para controladores ou métodos de ação específicos do MVC, use um filtro de ação. Por exemplo: 

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

No `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros do MVC. No exemplo a seguir, um `ClientIpCheckActionFilter` filtro de ação é adicionado. Uma lista de imsafe e uma instância de agente de console são passadas como parâmetros de construtor.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

O filtro de ação pode então ser aplicado a um controlador ou método de ação com o atributo [[userFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

No aplicativo de exemplo, o filtro de ação é aplicado ao método de `Get` ação do controlador. Ao testar o aplicativo enviando:

* Uma solicitação HTTP GET, o `[ServiceFilter]` atributo valida o endereço IP do cliente. Se o acesso for permitido para `Get` o método de ação, uma variação da seguinte saída do console será produzida pelo filtro de ação e pelo método de ação:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Um verbo de solicitação HTTP diferente de GET, `AdminSafeListMiddleware` o middleware valida o endereço IP do cliente.

## <a name="razor-pages-filter"></a>RazorFiltro de páginas

Se você quiser o controle de acesso controlado por seguração para um Razor aplicativo de Razor páginas, use um filtro de páginas. Por exemplo: 

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

No `Startup.ConfigureServices`, habilite Razor o filtro páginas adicionando-o à coleção de filtros MVC. No exemplo a seguir, um `ClientIpCheckPageFilter` Razor filtro de páginas é adicionado. Uma lista de imsafe e uma instância de agente de console são passadas como parâmetros de construtor.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Quando a página de *índice* Razor do aplicativo de exemplo é solicitada, o filtro de Razor páginas valida o endereço IP do cliente. O filtro produz uma variação da seguinte saída do console:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/middleware/index>
* [Filtros de ação](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
