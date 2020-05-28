---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="response-caching-middleware-in-aspnet-core"></a>Middleware de cache de resposta em ASP.NET Core

Por [John Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

Este artigo explica como configurar o middleware de cache de resposta em um aplicativo ASP.NET Core. O middleware determina quando as respostas são armazenáveis em cache, armazena respostas e serve respostas do cache. Para obter uma introdução ao cache HTTP e ao [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atributo, consulte [Caching de resposta](xref:performance/caching/response).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Configuração

O middleware de cache de resposta está implicitamente disponível para aplicativos ASP.NET Core por meio da estrutura compartilhada.

No `Startup.ConfigureServices` , adicione o middleware de cache de resposta à coleção de serviços:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configure o aplicativo para usar o middleware com o <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> método de extensão, que adiciona o middleware ao pipeline de processamento de solicitação no `Startup.Configure` :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

O aplicativo de exemplo adiciona cabeçalhos ao cache de controle em solicitações subsequentes:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): armazena em cache as respostas armazenáveis em cache por até 10 segundos.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): configura o middleware para servir uma resposta armazenada em cache somente se o cabeçalho [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) das solicitações subsequentes corresponder à da solicitação original.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Os cabeçalhos anteriores não são gravados na resposta e são substituídos quando um controlador, uma ação ou uma Razor página:

* Tem um atributo [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Isso se aplica mesmo se uma propriedade não estiver definida. Por exemplo, omitir a propriedade [VaryByHeader](/aspnet/core/performance/caching/response#vary) fará com que o cabeçalho correspondente seja removido da resposta.

O middleware de cache de resposta só armazena em cache as respostas do servidor que resultam em um código de status 200 (OK). Quaisquer outras respostas, incluindo [páginas de erro](xref:fundamentals/error-handling), são ignoradas pelo middleware.

> [!WARNING]
> As respostas que contêm o conteúdo para clientes autenticados devem ser marcadas como não armazenáveis em cache para impedir que o middleware armazene e atenda a essas respostas. Consulte [condições para o cache](#conditions-for-caching) para obter detalhes sobre como o middleware determina se uma resposta é armazenável em cache.

## <a name="options"></a>Opções

As opções de cache de resposta são mostradas na tabela a seguir.

| Opção | Descrição |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

--- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | O maior tamanho em cache para o corpo da resposta em bytes. O valor padrão é `64 * 1024 * 1024` (64 MB). | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | O limite de tamanho do middleware do cache de resposta em bytes. O valor padrão é `100 * 1024 * 1024` (100 MB). | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Determina se as respostas são armazenadas em cache em caminhos que diferenciam maiúsculas de minúsculas. O valor padrão é `false`. |

O exemplo a seguir configura o middleware para:

* Armazenar em cache as respostas com um tamanho de corpo menor ou igual a 1.024 bytes.
* Armazene as respostas por caminhos que diferenciam maiúsculas de minúsculas. Por exemplo, `/page1` e `/Page1` são armazenados separadamente.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Ao usar os modelos de página de controladores de API Web ou MVC Razor , o [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atributo especifica os parâmetros necessários para definir os cabeçalhos apropriados para o cache de resposta. O único parâmetro do `[ResponseCache]` atributo que exige estritamente o middleware é <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , que não corresponde a um cabeçalho HTTP real. Para obter mais informações, consulte <xref:performance/caching/response#responsecache-attribute>.

Quando não estiver usando o `[ResponseCache]` atributo, o cache de resposta poderá ser variado com `VaryByQueryKeys` . Use o <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> diretamente do [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

O uso de um único valor igual a `*` in `VaryByQueryKeys` varia o cache por todos os parâmetros de consulta de solicitação.

## <a name="http-headers-used-by-response-caching-middleware"></a>Cabeçalhos HTTP usados pelo middleware de cache de resposta

A tabela a seguir fornece informações sobre cabeçalhos HTTP que afetam o cache de resposta.

| parâmetro | Detalhes |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

--- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | | `Authorization` | A resposta não será armazenada em cache se o cabeçalho existir. | | `Cache-Control` | O middleware só considera respostas de cache marcadas com a `public` diretiva de cache. Controlar o cache com os seguintes parâmetros:<ul><li>idade máxima</li><li>máximo-obsoleto&#8224;</li><li>mín. de atualização</li><li>must-revalidate</li><li>no-cache</li><li>sem armazenamento</li><li>somente-se-em-cache</li><li>particulares</li><li>públicos</li><li>s-maxage</li><li>&#8225; de revalidação de proxy</li></ul>&#8224;se nenhum limite for especificado para `max-stale` , o middleware não executará nenhuma ação.<br>&#8225;`proxy-revalidate` tem o mesmo efeito que `must-revalidate` .<br><br>Para obter mais informações, consulte [RFC 7231: solicitar Cache-Control diretivas](https://tools.ietf.org/html/rfc7234#section-5.2.1). | | `Pragma` | Um `Pragma: no-cache` cabeçalho na solicitação produz o mesmo efeito que `Cache-Control: no-cache` . Esse cabeçalho é substituído pelas diretivas relevantes no `Cache-Control` cabeçalho, se presente. Considerado para compatibilidade com versões anteriores com HTTP/1.0. | | `Set-Cookie` | A resposta não será armazenada em cache se o cabeçalho existir. Qualquer middleware no pipeline de processamento de solicitação que define um ou mais cookies impede que o middleware de cache de resposta em cache a resposta (por exemplo, o [provedor TempData baseado em cookie](xref:fundamentals/app-state#tempdata)).  | | `Vary` | O `Vary` cabeçalho é usado para variar a resposta armazenada em cache por outro cabeçalho. Por exemplo, armazene respostas em cache por meio da codificação, incluindo o `Vary: Accept-Encoding` cabeçalho, que armazena em cache as respostas para solicitações com cabeçalhos `Accept-Encoding: gzip` e `Accept-Encoding: text/plain` separadamente. Uma resposta com um valor de cabeçalho de `*` nunca é armazenada. | | `Expires` | Uma resposta considerada obsoleta por esse cabeçalho não é armazenada ou recuperada, a menos que seja substituída por outros `Cache-Control` cabeçalhos. | | `If-None-Match` | A resposta completa será servida do cache se o valor não for `*` e a `ETag` da resposta não corresponder a nenhum dos valores fornecidos. Caso contrário, uma resposta 304 (não modificada) é servida. | | `If-Modified-Since` | Se o `If-None-Match` cabeçalho não estiver presente, uma resposta completa será servida do cache se a data de resposta armazenada em cache for mais recente do que o valor fornecido. Caso contrário, uma resposta *304-não modificada* é servida. | | `Date` | Ao servir do cache, o `Date` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original. | | `Content-Length` | Ao servir do cache, o `Content-Length` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original. | | `Age` | O `Age` cabeçalho enviado na resposta original é ignorado. O middleware computa um novo valor ao fornecer uma resposta armazenada em cache. |

## <a name="caching-respects-request-cache-control-directives"></a>Aspectos de cache de solicitação de cache – diretivas de controle

O middleware respeita as regras da especificação de [cache HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). As regras exigem um cache para honrar um `Cache-Control` cabeçalho válido enviado pelo cliente. Na especificação, um cliente pode fazer solicitações com um `no-cache` valor de cabeçalho e forçar o servidor a gerar uma nova resposta para cada solicitação. Atualmente, não há nenhum controle de desenvolvedor sobre esse comportamento de cache ao usar o middleware porque o middleware segue a especificação de cache oficial.

Para obter mais controle sobre o comportamento de caching, explore outros recursos de cache do ASP.NET Core. Consulte os seguintes tópicos:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Solução de problemas

Se o comportamento de caching não for o esperado, confirme se as respostas são armazenáveis em cache e capazes de serem servidas do cache. Examine os cabeçalhos de entrada da solicitação e os cabeçalhos de saída da resposta. Habilite o [log](xref:fundamentals/logging/index) para ajudar com a depuração.

Ao testar e solucionar problemas de comportamento de cache, um navegador pode definir cabeçalhos de solicitação que afetam o armazenamento em cache de maneiras indesejáveis. Por exemplo, um navegador pode definir o `Cache-Control` cabeçalho como `no-cache` ou `max-age=0` ao atualizar uma página. As seguintes ferramentas podem definir explicitamente os cabeçalhos de solicitação e são preferenciais para o cache de teste:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Condições para cache

* A solicitação deve resultar em uma resposta do servidor com um código de status 200 (OK).
* O método de solicitação deve ser GET ou HEAD.
* No `Startup.Configure` , o middleware de cache de resposta deve ser colocado antes do middleware que requer Caching. Para obter mais informações, consulte <xref:fundamentals/middleware/index>.
* O `Authorization` cabeçalho não deve estar presente.
* `Cache-Control`os parâmetros de cabeçalho devem ser válidos e a resposta deve ser marcada `public` e não marcada `private` .
* O `Pragma: no-cache` cabeçalho não deve estar presente se o `Cache-Control` cabeçalho não estiver presente, pois o `Cache-Control` cabeçalho substitui o `Pragma` cabeçalho quando estiver presente.
* O `Set-Cookie` cabeçalho não deve estar presente.
* `Vary`os parâmetros de cabeçalho devem ser válidos e não iguais a `*` .
* O `Content-Length` valor do cabeçalho (se definido) deve corresponder ao tamanho do corpo da resposta.
* O <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> não é usado.
* A resposta não deve estar obsoleta conforme especificado pelo `Expires` cabeçalho e pelas `max-age` diretivas de `s-maxage` cache e.
* O buffer de resposta deve ser bem-sucedido. O tamanho da resposta deve ser menor do que o configurado ou o padrão <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . O tamanho do corpo da resposta deve ser menor do que o configurado ou o padrão <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* A resposta deve ser armazenada em cache de acordo com as especificações [RFC 7234](https://tools.ietf.org/html/rfc7234) . Por exemplo, a `no-store` diretiva não deve existir em campos de cabeçalho de solicitação ou de resposta. Consulte a *seção 3: armazenando respostas em caches* do [RFC 7234](https://tools.ietf.org/html/rfc7234) para obter detalhes.

> [!NOTE]
> O sistema antifalsificação para gerar tokens seguros para impedir ataques de solicitação intersite forjada (CSRF) define `Cache-Control` os `Pragma` cabeçalhos e para `no-cache` que as respostas não sejam armazenadas em cache. Para obter informações sobre como desabilitar tokens antifalsificação para elementos de formulário HTML, consulte <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este artigo explica como configurar o middleware de cache de resposta em um aplicativo ASP.NET Core. O middleware determina quando as respostas são armazenáveis em cache, armazena respostas e serve respostas do cache. Para obter uma introdução ao cache HTTP e ao [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atributo, consulte [Caching de resposta](xref:performance/caching/response).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Configuração

Use o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

No `Startup.ConfigureServices` , adicione o middleware de cache de resposta à coleção de serviços:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configure o aplicativo para usar o middleware com o <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> método de extensão, que adiciona o middleware ao pipeline de processamento de solicitação no `Startup.Configure` :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

O aplicativo de exemplo adiciona cabeçalhos ao cache de controle em solicitações subsequentes:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): armazena em cache as respostas armazenáveis em cache por até 10 segundos.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): configura o middleware para servir uma resposta armazenada em cache somente se o cabeçalho [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) das solicitações subsequentes corresponder à da solicitação original.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Os cabeçalhos anteriores não são gravados na resposta e são substituídos quando um controlador, uma ação ou uma Razor página:

* Tem um atributo [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Isso se aplica mesmo se uma propriedade não estiver definida. Por exemplo, omitir a propriedade [VaryByHeader](/aspnet/core/performance/caching/response#vary) fará com que o cabeçalho correspondente seja removido da resposta.

O middleware de cache de resposta só armazena em cache as respostas do servidor que resultam em um código de status 200 (OK). Quaisquer outras respostas, incluindo [páginas de erro](xref:fundamentals/error-handling), são ignoradas pelo middleware.

> [!WARNING]
> As respostas que contêm o conteúdo para clientes autenticados devem ser marcadas como não armazenáveis em cache para impedir que o middleware armazene e atenda a essas respostas. Consulte [condições para o cache](#conditions-for-caching) para obter detalhes sobre como o middleware determina se uma resposta é armazenável em cache.

## <a name="options"></a>Opções

As opções de cache de resposta são mostradas na tabela a seguir.

| Opção | Descrição |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

--- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | O maior tamanho em cache para o corpo da resposta em bytes. O valor padrão é `64 * 1024 * 1024` (64 MB). | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | O limite de tamanho do middleware do cache de resposta em bytes. O valor padrão é `100 * 1024 * 1024` (100 MB). | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Determina se as respostas são armazenadas em cache em caminhos que diferenciam maiúsculas de minúsculas. O valor padrão é `false`. |

O exemplo a seguir configura o middleware para:

* Armazenar em cache as respostas com um tamanho de corpo menor ou igual a 1.024 bytes.
* Armazene as respostas por caminhos que diferenciam maiúsculas de minúsculas. Por exemplo, `/page1` e `/Page1` são armazenados separadamente.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Ao usar os modelos de página de controladores de API Web ou MVC Razor , o [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atributo especifica os parâmetros necessários para definir os cabeçalhos apropriados para o cache de resposta. O único parâmetro do `[ResponseCache]` atributo que exige estritamente o middleware é <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , que não corresponde a um cabeçalho HTTP real. Para obter mais informações, consulte <xref:performance/caching/response#responsecache-attribute>.

Quando não estiver usando o `[ResponseCache]` atributo, o cache de resposta poderá ser variado com `VaryByQueryKeys` . Use o <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> diretamente do [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

O uso de um único valor igual a `*` in `VaryByQueryKeys` varia o cache por todos os parâmetros de consulta de solicitação.

## <a name="http-headers-used-by-response-caching-middleware"></a>Cabeçalhos HTTP usados pelo middleware de cache de resposta

A tabela a seguir fornece informações sobre cabeçalhos HTTP que afetam o cache de resposta.

| parâmetro | Detalhes |
| ---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

--- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | | `Authorization` | A resposta não será armazenada em cache se o cabeçalho existir. | | `Cache-Control` | O middleware só considera respostas de cache marcadas com a `public` diretiva de cache. Controlar o cache com os seguintes parâmetros:<ul><li>idade máxima</li><li>máximo-obsoleto&#8224;</li><li>mín. de atualização</li><li>must-revalidate</li><li>no-cache</li><li>sem armazenamento</li><li>somente-se-em-cache</li><li>particulares</li><li>públicos</li><li>s-maxage</li><li>&#8225; de revalidação de proxy</li></ul>&#8224;se nenhum limite for especificado para `max-stale` , o middleware não executará nenhuma ação.<br>&#8225;`proxy-revalidate` tem o mesmo efeito que `must-revalidate` .<br><br>Para obter mais informações, consulte [RFC 7231: solicitar Cache-Control diretivas](https://tools.ietf.org/html/rfc7234#section-5.2.1). | | `Pragma` | Um `Pragma: no-cache` cabeçalho na solicitação produz o mesmo efeito que `Cache-Control: no-cache` . Esse cabeçalho é substituído pelas diretivas relevantes no `Cache-Control` cabeçalho, se presente. Considerado para compatibilidade com versões anteriores com HTTP/1.0. | | `Set-Cookie` | A resposta não será armazenada em cache se o cabeçalho existir. Qualquer middleware no pipeline de processamento de solicitação que define um ou mais cookies impede que o middleware de cache de resposta em cache a resposta (por exemplo, o [provedor TempData baseado em cookie](xref:fundamentals/app-state#tempdata)).  | | `Vary` | O `Vary` cabeçalho é usado para variar a resposta armazenada em cache por outro cabeçalho. Por exemplo, armazene respostas em cache por meio da codificação, incluindo o `Vary: Accept-Encoding` cabeçalho, que armazena em cache as respostas para solicitações com cabeçalhos `Accept-Encoding: gzip` e `Accept-Encoding: text/plain` separadamente. Uma resposta com um valor de cabeçalho de `*` nunca é armazenada. | | `Expires` | Uma resposta considerada obsoleta por esse cabeçalho não é armazenada ou recuperada, a menos que seja substituída por outros `Cache-Control` cabeçalhos. | | `If-None-Match` | A resposta completa será servida do cache se o valor não for `*` e a `ETag` da resposta não corresponder a nenhum dos valores fornecidos. Caso contrário, uma resposta 304 (não modificada) é servida. | | `If-Modified-Since` | Se o `If-None-Match` cabeçalho não estiver presente, uma resposta completa será servida do cache se a data de resposta armazenada em cache for mais recente do que o valor fornecido. Caso contrário, uma resposta *304-não modificada* é servida. | | `Date` | Ao servir do cache, o `Date` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original. | | `Content-Length` | Ao servir do cache, o `Content-Length` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original. | | `Age` | O `Age` cabeçalho enviado na resposta original é ignorado. O middleware computa um novo valor ao fornecer uma resposta armazenada em cache. |

## <a name="caching-respects-request-cache-control-directives"></a>Aspectos de cache de solicitação de cache – diretivas de controle

O middleware respeita as regras da especificação de [cache HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). As regras exigem um cache para honrar um `Cache-Control` cabeçalho válido enviado pelo cliente. Na especificação, um cliente pode fazer solicitações com um `no-cache` valor de cabeçalho e forçar o servidor a gerar uma nova resposta para cada solicitação. Atualmente, não há nenhum controle de desenvolvedor sobre esse comportamento de cache ao usar o middleware porque o middleware segue a especificação de cache oficial.

Para obter mais controle sobre o comportamento de caching, explore outros recursos de cache do ASP.NET Core. Consulte os seguintes tópicos:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Solução de problemas

Se o comportamento de caching não for o esperado, confirme se as respostas são armazenáveis em cache e capazes de serem servidas do cache. Examine os cabeçalhos de entrada da solicitação e os cabeçalhos de saída da resposta. Habilite o [log](xref:fundamentals/logging/index) para ajudar com a depuração.

Ao testar e solucionar problemas de comportamento de cache, um navegador pode definir cabeçalhos de solicitação que afetam o armazenamento em cache de maneiras indesejáveis. Por exemplo, um navegador pode definir o `Cache-Control` cabeçalho como `no-cache` ou `max-age=0` ao atualizar uma página. As seguintes ferramentas podem definir explicitamente os cabeçalhos de solicitação e são preferenciais para o cache de teste:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Condições para cache

* A solicitação deve resultar em uma resposta do servidor com um código de status 200 (OK).
* O método de solicitação deve ser GET ou HEAD.
* No `Startup.Configure` , o middleware de cache de resposta deve ser colocado antes do middleware que requer Caching. Para obter mais informações, consulte <xref:fundamentals/middleware/index>.
* O `Authorization` cabeçalho não deve estar presente.
* `Cache-Control`os parâmetros de cabeçalho devem ser válidos e a resposta deve ser marcada `public` e não marcada `private` .
* O `Pragma: no-cache` cabeçalho não deve estar presente se o `Cache-Control` cabeçalho não estiver presente, pois o `Cache-Control` cabeçalho substitui o `Pragma` cabeçalho quando estiver presente.
* O `Set-Cookie` cabeçalho não deve estar presente.
* `Vary`os parâmetros de cabeçalho devem ser válidos e não iguais a `*` .
* O `Content-Length` valor do cabeçalho (se definido) deve corresponder ao tamanho do corpo da resposta.
* O <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> não é usado.
* A resposta não deve estar obsoleta conforme especificado pelo `Expires` cabeçalho e pelas `max-age` diretivas de `s-maxage` cache e.
* O buffer de resposta deve ser bem-sucedido. O tamanho da resposta deve ser menor do que o configurado ou o padrão <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . O tamanho do corpo da resposta deve ser menor do que o configurado ou o padrão <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* A resposta deve ser armazenada em cache de acordo com as especificações [RFC 7234](https://tools.ietf.org/html/rfc7234) . Por exemplo, a `no-store` diretiva não deve existir em campos de cabeçalho de solicitação ou de resposta. Consulte a *seção 3: armazenando respostas em caches* do [RFC 7234](https://tools.ietf.org/html/rfc7234) para obter detalhes.

> [!NOTE]
> O sistema antifalsificação para gerar tokens seguros para impedir ataques de solicitação intersite forjada (CSRF) define `Cache-Control` os `Pragma` cabeçalhos e para `no-cache` que as respostas não sejam armazenadas em cache. Para obter informações sobre como desabilitar tokens antifalsificação para elementos de formulário HTML, consulte <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
