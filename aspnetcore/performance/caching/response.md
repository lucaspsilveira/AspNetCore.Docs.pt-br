---
title: Cache de resposta no ASP.NET Core
author: rick-anderson
description: Saiba como usar o cache de resposta para reduzir os requisitos de largura de banda e elevar o desempenho de aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/response
ms.openlocfilehash: 3e4bb9980c94f36319cf9b17e65a35ba0f77824e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776071"
---
# <a name="response-caching-in-aspnet-core"></a>Cache de resposta no ASP.NET Core

Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Steve Smith](https://ardalis.com/)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([como baixar](xref:index#how-to-download-a-sample))

O cache de resposta reduz o número de solicitações que um cliente ou proxy faz a um servidor Web. O cache de resposta também reduz a quantidade de trabalho que o servidor Web executa para gerar uma resposta. O cache de resposta é controlado por cabeçalhos que especificam como você deseja que o cliente, o proxy e o middleware armazenem em cache as respostas.

O [atributo ResponseCache](#responsecache-attribute) participa da configuração de cabeçalhos de cache de resposta. Os clientes e proxies intermediários devem respeitar os cabeçalhos para armazenar em cache as respostas na [especificação de cache HTTP 1,1](https://tools.ietf.org/html/rfc7234).

Para o cache do lado do servidor que segue a especificação de cache HTTP 1,1, use o [middleware de cache de resposta](xref:performance/caching/middleware). O middleware pode usar as propriedades <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> para influenciar o comportamento de cache do lado do servidor.

## <a name="http-based-response-caching"></a>Cache de resposta baseado em HTTP

A [especificação de cache HTTP 1,1](https://tools.ietf.org/html/rfc7234) descreve como os caches da Internet devem se comportar. O cabeçalho HTTP primário usado para cache é o [controle de cache](https://tools.ietf.org/html/rfc7234#section-5.2), que é usado para especificar *diretivas*de cache. As diretivas controlam o comportamento do cache à medida que as solicitações fazem seu caminho de clientes para servidores e, à medida que as respostas voltam dos servidores para os clientes. As solicitações e respostas são movidas por meio de servidores proxy, e os servidores proxy também devem estar em conformidade com a especificação de cache HTTP 1,1.

As `Cache-Control` diretivas comuns são mostradas na tabela a seguir.

| Diretiva                                                       | Ação |
| --------------------------------------------------------------- | ------ |
| [publicada](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Um cache pode armazenar a resposta. |
| [pessoal](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | A resposta não deve ser armazenada por um cache compartilhado. Um cache privado pode armazenar e reutilizar a resposta. |
| [idade máxima](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | O cliente não aceita uma resposta cuja idade é maior que o número especificado de segundos. Exemplos: `max-age=60` (60 segundos), `max-age=2592000` (1 mês) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Em solicitações**: um cache não deve usar uma resposta armazenada para atender à solicitação. O servidor de origem regenera a resposta para o cliente e o middleware atualiza a resposta armazenada em seu cache.<br><br>**Em respostas**: a resposta não deve ser usada para uma solicitação subsequente sem validação no servidor de origem. |
| [sem armazenamento](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Em solicitações**: um cache não deve armazenar a solicitação.<br><br>**Em respostas**: um cache não deve armazenar nenhuma parte da resposta. |

Outros cabeçalhos de cache que desempenham uma função no Caching são mostrados na tabela a seguir.

| Cabeçalho                                                     | Função |
| ---------------------------------------------------------- | -------- |
| [Idade](https://tools.ietf.org/html/rfc7234#section-5.1)     | Uma estimativa da quantidade de tempo em segundos desde que a resposta foi gerada ou validada com êxito no servidor de origem. |
| [Expira](https://tools.ietf.org/html/rfc7234#section-5.3) | O tempo após o qual a resposta é considerada obsoleta. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existe para compatibilidade com versões anteriores com caches HTTP/1.0 para comportamento `no-cache` de configuração. Se o `Cache-Control` cabeçalho estiver presente, o `Pragma` cabeçalho será ignorado. |
| [Varia](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Especifica que uma resposta armazenada em cache não deve ser enviada, a `Vary` menos que todos os campos de cabeçalho correspondam tanto à solicitação original da resposta em cache quanto à nova solicitação. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Os aspectos do cache baseado em HTTP solicitam diretivas de controle de cache

A [especificação de cache HTTP 1,1 para o cabeçalho de controle de cache](https://tools.ietf.org/html/rfc7234#section-5.2) requer um cache para honrar um cabeçalho válido `Cache-Control` enviado pelo cliente. Um cliente pode fazer solicitações com um `no-cache` valor de cabeçalho e forçar o servidor a gerar uma nova resposta para cada solicitação.

Sempre respeitando os `Cache-Control` cabeçalhos de solicitação do cliente faz sentido se você considerar a meta do cache http. Sob a especificação oficial, o Caching destina-se a reduzir a latência e a sobrecarga de rede de satisfazer solicitações em uma rede de clientes, proxies e servidores. Não é necessariamente uma maneira de controlar a carga em um servidor de origem.

Não há nenhum controle de desenvolvedor sobre esse comportamento de cache ao usar o [middleware de cache de resposta](xref:performance/caching/middleware) porque o middleware segue a especificação de cache oficial. Os [aprimoramentos planejados para o middleware](https://github.com/dotnet/AspNetCore/issues/2612) são uma oportunidade de configurar o middleware para ignorar o `Cache-Control` cabeçalho de uma solicitação ao decidir atender a uma resposta armazenada em cache. Os aprimoramentos planejados oferecem uma oportunidade de controlar melhor a carga do servidor.

## <a name="other-caching-technology-in-aspnet-core"></a>Outra tecnologia de cache no ASP.NET Core

### <a name="in-memory-caching"></a>Cache in-memory

O cache na memória usa a memória do servidor para armazenar dados armazenados em cache. Esse tipo de cache é adequado para um único servidor ou para vários servidores usando *sessões adesivas*. Sessões adesivas significa que as solicitações de um cliente são sempre roteadas para o mesmo servidor para processamento.

Para obter mais informações, consulte <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Cache distribuído

Use um cache distribuído para armazenar dados na memória quando o aplicativo estiver hospedado em uma nuvem ou farm de servidores. O cache é compartilhado entre os servidores que processam solicitações. Um cliente pode enviar uma solicitação que é tratada por qualquer servidor no grupo se os dados armazenados em cache para o cliente estiverem disponíveis. ASP.NET Core funciona com caches distribuídos SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)e [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Para obter mais informações, consulte <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Auxiliar de marca de cache

Armazene o conteúdo de uma exibição MVC ou página Razor com o auxiliar de marca de cache. O auxiliar de marca de cache usa o cache na memória para armazenar dados.

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Auxiliar de Marca de Cache Distribuído

Armazene em cache o conteúdo de uma exibição MVC ou página Razor em cenários de nuvem distribuída ou web farm com o auxiliar de marca de cache distribuído. O auxiliar de marca de cache distribuído usa SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)ou [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) para armazenar dados.

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>Atributo ResponseCache

O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> especifica os parâmetros necessários para definir os cabeçalhos apropriados no cache de resposta.

> [!WARNING]
> Desabilite o cache para conteúdo que contém informações para clientes autenticados. O Caching só deve ser habilitado para conteúdo que não seja alterado com base na identidade de um usuário ou se um usuário estiver conectado.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>varia a resposta armazenada pelos valores da lista de chaves de consulta fornecida. Quando um único valor de `*` é fornecido, o middleware varia as respostas por todos os parâmetros de cadeia de caracteres de consulta de solicitação.

O [middleware de cache de resposta](xref:performance/caching/middleware) deve estar habilitado para <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> definir a propriedade. Caso contrário, uma exceção de tempo de execução será lançada. Não há um cabeçalho HTTP correspondente para a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propriedade. A propriedade é um recurso HTTP manipulado pelo middleware de cache de resposta. Para que o middleware atenda a uma resposta armazenada em cache, a cadeia de caracteres de consulta e o valor da cadeia de caracteres de consulta devem corresponder a uma solicitação anterior. Por exemplo, considere a sequência de solicitações e os resultados mostrados na tabela a seguir.

| Solicitação                          | Result                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Retornado do servidor. |
| `http://example.com?key1=value1` | Retornado do middleware. |
| `http://example.com?key1=value2` | Retornado do servidor. |

A primeira solicitação é retornada pelo servidor e armazenada em cache no middleware. A segunda solicitação é retornada pelo middleware porque a cadeia de caracteres de consulta corresponde à solicitação anterior. A terceira solicitação não está no cache do middleware porque o valor da cadeia de caracteres de consulta não corresponde a uma solicitação anterior.

O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> é usado para configurar e criar (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`. O `ResponseCacheFilter` executa o trabalho de atualizar os cabeçalhos HTTP e os recursos apropriados da resposta. O filtro:

* Remove todos os cabeçalhos existentes `Vary`para `Cache-Control`, e `Pragma`.
* Grava os cabeçalhos apropriados com base nas propriedades definidas no <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.
* Atualiza o recurso HTTP de resposta do <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> cache se estiver definido.

### <a name="vary"></a>Varia

Esse cabeçalho só é gravado quando a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propriedade é definida. A propriedade definida como o `Vary` valor da propriedade. O exemplo a seguir usa <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> a propriedade:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Usando o aplicativo de exemplo, exiba os cabeçalhos de resposta com as ferramentas de rede do navegador. Os cabeçalhos de resposta a seguir são enviados com a resposta da página Cache1:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore e Location. None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>Substitui a maioria das outras propriedades. Quando essa propriedade é definida como `true`, o `Cache-Control` cabeçalho é definido como `no-store`. Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> é definido como `None`:

* `Cache-Control` está definido como `no-store,no-cache`.
* `Pragma` está definido como `no-cache`.

Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> é `false` e <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> é `None`, `Cache-Control`e `Pragma` são definidos como `no-cache`.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>normalmente é definido como `true` para páginas de erro. A página Cache2 no aplicativo de exemplo produz cabeçalhos de resposta que instruem o cliente a não armazenar a resposta.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

O aplicativo de exemplo retorna a página Cache2 com os seguintes cabeçalhos:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Localização e duração

Para habilitar o Caching <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> , o deve ser definido como um valor <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> positivo e deve `Any` ser (o padrão) `Client`ou. A estrutura define o `Cache-Control` cabeçalho para o valor de local seguido pelo `max-age` da resposta.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>as opções de `Any` e `Client` são transladadas `Cache-Control` em `public` valores `private`de cabeçalho de e, respectivamente. Conforme observado na seção [NoStore e Location. None](#nostore-and-locationnone) , a configuração <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> para `None` define os `Cache-Control` cabeçalhos `Pragma` e como `no-cache`.

`Location.Any`(`Cache-Control` definido como `public`) indica que o *cliente ou qualquer proxy intermediário* pode armazenar em cache o valor, incluindo o [middleware de cache de resposta](xref:performance/caching/middleware).

`Location.Client`(`Cache-Control` definido como `private`) indica que *somente o cliente* pode armazenar em cache o valor. Nenhum cache intermediário deve armazenar em cache o valor, incluindo o [middleware de cache de resposta](xref:performance/caching/middleware).

Os cabeçalhos de controle de cache simplesmente fornecem diretrizes para clientes e proxies intermediários quando e como armazenar em cache as respostas. Não há nenhuma garantia de que os clientes e proxies respeitarão a [especificação de cache HTTP 1,1](https://tools.ietf.org/html/rfc7234). O [middleware de cache de resposta](xref:performance/caching/middleware) sempre segue as regras de cache apresentadas pela especificação.

O exemplo a seguir mostra o modelo de página Cache3 do aplicativo de exemplo e os cabeçalhos produzidos <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> pela configuração e saindo <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> do valor padrão:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

O aplicativo de exemplo retorna a página Cache3 com o seguinte cabeçalho:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Perfis de cache

Em vez de duplicar as configurações de cache de resposta em vários atributos de ação do controlador, os perfis de cache podem serRazor configurados como opções ao configurar MVC/páginas no `Startup.ConfigureServices`. Os valores encontrados em um perfil de cache referenciado são usados como os padrões <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> pelo e são substituídos por todas as propriedades especificadas no atributo.

Configure um perfil de cache. O exemplo a seguir mostra um perfil de cache de 30 segundos no aplicativo `Startup.ConfigureServices`de exemplo:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

O modelo de página Cache4 do aplicativo de exemplo `Default30` faz referência ao perfil de cache:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> pode ser aplicado a:

* RazorOs atributos de manipuladores de &ndash; página (classes) não podem ser aplicados a métodos de manipulador.
* Controladores MVC (classes).
* Atributos de nível de método &ndash; de ações MVC (métodos) substituem as configurações especificadas em atributos de nível de classe.

O cabeçalho resultante aplicado à resposta da página Cache4 pelo perfil `Default30` de cache:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Recursos adicionais

* [Armazenando respostas em caches](https://tools.ietf.org/html/rfc7234#section-3)
* [Controle de cache](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
