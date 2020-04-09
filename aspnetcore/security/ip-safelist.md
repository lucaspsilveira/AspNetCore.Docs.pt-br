---
title: Lista de segurança ip do cliente para ASP.NET Core
author: damienbod
description: Aprenda a escrever middleware ou filtros de ação para validar endereços IP remotos em uma lista de endereços IP aprovados.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471805"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Lista de segurança ip do cliente para ASP.NET Core

Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)
 
Este artigo mostra três maneiras de implementar uma lista de segurança de endereço IP (também conhecida como lista de permitir) em um aplicativo ASP.NET Core. Um aplicativo de amostragem que acompanha demonstra as três abordagens. Você pode usar:

* Middleware para verificar o endereço IP remoto de cada solicitação.
* Os filtros de ação MVC para verificar o endereço IP remoto das solicitações de controladores específicos ou métodos de ação.
* Os filtros Razor Pages para verificar o endereço IP remoto das solicitações de páginas de Navalha.

Em cada caso, uma seqüência contendo endereços IP do cliente aprovados é armazenada em uma configuração de aplicativo. O middleware ou filtro:

* Analisa a seqüência em uma matriz. 
* Verifica se o endereço IP remoto existe na matriz.

O acesso é permitido se a matriz contiver o endereço IP. Caso contrário, um código de status proibido HTTP 403 é devolvido.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Lista de segurança de endereçoIP

No aplicativo de exemplo, a lista de segurança de endereço IP é:

* Definido pela `AdminSafeList` propriedade no arquivo *appsettings.json.*
* Uma seqüência delimitada em ponto e vírgula que pode conter endereços da [versão 4 do Protocolo da Internet (IPv4)](https://wikipedia.org/wiki/IPv4) e do Protocolo da Internet versão 6 [(IPv6).](https://wikipedia.org/wiki/IPv6)

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

No exemplo anterior, os endereços `127.0.0.1` `192.168.1.5` IPv4 e o endereço `::1` de loopback `0:0:0:0:0:0:0:1`IPv6 (formato compactado para) são permitidos.

## <a name="middleware"></a>Middleware

O `Startup.Configure` método adiciona `AdminSafeListMiddleware` o tipo de middleware personalizado ao pipeline de solicitação do aplicativo. A lista de segurança é recuperada com o provedor de configuração .NET Core e é passada como um parâmetro de construção.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

O middleware analisa a seqüência em uma matriz e procura o endereço IP remoto na matriz. Se o endereço IP remoto não for encontrado, o middleware retorna HTTP 403 Proibido. Este processo de validação é ignorado para solicitações HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtro de ação

Se você quiser um controle de acesso orientado por listas seguras para controladores MVC específicos ou métodos de ação, use um filtro de ação. Por exemplo:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

Em `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros MVC. No exemplo a `ClientIpCheckActionFilter` seguir, um filtro de ação é adicionado. Uma lista de segurança e uma instância de logger do console são passadas como parâmetros de construção.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

O filtro de ação pode então ser aplicado a um controlador ou método de ação com o atributo [[ServiceFilter]:](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

No aplicativo de amostra, o filtro de ação `Get` é aplicado ao método de ação do controlador. Quando você testa o aplicativo enviando:

* Uma solicitação HTTP `[ServiceFilter]` GET, o atributo valida o endereço IP do cliente. Se o `Get` acesso for permitido ao método de ação, uma variação da seguinte saída do console é produzida pelo filtro de ação e pelo método de ação:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Um verbo de solicitação HTTP `AdminSafeListMiddleware` que não seja GET, o middleware valida o endereço IP do cliente.

## <a name="razor-pages-filter"></a>Filtro de páginas de barbear

Se você quiser um controle de acesso orientado por listas seguras para um aplicativo Razor Pages, use um filtro Razor Pages. Por exemplo:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

In `Startup.ConfigureServices`, habilite o filtro Páginas de Navalha adicionando-o à coleção de filtros MVC. No exemplo a `ClientIpCheckPageFilter` seguir, um filtro Páginas de Navalha é adicionado. Uma lista de segurança e uma instância de logger do console são passadas como parâmetros de construção.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Quando a página *Index* Razor do aplicativo de amostra é solicitada, o filtro Páginas de Barbear valida o endereço IP do cliente. O filtro produz uma variação da seguinte saída do console:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/middleware/index>
* [Filtros de ação](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
