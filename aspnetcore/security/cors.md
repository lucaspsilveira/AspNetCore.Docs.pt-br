---
title: Habilitar as solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como o CORS como um padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 1a52a2425eeba2bc62253e96fe6d2465562c154e
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292757"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Habilitar as solicitações entre origens (CORS) no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)

Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo. Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).

CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):

* É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.
* **Não** é um recurso de segurança, o CORS libera a segurança. Uma API não é mais segura, permitindo CORS. Para obter mais informações, consulte [como o CORS funciona](#how-cors).
* Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.
* É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mesma origem

Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Essas duas URLs têm a mesma origem:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Essas URLs têm origens diferentes das duas URLs anteriores:

* `https://example.net`: Domínio diferente
* `https://www.example.com/foo.html`: Subdomínio diferente
* `http://example.com/foo.html`: Esquema diferente
* `https://example.com:9000/foo.html`: Porta diferente

## <a name="enable-cors"></a>Habilitar CORS

Há três maneiras de habilitar o CORS:

* No middleware usando uma [política nomeada](#np) ou uma [política padrão](#dp).
* Usando o [Roteamento de ponto de extremidade](#ecors).
* Com o atributo [[EnableCors]](#attr) .

O uso do atributo [[EnableCors]](#attr) com uma política nomeada fornece o melhor controle na limitação de pontos de extremidade que dão suporte a CORS.

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A>deve ser chamado antes de <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> usar `UseResponseCaching` .

Cada abordagem é detalhada nas seções a seguir.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS com política nomeada e middleware

O middleware CORS lida com solicitações entre origens. O código a seguir aplica uma política CORS a todos os pontos de extremidade do aplicativo com as origens especificadas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

O código anterior:

* Define o nome da política como `_myAllowSpecificOrigins` . O nome da política é arbitrário.
* Chama o <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensão e especifica a `_myAllowSpecificOrigins` política CORS. `UseCors`Adiciona o middleware CORS. A chamada para `UseCors` deve ser colocada após `UseRouting` , mas antes de `UseAuthorization` . Para obter mais informações, consulte [ordem de middleware](xref:fundamentals/middleware/index#middleware-order).
* Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). O lambda pega um <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto. [Opções de configuração](#cors-policy-options), como `WithOrigins` , são descritas posteriormente neste artigo.
* Habilita a `_myAllowSpecificOrigins` política CORS para todos os pontos de extremidade do controlador. Consulte [Roteamento de ponto de extremidade](#ecors) para aplicar uma política CORS a pontos de extremidades específicos.
* Ao usar o [middleware de cache de resposta](xref:performance/caching/middleware), chame <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .

Com o roteamento de ponto de extremidade, o middleware CORS **deve** ser configurado para ser executado entre as chamadas para `UseRouting` e `UseEndpoints` .

Consulte [testar CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.

A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada de método adiciona serviços CORS ao contêiner de serviço do aplicativo:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.

Os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos podem ser encadeados, conforme mostrado no código a seguir:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Observação: a URL especificada **não** deve conter uma barra à direita ( `/` ). Se a URL for encerrada com `/` , a comparação retornará `false` e nenhum cabeçalho será retornado.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS com política padrão e middleware

O código realçado a seguir habilita a política CORS padrão:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

O código anterior aplica a política CORS padrão a todos os pontos de extremidade do controlador.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Habilitar CORS com roteamento de ponto de extremidade

Habilitar o CORS em uma base por ponto de extremidade usando `RequireCors` atualmente **não** oferece suporte a solicitações de [simulação automáticas](#apf). Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [testar CORS com roteamento de ponto de extremidade e [httpoptions]](#tcer).

Com o roteamento de ponto de extremidade, o CORS pode ser habilitado em uma base por ponto de extremidade usando o <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> conjunto de métodos de extensão:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

No código anterior:

* `app.UseCors`habilita o middleware CORS. Como uma política padrão não foi configurada, `app.UseCors()` sozinha não habilita o CORS.
* Os `/echo` pontos de extremidade do controlador e permitem solicitações entre origens usando a política especificada.
* Os `/echo2` pontos de extremidade e de Razor páginas **não** permitem solicitações entre origens porque nenhuma política padrão foi especificada.

O atributo [[DisableCors]](#dc) não **desabilita o** CORS que foi habilitado pelo roteamento de ponto de extremidade com `RequireCors` .

Consulte [testar CORS com o roteamento de ponto de extremidade e [httpoptions]](#tcer) para obter instruções sobre o código de teste semelhante ao anterior.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Habilitar CORS com atributos

Habilitar o CORS com o atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e aplicar uma política nomeada somente a esses pontos de extremidade que exigem CORS fornece o controle mais preciso.

O atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente. O `[EnableCors]` atributo habilita o CORS para pontos de extremidade selecionados, em vez de todos os pontos de extremidade:

* `[EnableCors]`Especifica a política padrão.
* `[EnableCors("{Policy String}")]`Especifica uma política nomeada.

O `[EnableCors]` atributo pode ser aplicado a:

* RazorWeb`PageModel`
* Controller
* Método de ação do controlador

Políticas diferentes podem ser aplicadas a controladores, modelos de página ou métodos de ação com o `[EnableCors]` atributo. Quando o `[EnableCors]` atributo é aplicado a um controlador, modelo de página ou método de ação e o CORS é habilitado no middleware, **ambas** as políticas são aplicadas. **Recomendamos a combinação de políticas. Use o** `[EnableCors]` **atributo ou middleware, não ambos no mesmo aplicativo.**

O código a seguir aplica uma política diferente a cada método:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

O código a seguir cria duas políticas CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Para o melhor controle da limitação de solicitações de CORS:

* Use `[EnableCors("MyPolicy")]` com uma política nomeada.
* Não defina uma política padrão.
* Não use o [Roteamento de ponto de extremidade](#ecors).

O código na próxima seção atende à lista anterior.

Consulte [testar CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.

<a name="dc"></a>

### <a name="disable-cors"></a>Desabilitar CORS

O atributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) não **desabilita o** CORS que foi habilitado pelo roteamento de ponto de [extremidade](#ecors).

O código a seguir define a política CORS `"MyPolicy"` :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

O código a seguir desabilita o CORS para a `GetValues2` ação:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

O código anterior:

* Não habilita o CORS com [Roteamento de ponto de extremidade](#ecors).
* Não define uma [política CORS padrão](#dp).
* Usa [[EnableCors ("MyPolicy")]](#attr) para habilitar a `"MyPolicy"` política CORS para o controlador.
* Desabilita o CORS para o `GetValues2` método.

Consulte [testar CORS](#testc) para obter instruções sobre como testar o código anterior.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opções de política de CORS

Esta seção descreve as várias opções que podem ser definidas em uma política CORS:

* [Definir as origens permitidas](#set-the-allowed-origins)
* [Definir os métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Definir os cabeçalhos de solicitação permitidos](#set-the-allowed-request-headers)
* [Definir os cabeçalhos de resposta expostos](#set-the-exposed-response-headers)
* [Credenciais em solicitações entre origens](#credentials-in-cross-origin-requests)
* [Definir o tempo de expiração de simulação](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado em `Startup.ConfigureServices` . Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.

## <a name="set-the-allowed-origins"></a>Definir as origens permitidas

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitações CORS de todas as origens com qualquer esquema ( `http` ou `https` ). `AllowAnyOrigin`o não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.

> [!NOTE]
> Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites. O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.

`AllowAnyOrigin`afeta as solicitações de simulação e o `Access-Control-Allow-Origin` cabeçalho. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Define a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriedade da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Definir os métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite qualquer método HTTP:
* Afeta as solicitações de simulação e o `Access-Control-Allow-Methods` cabeçalho. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Definir os cabeçalhos de solicitação permitidos

Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada [criar cabeçalhos de solicitação de autor](https://xhr.spec.whatwg.org/#request), chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir todos os [cabeçalhos de solicitação de autor](https://www.w3.org/TR/cors/#author-request-headers), chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`afeta as solicitações de simulação e o cabeçalho [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) . Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

Uma política de middleware CORS correspondente a cabeçalhos específicos especificados por `WithHeaders` é possível somente quando os cabeçalhos enviados `Access-Control-Request-Headers` exatamente correspondem aos cabeçalhos indicados em `WithHeaders` .

Por exemplo, considere um aplicativo configurado da seguinte maneira:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

O middleware CORS recusa uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` ([headernames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado em `WithHeaders` :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

O aplicativo retorna uma resposta de *200 OK* , mas não envia os cabeçalhos CORS de volta. Portanto, o navegador não tenta a solicitação entre origens.

### <a name="set-the-exposed-response-headers"></a>Definir os cabeçalhos de resposta expostos

Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo. Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).

Os cabeçalhos de resposta que estão disponíveis por padrão são:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*. Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Credenciais em solicitações entre origens

As credenciais exigem tratamento especial em uma solicitação CORS. Por padrão, o navegador não envia credenciais com uma solicitação entre origens. As credenciais incluem cookies e esquemas de autenticação HTTP. Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true` .

Usando `XMLHttpRequest` diretamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Usando o jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

O servidor deve permitir as credenciais. Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

A resposta HTTP inclui um `Access-Control-Allow-Credentials` cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.

Se o navegador enviar credenciais, mas a resposta não incluir um `Access-Control-Allow-Credentials` cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.

Permitir credenciais entre origens é um risco de segurança. Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

A especificação CORS também indica que a definição de origens para `"*"` (todas as origens) é inválida se o `Access-Control-Allow-Credentials` cabeçalho estiver presente.

<a name="pref"></a>

## <a name="preflight-requests"></a>Solicitações de simulação

Para algumas solicitações de CORS, o navegador envia uma solicitação de [Opções](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicional antes de fazer a solicitação real. Essa solicitação é chamada de uma [solicitação de simulação](https://developer.mozilla.org/docs/Glossary/Preflight_request). O navegador poderá ignorar a solicitação de simulação se **todas** as seguintes condições forem verdadeiras:

* O método de solicitação é GET, HEAD ou POST.
* O aplicativo não define cabeçalhos de solicitação diferentes de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` ou `Last-Event-ID` .
* O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente se aplica aos cabeçalhos que o aplicativo define chamando `setRequestHeader` no `XMLHttpRequest` objeto. A especificação CORS chama esses cabeçalhos de [solicitação de autor](https://www.w3.org/TR/cors/#author-request-headers)de cabeçalho. A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent` , `Host` ou `Content-Length` .

Veja a seguir um exemplo de resposta semelhante à solicitação de simulação feita no botão **[Put Test]** na seção [testar CORS](#testc) deste documento.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

A solicitação de simulação usa o método de [Opções http](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) . Ele pode incluir os seguintes cabeçalhos:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): o método http que será usado para a solicitação real.
* [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real. Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent` .
* [Acesso-controle-permitir-métodos](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Se a solicitação de simulação for negada, o aplicativo retornará uma `200 OK` resposta, mas não definirá os cabeçalhos CORS. Portanto, o navegador não tenta a solicitação entre origens. Para obter um exemplo de uma solicitação de simulação negada, consulte a seção [testar CORS](#testc) deste documento.

Usando as ferramentas F12, o aplicativo de console mostra um erro semelhante a um dos seguintes, dependendo do navegador:

* Firefox: solicitação entre origens bloqueada: a mesma política de origem não permite a leitura do recurso remoto em `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` . (Motivo: a solicitação CORS não teve sucesso). [Saiba mais](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Baseado em Chromium: o acesso à busca em ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' da origem ' https://cors3.azurewebsites.net ' foi bloqueado pela política de CORS: a resposta à solicitação de simulação não passou na verificação de controle de acesso: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado. Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.

Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir todos os [cabeçalhos de solicitação de autor](https://www.w3.org/TR/cors/#author-request-headers), chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Os navegadores não são consistentes em como eles são definidos `Access-Control-Request-Headers` . Se:

* Os cabeçalhos são definidos como algo diferente de`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>é chamado: inclua pelo menos `Accept` , `Content-Type` e `Origin` , além de todos os cabeçalhos personalizados aos quais você deseja dar suporte.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Código de solicitação de simulação automática

Quando a política CORS for aplicada:

* Globalmente chamando `app.UseCors` em `Startup.Configure` .
* Usando o `[EnableCors]` atributo.

ASP.NET Core responde à solicitação de simulação de opções.

Habilitar o CORS em uma base por ponto de extremidade usando `RequireCors` atualmente **não** oferece suporte a solicitações de simulação automáticas.

A seção de [CORS de teste](#testc) deste documento demonstra esse comportamento.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[Httpoptions] atributo para solicitações de simulação

Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde às solicitações de simulação de CORS automaticamente. Em alguns cenários, esse pode não ser o caso. Por exemplo, usando [CORS com roteamento de ponto de extremidade](#ecors).

O código a seguir usa o atributo [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para criar pontos de extremidade para solicitações de opções:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Consulte [testar CORS com o roteamento de ponto de extremidade e [httpoptions]](#tcer) para obter instruções sobre como testar o código anterior.

### <a name="set-the-preflight-expiration-time"></a>Definir o tempo de expiração de simulação

O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta para a solicitação de simulação pode ser armazenada em cache. Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Como o CORS funciona

Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens http.

* O CORS **não** é um recurso de segurança. O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.
  * Por exemplo, um ator mal-intencionado pode usar [XSS (script entre sites)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites em seu site habilitado para CORS para roubar informações.
* Uma API não é mais segura, permitindo CORS.
  * Cabe ao cliente (navegador) impor o CORS. O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta. Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Um navegador da Web digitando a URL na barra de endereços.
* É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.
  * Os navegadores sem CORS não podem fazer solicitações entre origens. Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição. JSONP não usa XHR, ele usa a `<script>` marca para receber a resposta. Os scripts podem ser carregados entre origens.

A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens. Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens. O código JavaScript personalizado não é necessário para habilitar o CORS.

O [botão Put Test](https://cors3.azurewebsites.net/test) no [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implantado

Veja a seguir um exemplo de uma solicitação entre origens do botão testar [valores](https://cors3.azurewebsites.net/) para `https://cors1.azurewebsites.net/api/values` . O `Origin` cabeçalho:

* Fornece o domínio do site que está fazendo a solicitação.
* É necessário e deve ser diferente do host.

**Cabeçalhos gerais**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Cabeçalhos de resposta**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Cabeçalhos de solicitação**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

Em `OPTIONS` solicitações, o servidor define o cabeçalho de **cabeçalhos de resposta** `Access-Control-Allow-Origin: {allowed origin}` na resposta. Por exemplo, a solicitação de botão de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada, [delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` contém os seguintes cabeçalhos:

**Cabeçalhos gerais**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Cabeçalhos de resposta**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Cabeçalhos de solicitação**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Nos cabeçalhos de **resposta**anteriores, o servidor define o cabeçalho [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) na resposta. O `https://cors1.azurewebsites.net` valor desse cabeçalho corresponde ao `Origin` cabeçalho da solicitação.

Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> for chamado, o `Access-Control-Allow-Origin: *` valor de curinga será retornado. `AllowAnyOrigin`permite qualquer origem.

Se a resposta não incluir o `Access-Control-Allow-Origin` cabeçalho, a solicitação entre origens falhará. Especificamente, o navegador não permite a solicitação. Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.

<a name="options"></a>

### <a name="display-options-requests"></a>Exibir solicitações de opções

Por padrão, os navegadores Chrome e Edge não mostram solicitações de opções na guia rede das ferramentas F12. Para exibir solicitações de opções nestes navegadores:

* `chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`
* Desabilite o sinalizador.
* reiniciar.

O Firefox mostra solicitações de opções por padrão.

## <a name="cors-in-iis"></a>CORS no IIS

Ao implantar no IIS, o CORS precisará ser executado antes da autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo. Para dar suporte a esse cenário, o [módulo CORS do IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.

<a name="testc"></a>

## <a name="test-cors"></a>Testar o CORS

O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tem código para testar CORS. Consulte [como baixar](xref:index#how-to-download-a-sample). O exemplo é um projeto de API com Razor páginas adicionadas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`Só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

O seguinte `ValuesController` fornece os pontos de extremidade para teste:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) é fornecido pelo [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet Package e exibe informações de rota.

Teste o código de exemplo anterior usando uma das seguintes abordagens:

* Use o aplicativo de exemplo implantado em [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) . Não é necessário baixar o exemplo.
* Execute o exemplo com `dotnet run` o uso da URL padrão de `https://localhost:5001` .
* Execute o exemplo do Visual Studio com a porta definida como 44398 para uma URL de `https://localhost:44398` .

Usando um navegador com as ferramentas F12:

* Selecione o botão **valores** e examine os cabeçalhos na guia **rede** .
* Selecione o botão **Put Test** . Consulte [Exibir opções solicitações](#options) para obter instruções sobre como exibir a solicitação de opções. O **teste Put** cria duas solicitações, uma solicitação de simulação de opções e a solicitação Put.
* Selecione o **`GetValues2 [DisableCors]`** botão para disparar uma solicitação de CORS com falha. Conforme mencionado no documento, a resposta retorna 200 êxito, mas a solicitação CORS não é feita. Selecione a guia **console** para ver o erro CORS. Dependendo do navegador, um erro semelhante ao seguinte é exibido:

     O acesso à busca `'https://cors1.azurewebsites.net/api/values/GetValues2'` da origem `'https://cors3.azurewebsites.net'` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado. Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.
     
Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como [ondulação](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)ou [postmaster](https://www.getpostman.com/). Ao usar uma ferramenta, a origem da solicitação especificada pelo `Origin` cabeçalho deve ser diferente do host que está recebendo a solicitação. Se a solicitação não for de *origem cruzada* com base no valor do `Origin` cabeçalho:

* Não há necessidade de middleware de CORS para processar a solicitação.
* Cabeçalhos CORS não são retornados na resposta.

O comando a seguir usa `curl` para emitir uma solicitação de opções com informações:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Testar CORS com roteamento de ponto de extremidade e [Httpoptions]

Habilitar o CORS em uma base por ponto de extremidade usando `RequireCors` atualmente **não** oferece suporte a solicitações de [simulação automáticas](#apf). Considere o código a seguir, que usa o [Roteamento de ponto de extremidade para habilitar CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

O seguinte `TodoItems1Controller` fornece pontos de extremidade para teste:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Teste o código anterior na [página de teste](https://cors1.azurewebsites.net/test?number=1) do [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantado.

Os botões **delete [EnableCors]** e **Get [EnableCors] são** bem-sucedidos, pois os pontos de extremidade têm `[EnableCors]` e respondem a solicitações de simulação. Os outros pontos de extremidade falham. O botão **Get** falha, porque o [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envia:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

O seguinte `TodoItems2Controller` fornece pontos de extremidade semelhantes, mas inclui código explícito para responder às solicitações de opções:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Teste o código anterior na [página de teste](https://cors1.azurewebsites.net/test?number=2) do exemplo implantado. Na lista suspensa **controlador** , selecione **simulação** e, em seguida, **definir controlador**. Todas as chamadas de CORS para os `TodoItems2Controller` pontos de extremidade são bem-sucedidos.

## <a name="additional-resources"></a>Recursos adicionais

* [CORS (Compartilhamento de Recursos entre Origens)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introdução ao módulo CORS do IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo. Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).

CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):

* É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.
* **Não** é um recurso de segurança, o CORS libera a segurança. Uma API não é mais segura, permitindo CORS. Para obter mais informações, consulte [como o CORS funciona](#how-cors).
* Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.
* É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mesma origem

Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Essas duas URLs têm a mesma origem:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Essas URLs têm origens diferentes das duas URLs anteriores:

* `https://example.net`: Domínio diferente
* `https://www.example.com/foo.html`: Subdomínio diferente
* `http://example.com/foo.html`: Esquema diferente
* `https://example.com:9000/foo.html`: Porta diferente

O Internet Explorer não considera a porta ao comparar origens.

## <a name="cors-with-named-policy-and-middleware"></a>CORS com política nomeada e middleware

O middleware CORS lida com solicitações entre origens. O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

O código anterior:

* Define o nome da política como " \_ myAllowSpecificOrigins". O nome da política é arbitrário.
* Chama o <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensão, que habilita o CORS.
* Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). O lambda pega um <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto. [Opções de configuração](#cors-policy-options), como `WithOrigins` , são descritas posteriormente neste artigo.

A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada de método adiciona serviços CORS ao contêiner de serviço do aplicativo:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.

O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode encadear métodos, conforme mostrado no código a seguir:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Observação: a URL **não** deve conter uma barra à direita ( `/` ). Se a URL for encerrada com `/` , a comparação retornará `false` e nenhum cabeçalho será retornado.

O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Observação: `UseCors` deve ser chamado antes de `UseMvc` .

Consulte [habilitar CORS em Razor páginas, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.

Consulte [testar CORS](#test) para obter instruções sobre o código de teste semelhante ao código anterior.

## <a name="enable-cors-with-attributes"></a>Habilitar CORS com atributos

O atributo [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente. O `[EnableCors]` atributo habilita o CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.

Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.

O `[EnableCors]` atributo pode ser aplicado a:

* RazorWeb`PageModel`
* Controller
* Método de ação do controlador

Você pode aplicar políticas diferentes ao controlador/página-modelo/ação com o `[EnableCors]` atributo. Quando o `[EnableCors]` atributo é aplicado a um método de controlador/modelo de página/ação e o CORS é habilitado no middleware, **ambas** as políticas são aplicadas. Recomendamos **não** combinar políticas. Use o `[EnableCors]` atributo ou middleware, **não ambos**. Ao usar `[EnableCors]` , **não** defina uma política padrão.

O código a seguir aplica uma política diferente a cada método:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

O código a seguir cria uma política padrão CORS e uma política chamada `"AnotherPolicy"` :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Desabilitar CORS

O atributo [ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) desabilita o CORS para o controlador/página-modelo/ação.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opções de política de CORS

Esta seção descreve as várias opções que podem ser definidas em uma política CORS:

* [Definir as origens permitidas](#set-the-allowed-origins)
* [Definir os métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Definir os cabeçalhos de solicitação permitidos](#set-the-allowed-request-headers)
* [Definir os cabeçalhos de resposta expostos](#set-the-exposed-response-headers)
* [Credenciais em solicitações entre origens](#credentials-in-cross-origin-requests)
* [Definir o tempo de expiração de simulação](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado em `Startup.ConfigureServices` . Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.

## <a name="set-the-allowed-origins"></a>Definir as origens permitidas

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitações CORS de todas as origens com qualquer esquema ( `http` ou `https` ). `AllowAnyOrigin`o não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.

> [!NOTE]
> Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites. Para um aplicativo seguro, especifique uma lista exata de origens se o cliente precisar se autorizar para acessar os recursos do servidor.

`AllowAnyOrigin`afeta as solicitações de simulação e o `Access-Control-Allow-Origin` cabeçalho. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Define a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriedade da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Definir os métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite qualquer método HTTP:
* Afeta as solicitações de simulação e o `Access-Control-Allow-Methods` cabeçalho. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Definir os cabeçalhos de solicitação permitidos

Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada *criar cabeçalhos de solicitação de autor*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Essa configuração afeta as solicitações de simulação e o `Access-Control-Request-Headers` cabeçalho. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

O middleware CORS sempre permite que quatro cabeçalhos no `Access-Control-Request-Headers` sejam enviados, independentemente dos valores configurados em CorsPolicy. Headers. Essa lista de cabeçalhos inclui:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Por exemplo, considere um aplicativo configurado da seguinte maneira:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

O middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` sempre está na lista de permissões:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Definir os cabeçalhos de resposta expostos

Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo. Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).

Os cabeçalhos de resposta que estão disponíveis por padrão são:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*. Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Credenciais em solicitações entre origens

As credenciais exigem tratamento especial em uma solicitação CORS. Por padrão, o navegador não envia credenciais com uma solicitação entre origens. As credenciais incluem cookies e esquemas de autenticação HTTP. Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true` .

Usando `XMLHttpRequest` diretamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Usando o jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

O servidor deve permitir as credenciais. Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

A resposta HTTP inclui um `Access-Control-Allow-Credentials` cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.

Se o navegador enviar credenciais, mas a resposta não incluir um `Access-Control-Allow-Credentials` cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.

Permitir credenciais entre origens é um risco de segurança. Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

A especificação CORS também indica que a definição de origens para `"*"` (todas as origens) é inválida se o `Access-Control-Allow-Credentials` cabeçalho estiver presente.

### <a name="preflight-requests"></a>Solicitações de simulação

Para algumas solicitações de CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real. Essa solicitação é chamada de uma *solicitação de simulação*. O navegador poderá ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:

* O método de solicitação é GET, HEAD ou POST.
* O aplicativo não define cabeçalhos de solicitação diferentes de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` ou `Last-Event-ID` .
* O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente se aplica aos cabeçalhos que o aplicativo define chamando `setRequestHeader` no `XMLHttpRequest` objeto. A especificação CORS chama esses cabeçalhos de *solicitação de autor*de cabeçalho. A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent` , `Host` ou `Content-Length` .

Veja a seguir um exemplo de uma solicitação de simulação:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

A solicitação de simulação usa o método de opções HTTP. Ele inclui dois cabeçalhos especiais:

* `Access-Control-Request-Method`: O método HTTP que será usado para a solicitação real.
* `Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real. Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent` .

<!-- I think this needs to be removed, was put here accidently -->

Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de simulação de CORS. Consulte o [atributo [httpoptions] para solicitações de simulação](#pro).

Uma solicitação de simulação de CORS pode incluir um `Access-Control-Request-Headers` cabeçalho, que indica ao servidor os cabeçalhos que são enviados com a solicitação real.

Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Os navegadores não são totalmente consistentes em como eles são definidos `Access-Control-Request-Headers` . Se você definir cabeçalhos para algo diferente de `"*"` (ou usar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), deverá incluir pelo menos `Accept` , `Content-Type` e `Origin` , além de todos os cabeçalhos personalizados aos quais deseja dar suporte.

Veja a seguir um exemplo de resposta para a solicitação de simulação (supondo que o servidor permita a solicitação):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

A resposta inclui um `Access-Control-Allow-Methods` cabeçalho que lista os métodos permitidos e, opcionalmente `Access-Control-Allow-Headers` , um cabeçalho, que lista os cabeçalhos permitidos. Se a solicitação de simulação for realizada com sucesso, o navegador enviará a solicitação real.

Se a solicitação de simulação for negada, o aplicativo retornará uma resposta *200 OK* , mas não enviará os cabeçalhos CORS de volta. Portanto, o navegador não tenta a solicitação entre origens.

### <a name="set-the-preflight-expiration-time"></a>Definir o tempo de expiração de simulação

O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta para a solicitação de simulação pode ser armazenada em cache. Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Como o CORS funciona

Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens http.

* O CORS **não** é um recurso de segurança. O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.
  * Por exemplo, um ator mal-intencionado poderia usar a [prevenção de scripts entre sites (XSS)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites para o site habilitado para CORS para roubar informações.
* Sua API não é mais segura, permitindo CORS.
  * Cabe ao cliente (navegador) impor o CORS. O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta. Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Um navegador da Web digitando a URL na barra de endereços.
* É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.
  * Os navegadores (sem CORS) não podem fazer solicitações entre origens. Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição. JSONP não usa XHR, ele usa a `<script>` marca para receber a resposta. Os scripts podem ser carregados entre origens.

A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens. Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens. O código JavaScript personalizado não é necessário para habilitar o CORS.

Veja a seguir um exemplo de uma solicitação entre origens. O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação. O `Origin` cabeçalho é necessário e deve ser diferente do host.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Se o servidor permitir a solicitação, ele definirá o `Access-Control-Allow-Origin` cabeçalho na resposta. O valor desse cabeçalho corresponde ao `Origin` cabeçalho da solicitação ou é o valor curinga `"*"` , o que significa que qualquer origem é permitida:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Se a resposta não incluir o `Access-Control-Allow-Origin` cabeçalho, a solicitação entre origens falhará. Especificamente, o navegador não permite a solicitação. Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.

<a name="test"></a>

## <a name="test-cors"></a>Testar o CORS

Para testar o CORS:

1. [Criar um projeto de API](xref:tutorials/first-web-api). Como alternativa, você pode [baixar o exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Habilite o CORS usando uma das abordagens deste documento. Por exemplo:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`Só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Criar um projeto de aplicativo Web ( Razor páginas ou Mvc). O exemplo usa Razor páginas. Você pode criar o aplicativo Web na mesma solução que o projeto de API.
1. Adicione o seguinte código realçado ao arquivo *index. cshtml* :

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` pela URL para o aplicativo implantado.
1. Implante o projeto de API. Por exemplo, [implante no Azure](xref:host-and-deploy/azure-apps/index).
1. Execute as Razor páginas ou o aplicativo MVC na área de trabalho e clique no botão **testar** . Use as ferramentas F12 para examinar mensagens de erro.
1. Remova a origem do localhost de `WithOrigins` e implante o aplicativo. Como alternativa, execute o aplicativo cliente com uma porta diferente. Por exemplo, execute do Visual Studio.
1. Teste com o aplicativo cliente. As falhas de CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript. Use a guia Console nas Ferramentas F12 para ver o erro. Dependendo do navegador, você receberá um erro (no console de ferramentas F12) semelhante ao seguinte:

   * Usando o Microsoft Edge:

     **SEC7120: [CORS] a origem `https://localhost:44375` não foi encontrada `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para o recurso entre origens em`https://webapi.azurewebsites.net/api/values/1`**

   * Usando o Chrome:

     **O acesso a XMLHttpRequest de `https://webapi.azurewebsites.net/api/values/1` origem `https://localhost:44375` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.**
     
Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/). Ao usar uma ferramenta, a origem da solicitação especificada pelo `Origin` cabeçalho deve ser diferente do host que está recebendo a solicitação. Se a solicitação não for de *origem cruzada* com base no valor do `Origin` cabeçalho:

* Não há necessidade de middleware de CORS para processar a solicitação.
* Cabeçalhos CORS não são retornados na resposta.

## <a name="cors-in-iis"></a>CORS no IIS

Ao implantar no IIS, o CORS precisará ser executado antes da autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo. Para dar suporte a esse cenário, o [módulo CORS do IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.

## <a name="additional-resources"></a>Recursos adicionais

* [CORS (Compartilhamento de Recursos entre Origens)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introdução ao módulo CORS do IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
