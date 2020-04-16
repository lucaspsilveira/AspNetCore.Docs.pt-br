---
title: Habilite o CORS (Cross-Origin Requests, solicitação de origem cruzada) em ASP.NET Núcleo
author: rick-anderson
description: Saiba como o CORS como padrão para permitir ou rejeitar solicitações de origem cruzada em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440903"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Habilite o CORS (Cross-Origin Requests, solicitação de origem cruzada) em ASP.NET Núcleo

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)

Este artigo mostra como ativar o CORS em um aplicativo ASP.NET Core.

A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web. Essa restrição é chamada *de política de mesma origem.* A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Às vezes, você pode querer permitir que outros sites façam solicitações de origem cruzada para o seu aplicativo. Para obter mais informações, consulte o artigo da [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Compartilhamento de recursos de origem cruzada](https://www.w3.org/TR/cors/) (CORS):

* É um padrão W3C que permite que um servidor relaxe a política de mesma origem.
* **Não** é um recurso de segurança, o CORS relaxa a segurança. Uma API não é mais segura ao permitir o CORS. Para obter mais informações, consulte [Como funciona o CORS](#how-cors).
* Permite que um servidor permita explicitamente algumas solicitações de origem cruzada enquanto rejeita outros.
* É mais seguro e flexível do que as técnicas anteriores, como [jsonp](/dotnet/framework/wcf/samples/jsonp).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mesma origem

Dois URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Estes dois URLs têm a mesma origem:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Esses URLs têm origens diferentes das duas URLs anteriores:

* `https://example.net`&ndash; Domínio diferente
* `https://www.example.com/foo.html`&ndash; Subdomínio diferente
* `http://example.com/foo.html`&ndash; Esquema diferente
* `https://example.com:9000/foo.html`&ndash; Porta diferente

## <a name="enable-cors"></a>Habilitar CORS

Existem três maneiras de habilitar o CORS:

* Em middleware usando uma [diretiva nomeada](#np) ou [política padrão](#dp).
* Usando [roteamento de ponto final](#ecors).
* Com o atributo [[EnableCors].](#attr)

O uso do atributo [[EnableCors]](#attr) com uma diretiva nomeada fornece o melhor controle na limitação de pontos finais que suportam o CORS.

Cada abordagem é detalhada nas seções a seguir.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS com política nomeada e middleware

O CORS Middleware lida com solicitações de origem cruzada. O código a seguir aplica uma diretiva CORS a todos os pontos finais do aplicativo com as origens especificadas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

O código anterior:

* Define o nome `_myAllowSpecificOrigins`da política para . O nome da política é arbitrário.
* Chama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> o método de `_myAllowSpecificOrigins` extensão e especifica a política cors. `UseCors`adiciona o middleware CORS.
* Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) A lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> leva um objeto. [As opções de configuração,](#cors-policy-options)como `WithOrigins`, são descritas posteriormente neste artigo.
* Habilita `_myAllowSpecificOrigins` a política CORS para todos os pontos finais do controlador. Consulte [o roteamento de ponto final](#ecors) para aplicar uma política CORS a pontos finais específicos.

Com o roteamento de ponto final, o middleware CORS `UseEndpoints` ***deve*** ser configurado para ser executado entre as chamadas para `UseRouting` e .

Consulte [Test CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.

A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada do método adiciona serviços CORS ao contêiner de serviço do aplicativo:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obter mais informações, consulte [as opções de política](#cpo) do CORS neste documento.

Os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos podem ser acorrentados, conforme mostrado no seguinte código:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Nota: A URL especificada **não** deve`/`conter uma barra de arrasto (). Se a URL `/`terminar com `false` , a comparação retorna e nenhum cabeçalho é devolvido.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS com política padrão e middleware

O código destacado a seguir permite a política CORS padrão:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

O código anterior aplica a diretiva CORS padrão a todos os pontos finais do controlador.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Habilite cors com roteamento de ponto final

A habilitação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta [solicitações automáticas de pré-vôo](#apf). Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [teste o CORS com roteamento de ponto final e [HttpOptions]](#tcer).

Com o roteamento de ponto final, o CORS pode <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ser habilitado por ponto final usando o conjunto de métodos de extensão:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

No código anterior:

* `app.UseCors`habilita o middleware CORS. Como uma diretiva padrão não foi `app.UseCors()` configurada, sozinha não habilita o CORS.
* Os `/echo` pontos finais do controlador permitem solicitações de origem cruzada usando a diretiva especificada.
* Os `/echo2` pontos finais e as páginas de navalha ***não*** permitem solicitações de origem cruzada porque nenhuma política padrão foi especificada.

O atributo [[DesativarCors]](#dc) ***não*** desabilita o CORS habilitado pelo `RequireCors`roteamento de ponto final com .

Consulte [Test CORS com roteamento de ponto final e [HttpOptions]](#tcer) para obter instruções sobre o código de teste semelhante ao anterior.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Habilite o CORS com atributos

Habilitar o CORS com o atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e aplicar uma diretiva nomeada apenas aos pontos finais que requerem cors fornece o melhor controle.

O atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa à aplicação do CORS globalmente. O `[EnableCors]` atributo habilita o CORS para pontos finais selecionados, em vez de todos os pontos finais:

* `[EnableCors]`especifica a política padrão.
* `[EnableCors("{Policy String}")]`especifica uma política nomeada.

O `[EnableCors]` atributo pode ser aplicado a:

* Página navalha`PageModel`
* Controller
* Método de ação do controlador

Diferentes políticas podem ser aplicadas a controladores, modelos de página ou métodos de ação com o atributo. `[EnableCors]` Quando `[EnableCors]` o atributo é aplicado a um controlador, modelo de página ou método de ação, e o CORS é habilitado em middleware, ***ambas as*** políticas são aplicadas. ***Recomendamos contra a combinação de políticas. Use o atributo*** `[EnableCors]` ***ou middleware, não ambos no mesmo aplicativo.***

O código a seguir aplica uma política diferente a cada método:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

O código a seguir cria duas políticas cors:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Para o melhor controle de limitar as solicitações do CORS:

* Use `[EnableCors("MyPolicy")]` com uma política nomeada.
* Não defina uma política padrão.
* Não use [roteamento de ponto final](#ecors).

O código na próxima seção atende à lista anterior.

Consulte [Test CORS](#testc) para obter instruções sobre o código de teste semelhante ao código anterior.

<a name="dc"></a>

### <a name="disable-cors"></a>Desativar o CORS

O atributo [[DesativarCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***não*** desabilita o CORS habilitado pelo [roteamento de ponto final](#ecors).

O código a seguir define `"MyPolicy"`a política cors:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

O código a seguir desativa o CORS para a `GetValues2` ação:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

O código anterior:

* Não habilita o CORS com [roteamento de ponto final](#ecors).
* Não define uma [política padrão do CORS.](#dp)
* Usa [[EnableCors("MyPolicy")]](#attr) para `"MyPolicy"` habilitar a diretiva CORS para o controlador.
* Desabilita o CORS para o `GetValues2` método.

Consulte [O Cors de teste](#testc) para obter instruções sobre o teste do código anterior.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opções de política do CORS

Esta seção descreve as várias opções que podem ser definidas em uma política cors:

* [Defina as origens permitidas](#set-the-allowed-origins)
* [Defina os métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Defina os cabeçalhos de solicitação permitidos](#set-the-allowed-request-headers)
* [Defina os cabeçalhos de resposta expostos](#set-the-exposed-response-headers)
* [Credenciais em solicitações de origem cruzada](#credentials-in-cross-origin-requests)
* [Defina o tempo de expiração do pré-vôo](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado `Startup.ConfigureServices`dentro . Para algumas opções, pode ser útil ler a seção Como o [CORS funciona](#how-cors) primeiro.

## <a name="set-the-allowed-origins"></a>Defina as origens permitidas

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitações do CORS de`http` todas `https`as origens com qualquer esquema (ou ). `AllowAnyOrigin`é inseguro porque *qualquer site* pode fazer solicitações de origem cruzada para o aplicativo.

> [!NOTE]
> Especificar `AllowAnyOrigin` `AllowCredentials` e é uma configuração insegura e pode resultar em falsificação de solicitação entre sites. O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.

`AllowAnyOrigin`afeta pedidos de pré-vôo e o `Access-Control-Allow-Origin` cabeçalho. Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Define <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> a propriedade da diretiva como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Defina os métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite qualquer método HTTP:
* Afeta as solicitações `Access-Control-Allow-Methods` de pré-vôo e o cabeçalho. Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Defina os cabeçalhos de solicitação permitidos

Para permitir que cabeçalhos específicos sejam enviados em uma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> solicitação cors, chamados [cabeçalhos de solicitação do autor,](https://xhr.spec.whatwg.org/#request)ligue e especifique os cabeçalhos permitidos:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [cabeçalhos de solicitação do autor,](https://www.w3.org/TR/cors/#author-request-headers)ligue:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`afeta as solicitações de pré-vôo e o [cabeçalho access-control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)

Uma correspondência de diretiva CORS Middleware `WithHeaders` para cabeçalhos específicos `Access-Control-Request-Headers` especificados só é `WithHeaders`possível quando os cabeçalhos enviados correspondem exatamente aos cabeçalhos indicados em .

Por exemplo, considere um aplicativo configurado da seguinte forma:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

O CORS Middleware recusa uma solicitação de `Content-Language` pré-vôo com o cabeçalho `WithHeaders`de solicitação a seguir porque[(HeaderNames.ContentLanguage)](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)não está listado em :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

O aplicativo retorna uma resposta *de 200 OK,* mas não envia os cabeçalhos cors de volta. Portanto, o navegador não tenta a solicitação de origem cruzada.

### <a name="set-the-exposed-response-headers"></a>Defina os cabeçalhos de resposta expostos

Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo. Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (Terminologia): Simples Cabeçalho de Resposta](https://www.w3.org/TR/cors/#simple-response-header).

Os cabeçalhos de resposta disponíveis por padrão são:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

A especificação cors chama esses cabeçalhos *de cabeçalho simples cabeçalhos de resposta*. Para disponibilizar outros cabeçalhos <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>para o aplicativo, ligue para:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Credenciais em solicitações de origem cruzada

As credenciais requerem tratamento especial em uma solicitação do CORS. Por padrão, o navegador não envia credenciais com uma solicitação de origem cruzada. As credenciais incluem cookies e esquemas de autenticação HTTP. Para enviar credenciais com uma solicitação de `XMLHttpRequest.withCredentials` `true`origem cruzada, o cliente deve definir para .

Usando `XMLHttpRequest` diretamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Usando jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

O servidor deve permitir as credenciais. Para permitir credenciais de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origem cruzada, ligue para:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

A resposta HTTP `Access-Control-Allow-Credentials` inclui um cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação de origem cruzada.

Se o navegador enviar credenciais, mas `Access-Control-Allow-Credentials` a resposta não incluir um cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação de origem cruzada falhará.

Permitir credenciais de origem cruzada é um risco para a segurança. Um site em outro domínio pode enviar credenciais de um usuário de entrada para o aplicativo em nome do usuário sem o conhecimento do usuário. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

A especificação do CORS também `"*"` afirma que a configuração de `Access-Control-Allow-Credentials` origens para (todas as origens) é inválida se o cabeçalho estiver presente.

<a name="pref"></a>

## <a name="preflight-requests"></a>Solicitações de pré-vôo

Para algumas solicitações do CORS, o navegador envia uma solicitação de [OPÇÕES](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicionais antes de fazer a solicitação real. Este pedido é chamado de [solicitação de pré-vôo.](https://developer.mozilla.org/docs/Glossary/Preflight_request) O navegador pode ignorar a solicitação de pré-vôo se ***todas as*** seguintes condições forem verdadeiras:

* O método de solicitação é GET, HEAD ou POST.
* O aplicativo não define cabeçalhos `Content-Language`de `Content-Type`solicitação `Last-Event-ID`que não senão, `Accept` `Accept-Language`ou .
* O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

A regra sobre cabeçalhos de solicitação definidos para a `setRequestHeader` solicitação `XMLHttpRequest` do cliente se aplica aos cabeçalhos que o aplicativo define chamando o objeto. A especificação cors chama esses cabeçalhos [autor solicitar cabeçalhos](https://www.w3.org/TR/cors/#author-request-headers). A regra não se aplica aos cabeçalhos `User-Agent` `Host`que `Content-Length`o navegador pode definir, tais como , ou .

A seguir, uma resposta de exemplo semelhante à solicitação de pré-vôo feita a partir do botão **[Colocar teste]** na seção [Test CORS](#testc) deste documento.

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

A solicitação de pré-vôo usa o método [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Pode incluir os seguintes cabeçalhos:

* [Método de solicitação de controle de acesso](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): O método HTTP que será usado para a solicitação real.
* [Cabeçalhos de solicitação de controle de acesso](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real. Como dito anteriormente, isso não inclui cabeçalhos `User-Agent`que o navegador define, como .
* [Métodos de permitir controle de acesso](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Se a solicitação de pré-vôo `200 OK` for negada, o aplicativo retorna uma resposta, mas não define os cabeçalhos cors. Portanto, o navegador não tenta a solicitação de origem cruzada. Para obter um exemplo de solicitação de pré-vôo negada, consulte a seção [Teste CORS](#testc) deste documento.

Usando as ferramentas f12, o aplicativo do console mostra um erro semelhante a um dos seguintes, dependendo do navegador:

* Firefox: Solicitação de origem cruzada bloqueada: a mesma política de `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origem não permite a leitura do recurso remoto em . (Motivo: pedido do CORS não teve sucesso). [Saiba mais](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Baseado em cromo: O acessohttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5para buscarhttps://cors3.azurewebsites.netem ' from origin ' foi bloqueado pela política cors: A resposta à solicitação de pré-vôo não passa pela verificação de controle de acesso: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado. Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.

Para permitir cabeçalhos específicos, ligue para: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [cabeçalhos de solicitação do autor,](https://www.w3.org/TR/cors/#author-request-headers)ligue:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Os navegadores não são consistentes na forma como definem `Access-Control-Request-Headers`. Se um deles:

* Cabeçalhos são definidos para qualquer outra coisa que não`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>é chamado: Incluir `Accept` `Content-Type`pelo `Origin`menos , e , além de quaisquer cabeçalhos personalizados que você deseja suportar.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Código automático de solicitação de pré-vôo

Quando a política cors é aplicada ou:

* Globalmente, `app.UseCors` `Startup.Configure`chamando.
* Usando `[EnableCors]` o atributo.

ASP.NET Core responde à solicitação de OPÇÕES de pré-vôo.

A ativação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta solicitações automáticas de pré-vôo.

A seção [Teste CORS](#testc) deste documento demonstra esse comportamento.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] atributo para solicitações de pré-vôo

Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de pré-vôo do CORS. Em alguns cenários, isso pode não ser o caso. Por exemplo, usar [o CORS com roteamento de ponto final](#ecors).

O código a seguir usa o atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para criar pontos finais para solicitações de OPÇÕES:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Consulte [Test CORS com roteamento de ponto final e [HttpOptions]](#tcer) para obter instruções sobre o teste do código anterior.

### <a name="set-the-preflight-expiration-time"></a>Defina o tempo de expiração do pré-vôo

O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta à solicitação de pré-vôo pode ser armazenada em cache. Para definir este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>cabeçalho, ligue:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Como funciona o CORS

Esta seção descreve o que acontece em uma solicitação [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens HTTP.

* Cors **não** é um recurso de segurança. Cors é um padrão W3C que permite que um servidor relaxe a política de mesma origem.
  * Por exemplo, um ator mal-intencionado poderia usar [o Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contra seu site e executar uma solicitação entre sites para o seu site habilitado para cors para roubar informações.
* Uma API não é mais segura permitindo o CORS.
  * Cabe ao cliente (navegador) fazer valer o CORS. O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta. Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Um navegador da Web inserindo a URL na barra de endereços.
* É uma maneira de um servidor permitir que os navegadores executem um [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) de origem cruzada ou solicitação [de API de origem cruzada](https://developer.mozilla.org/docs/Web/API/Fetch_API) que de outra forma seria proibido.
  * Navegadores sem CORS não podem fazer solicitações de origem cruzada. Antes do CORS, [o JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) era usado para contornar essa restrição. JSONP não usa XHR, usa `<script>` a tag para receber a resposta. Os scripts podem ser carregados de origem cruzada.

A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem solicitações de origem cruzada. Se um navegador suportar o CORS, ele define esses cabeçalhos automaticamente para solicitações de origem cruzada. O código JavaScript personalizado não é necessário para ativar o CORS.

O [botão de teste PUT](https://cors3.azurewebsites.net/test) na [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implantada

A seguir, um exemplo de uma solicitação de `https://cors1.azurewebsites.net/api/values`origem cruzada do botão de teste [Valores](https://cors3.azurewebsites.net/) para . O `Origin` cabeçalho:

* Fornece o domínio do site que está fazendo a solicitação.
* É necessário e deve ser diferente do hospedeiro.

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

**Cabeçalhos da solicitação**

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

Nas `OPTIONS` solicitações, o servidor define o **cabeçalho de cabeçalho de** `Access-Control-Allow-Origin: {allowed origin}` resposta na resposta. Por exemplo, a solicitação de botão `OPTIONS` ['Excluir ' 'EnableCors]](https://cors1.azurewebsites.net/test?number=2) de [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada contém os seguintes cabeçalhos:

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

**Cabeçalhos da solicitação**

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

Nos **cabeçalhos de resposta anteriores,** o servidor define o [cabeçalho Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) na resposta. O `https://cors1.azurewebsites.net` valor deste cabeçalho corresponde ao `Origin` cabeçalho da solicitação.

Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> for chamado, o `Access-Control-Allow-Origin: *`valor do curinga é devolvido. `AllowAnyOrigin`permite qualquer origem.

Se a resposta não `Access-Control-Allow-Origin` incluir o cabeçalho, a solicitação de origem cruzada falhará. Especificamente, o navegador não permite a solicitação. Mesmo que o servidor retorne uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.

<a name="options"></a>

### <a name="display-options-requests"></a>Solicitações de opções de exibição

Por padrão, os navegadores Chrome e Edge não mostram solicitações OPTIONS na guia de rede das ferramentas F12. Para exibir solicitações OPTIONS nesses navegadores:

* `chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`
* desativar a bandeira.
* Reiniciar.

O Firefox mostra as solicitações OPTIONS por padrão.

## <a name="cors-in-iis"></a>CORS no IIS

Ao implantar no IIS, o CORS deve ser executado antes da Autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo. Para suportar esse cenário, o [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.

<a name="testc"></a>

## <a name="test-cors"></a>Testar o CORS

O [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tem código para testar o CORS. Consulte [como baixar](xref:index#how-to-download-a-sample). A amostra é um projeto de API com páginas de barbear adicionadas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`só deve ser usado para testar um aplicativo de amostra semelhante ao [código de amostra de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

O `ValuesController` seguinte fornece os pontos finais para testes:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[O MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) é fornecido pelo pacote [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet e exibe informações de rota.

Teste o código de amostra anterior usando uma das seguintes abordagens:

* Use o aplicativo [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)de amostra implantado em . Não há necessidade de baixar a amostra.
* Execute a `dotnet run` amostra usando a `https://localhost:5001`URL padrão de .
* Execute a amostra do Visual Studio com a porta definida `https://localhost:44398`como 44398 para uma URL de .

Usando um navegador com as ferramentas f12:

* Selecione o botão **Valores** e revise os cabeçalhos na guia **Rede.**
* Selecione o botão **de teste PUT.** Consulte [as solicitações de opções de exibição](#options) para exibir a solicitação OPÇÕES. O **teste PUT** cria duas solicitações, uma solicitação de pré-vôo OPÇÕES e a solicitação PUT.
* Selecione **`GetValues2 [DisableCors]`** o botão para acionar uma solicitação CORS com falha. Como mencionado no documento, a resposta retorna 200 de sucesso, mas a solicitação do CORS não é feita. Selecione a guia **Console** para ver o erro cors. Dependendo do navegador, um erro semelhante ao seguinte é exibido:

     O acesso `'https://cors1.azurewebsites.net/api/values/GetValues2'` ao `'https://cors3.azurewebsites.net'` fetch at from origin foi bloqueado pela política cors: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado. Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.
     
Os pontos finais habilitados para CORS podem ser testados com uma ferramenta, como [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)ou [Carteiro](https://www.getpostman.com/). Ao usar uma ferramenta, a origem da `Origin` solicitação especificada pelo cabeçalho deve diferir do host que recebe a solicitação. Se a solicitação não *for de origem* cruzada `Origin` com base no valor do cabeçalho:

* Não há necessidade de o CORS Middleware processar a solicitação.
* Os cabeçalhos cors não são devolvidos na resposta.

O comando `curl` a seguir é use para emitir uma solicitação DE OPÇÕES com informações:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Teste o CORS com roteamento de ponto final e [HttpOptions]

A habilitação do CORS por `RequireCors` ponto final usando atualmente ***não*** suporta [solicitações automáticas de pré-vôo](#apf). Considere o seguinte código que usa [roteamento de ponto final para habilitar o CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

O `TodoItems1Controller` seguinte fornece pontos finais para testes:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Teste o código anterior da página de [teste](https://cors1.azurewebsites.net/test?number=1) da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implantada .

Os botões **Delete [EnableCors]** e **GET [EnableCors]** são `[EnableCors]` bem sucedidos, pois os pontos finais têm e respondem às solicitações de pré-vôo. Os outros pontos finais falham. O botão **GET** falha, porque o [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envia:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

O `TodoItems2Controller` seguinte fornece pontos finais semelhantes, mas inclui código explícito para responder às solicitações OPTIONS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Teste o código anterior da página de [teste](https://cors1.azurewebsites.net/test?number=2) da amostra implantada. Na lista de paradas **do Controlador,** selecione **Preflight** e, em seguida, **Defina controlador**. Todas as chamadas do `TodoItems2Controller` CORS para os pontos finais são bem sucedidas.

## <a name="additional-resources"></a>Recursos adicionais

* [CORS (Compartilhamento de Recursos entre Origens)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Começando com o módulo IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como ativar o CORS em um aplicativo ASP.NET Core.

A segurança do navegador impede que uma página da Web seja feita para um domínio diferente daquele que serviu a página da Web. Essa restrição é chamada *de política de mesma origem.* A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Às vezes, você pode querer permitir que outros sites façam solicitações de origem cruzada para o seu aplicativo. Para obter mais informações, consulte o artigo da [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Compartilhamento de recursos de origem cruzada](https://www.w3.org/TR/cors/) (CORS):

* É um padrão W3C que permite que um servidor relaxe a política de mesma origem.
* **Não** é um recurso de segurança, o CORS relaxa a segurança. Uma API não é mais segura ao permitir o CORS. Para obter mais informações, consulte [Como funciona o CORS](#how-cors).
* Permite que um servidor permita explicitamente algumas solicitações de origem cruzada enquanto rejeita outros.
* É mais seguro e flexível do que as técnicas anteriores, como [jsonp](/dotnet/framework/wcf/samples/jsonp).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mesma origem

Dois URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Estes dois URLs têm a mesma origem:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Esses URLs têm origens diferentes das duas URLs anteriores:

* `https://example.net`&ndash; Domínio diferente
* `https://www.example.com/foo.html`&ndash; Subdomínio diferente
* `http://example.com/foo.html`&ndash; Esquema diferente
* `https://example.com:9000/foo.html`&ndash; Porta diferente

O Internet Explorer não considera a porta ao comparar origens.

## <a name="cors-with-named-policy-and-middleware"></a>CORS com política nomeada e middleware

O CORS Middleware lida com solicitações de origem cruzada. O código a seguir permite o CORS para todo o aplicativo com a origem especificada:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

O código anterior:

* Define o nome\_da política como "myAllowSpecificOrigins". O nome da política é arbitrário.
* Chama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> o método de extensão, que habilita o CORS.
* Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) A lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> leva um objeto. [As opções de configuração,](#cors-policy-options)como `WithOrigins`, são descritas posteriormente neste artigo.

A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada do método adiciona serviços CORS ao contêiner de serviço do aplicativo:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obter mais informações, consulte [as opções de política](#cpo) do CORS neste documento .

O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode acorrentar métodos, conforme mostrado no código a seguir:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Nota: A URL **não** deve`/`conter uma barra de arrasto (). Se a URL `/`terminar com `false` , a comparação retorna e nenhum cabeçalho é devolvido.

O código a seguir aplica as políticas cors a todos os pontos finais de aplicativos via CORS Middleware:
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
Nota: `UseCors` deve `UseMvc`ser chamado antes .

Consulte [Ativar cors em páginas de barbear, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.

Consulte [Test CORS](#test) para obter instruções sobre o código de teste semelhante ao código anterior.

## <a name="enable-cors-with-attributes"></a>Habilite o CORS com atributos

O atributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa à aplicação do CORS globalmente. O `[EnableCors]` atributo habilita o CORS para pontos finais selecionados, em vez de todos os pontos finais.

Use `[EnableCors]` para especificar `[EnableCors("{Policy String}")]` a política padrão e para especificar uma diretiva.

O `[EnableCors]` atributo pode ser aplicado a:

* Página navalha`PageModel`
* Controller
* Método de ação do controlador

Você pode aplicar diferentes políticas ao controlador/modelo de página/ação com o atributo. `[EnableCors]` Quando `[EnableCors]` o atributo é aplicado a um método de controle/modelo/ação de página e o CORS é habilitado em middleware, ***ambas as*** políticas são aplicadas. Recomendamos ***não*** combinar políticas. Use `[EnableCors]` o atributo ou middleware, ***não ambos**. Ao `[EnableCors]`usar, **não** defina uma política padrão.

O código a seguir aplica uma política diferente a cada método:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

O código a seguir cria uma política `"AnotherPolicy"`padrão cors e uma diretiva chamada :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Desativar o CORS

O atributo [ &lbrack;&rbrack; DisableCors desativa](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) o CORS para o controlador/modelo de página/ação.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opções de política do CORS

Esta seção descreve as várias opções que podem ser definidas em uma política cors:

* [Defina as origens permitidas](#set-the-allowed-origins)
* [Defina os métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Defina os cabeçalhos de solicitação permitidos](#set-the-allowed-request-headers)
* [Defina os cabeçalhos de resposta expostos](#set-the-exposed-response-headers)
* [Credenciais em solicitações de origem cruzada](#credentials-in-cross-origin-requests)
* [Defina o tempo de expiração do pré-vôo](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado `Startup.ConfigureServices`dentro . Para algumas opções, pode ser útil ler a seção Como o [CORS funciona](#how-cors) primeiro.

## <a name="set-the-allowed-origins"></a>Defina as origens permitidas

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitações do CORS de`http` todas `https`as origens com qualquer esquema (ou ). `AllowAnyOrigin`é inseguro porque *qualquer site* pode fazer solicitações de origem cruzada para o aplicativo.

> [!NOTE]
> Especificar `AllowAnyOrigin` `AllowCredentials` e é uma configuração insegura e pode resultar em falsificação de solicitação entre sites. Para um aplicativo seguro, especifique uma lista exata de origens se o cliente deve se autorizar a acessar recursos do servidor.

`AllowAnyOrigin`afeta pedidos de pré-vôo e o `Access-Control-Allow-Origin` cabeçalho. Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Define <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> a propriedade da diretiva como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Defina os métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite qualquer método HTTP:
* Afeta as solicitações `Access-Control-Allow-Methods` de pré-vôo e o cabeçalho. Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Defina os cabeçalhos de solicitação permitidos

Para permitir que cabeçalhos específicos sejam enviados em uma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> solicitação cors, chamados *cabeçalhos de solicitação do autor,* ligue e especifique os cabeçalhos permitidos:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>cabeçalhos de solicitação do autor solicitem:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Esta configuração afeta as `Access-Control-Request-Headers` solicitações de pré-vôo e o cabeçalho. Para obter mais informações, consulte a seção [de solicitações de pré-vôo.](#preflight-requests)

O CORS Middleware sempre permite `Access-Control-Request-Headers` que quatro cabeçalhos no ser enviado, independentemente dos valores configurados em CorsPolicy.Headers. Esta lista de cabeçalhos inclui:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Por exemplo, considere um aplicativo configurado da seguinte forma:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

O CORS Middleware responde com sucesso a uma `Content-Language` solicitação de pré-vôo com o seguinte cabeçalho de solicitação porque está sempre listado em branco:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Defina os cabeçalhos de resposta expostos

Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo. Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (Terminologia): Simples Cabeçalho de Resposta](https://www.w3.org/TR/cors/#simple-response-header).

Os cabeçalhos de resposta disponíveis por padrão são:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

A especificação cors chama esses cabeçalhos *de cabeçalho simples cabeçalhos de resposta*. Para disponibilizar outros cabeçalhos <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>para o aplicativo, ligue para:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Credenciais em solicitações de origem cruzada

As credenciais requerem tratamento especial em uma solicitação do CORS. Por padrão, o navegador não envia credenciais com uma solicitação de origem cruzada. As credenciais incluem cookies e esquemas de autenticação HTTP. Para enviar credenciais com uma solicitação de `XMLHttpRequest.withCredentials` `true`origem cruzada, o cliente deve definir para .

Usando `XMLHttpRequest` diretamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Usando jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

O servidor deve permitir as credenciais. Para permitir credenciais de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origem cruzada, ligue para:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

A resposta HTTP `Access-Control-Allow-Credentials` inclui um cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação de origem cruzada.

Se o navegador enviar credenciais, mas `Access-Control-Allow-Credentials` a resposta não incluir um cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação de origem cruzada falhará.

Permitir credenciais de origem cruzada é um risco para a segurança. Um site em outro domínio pode enviar credenciais de um usuário de entrada para o aplicativo em nome do usuário sem o conhecimento do usuário. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

A especificação do CORS também `"*"` afirma que a configuração de `Access-Control-Allow-Credentials` origens para (todas as origens) é inválida se o cabeçalho estiver presente.

### <a name="preflight-requests"></a>Solicitações de pré-vôo

Para algumas solicitações do CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real. Este pedido é chamado de *solicitação de pré-vôo.* O navegador pode ignorar a solicitação de pré-vôo se as seguintes condições forem verdadeiras:

* O método de solicitação é GET, HEAD ou POST.
* O aplicativo não define cabeçalhos `Content-Language`de `Content-Type`solicitação `Last-Event-ID`que não senão, `Accept` `Accept-Language`ou .
* O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

A regra sobre cabeçalhos de solicitação definidos para a `setRequestHeader` solicitação `XMLHttpRequest` do cliente se aplica aos cabeçalhos que o aplicativo define chamando o objeto. A especificação cors chama esses cabeçalhos *autor solicitar cabeçalhos*. A regra não se aplica aos cabeçalhos `User-Agent` `Host`que `Content-Length`o navegador pode definir, tais como , ou .

A seguir, um exemplo de uma solicitação de pré-vôo:

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

A solicitação de pré-vôo usa o método HTTP OPTIONS. Inclui dois cabeçalhos especiais:

* `Access-Control-Request-Method`: O método HTTP que será utilizado para a solicitação real.
* `Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real. Como dito anteriormente, isso não inclui cabeçalhos `User-Agent`que o navegador define, como .

<!-- I think this needs to be removed, was put here accidently -->

Quando o CORS é habilitado com a política apropriada, ASP.NET Core geralmente responde automaticamente às solicitações de pré-vôo do CORS. Consulte [o atributo [HttpOptions] para solicitações de pré-vôo](#pro).

Uma solicitação de pré-vôo `Access-Control-Request-Headers` cors pode incluir um cabeçalho, que indica para o servidor os cabeçalhos que são enviados com a solicitação real.

Para permitir cabeçalhos específicos, ligue para: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir que todos os <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>cabeçalhos de solicitação do autor solicitem:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Os navegadores não são totalmente consistentes na forma como definem `Access-Control-Request-Headers`. Se você definir cabeçalhos para qualquer outra coisa `Accept` `Content-Type`que `Origin`não seja `"*"` (ou usar), <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>você deve incluir pelo menos , e , além de quaisquer cabeçalhos personalizados que você deseja suportar.

A seguir, uma resposta de exemplo à solicitação de pré-vôo (assumindo que o servidor permite a solicitação):

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

A resposta inclui `Access-Control-Allow-Methods` um cabeçalho que lista `Access-Control-Allow-Headers` os métodos permitidos e, opcionalmente, um cabeçalho, que lista os cabeçalhos permitidos. Se a solicitação de pré-vôo for bem sucedida, o navegador envia a solicitação real.

Se a solicitação de pré-vôo for negada, o aplicativo retorna uma resposta *de 200 OK,* mas não envia os cabeçalhos cors de volta. Portanto, o navegador não tenta a solicitação de origem cruzada.

### <a name="set-the-preflight-expiration-time"></a>Defina o tempo de expiração do pré-vôo

O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta à solicitação de pré-vôo pode ser armazenada em cache. Para definir este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>cabeçalho, ligue:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Como funciona o CORS

Esta seção descreve o que acontece em uma solicitação [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) no nível das mensagens HTTP.

* Cors **não** é um recurso de segurança. Cors é um padrão W3C que permite que um servidor relaxe a política de mesma origem.
  * Por exemplo, um ator mal-intencionado poderia usar [o Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contra seu site e executar uma solicitação entre sites para o seu site habilitado para cors para roubar informações.
* Sua API não é mais segura permitindo cors.
  * Cabe ao cliente (navegador) fazer valer o CORS. O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta. Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Um navegador da Web inserindo a URL na barra de endereços.
* É uma maneira de um servidor permitir que os navegadores executem um [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) de origem cruzada ou solicitação [de API de origem cruzada](https://developer.mozilla.org/docs/Web/API/Fetch_API) que de outra forma seria proibido.
  * Os navegadores (sem cors) não podem fazer solicitações de origem cruzada. Antes do CORS, [o JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) era usado para contornar essa restrição. JSONP não usa XHR, usa `<script>` a tag para receber a resposta. Os scripts podem ser carregados de origem cruzada.

A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem solicitações de origem cruzada. Se um navegador suportar o CORS, ele define esses cabeçalhos automaticamente para solicitações de origem cruzada. O código JavaScript personalizado não é necessário para ativar o CORS.

O seguinte é um exemplo de uma solicitação de origem cruzada. O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação. O `Origin` cabeçalho é necessário e deve ser diferente do host.

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

Se o servidor permitir a `Access-Control-Allow-Origin` solicitação, ele define o cabeçalho na resposta. O valor deste cabeçalho corresponde ao `Origin` cabeçalho `"*"`da solicitação ou é o valor curinga, o que significa que qualquer origem é permitida:

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

Se a resposta não `Access-Control-Allow-Origin` incluir o cabeçalho, a solicitação de origem cruzada falhará. Especificamente, o navegador não permite a solicitação. Mesmo que o servidor retorne uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.

<a name="test"></a>

## <a name="test-cors"></a>Testar o CORS

Para testar o CORS:

1. [Crie um projeto de API](xref:tutorials/first-web-api). Alternativamente, você pode [baixar a amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Habilite o CORS usando uma das abordagens deste documento. Por exemplo:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`só deve ser usado para testar um aplicativo de amostra semelhante ao [código de amostra de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Crie um projeto de aplicativo web (Razor Pages ou MVC). A amostra usa páginas de barbear. Você pode criar o aplicativo web na mesma solução que o projeto de API.
1. Adicione o seguinte código destacado ao arquivo *Index.cshtml:*

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. No código anterior, `url: 'https://<web app>.azurewebsites.net/api/values/1',` substitua-o pela URL para o aplicativo implantado.
1. Implantar o projeto de API. Por exemplo, [implantar no Azure](xref:host-and-deploy/azure-apps/index).
1. Execute o aplicativo Razor Pages ou MVC na área de trabalho e clique no botão **Teste.** Use as ferramentas F12 para revisar mensagens de erro.
1. Remova a origem `WithOrigins` do host local e implante o aplicativo. Alternativamente, execute o aplicativo cliente com uma porta diferente. Por exemplo, executado a partir de Visual Studio.
1. Teste com o aplicativo do cliente. As falhas do CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript. Use a guia do console nas ferramentas f12 para ver o erro. Dependendo do navegador, você tem um erro (no console de ferramentas f12) semelhante ao seguinte:

   * Usando o Microsoft Edge:

     **SEC7120: [CORS] `https://localhost:44375` A origem `https://localhost:44375` não foi encontrado no cabeçalho de resposta Access-Control-Allow-Origin para recurso de origem cruzada em`https://webapi.azurewebsites.net/api/values/1`**

   * Usando o Chrome:

     **O acesso ao XMLHttpRequest na `https://webapi.azurewebsites.net/api/values/1` origem `https://localhost:44375` foi bloqueado pela diretiva CORS: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.**
     
Os pontos finais habilitados para CORS podem ser testados com uma ferramenta, como [O Violinista](https://www.telerik.com/fiddler) ou [o Carteiro](https://www.getpostman.com/). Ao usar uma ferramenta, a origem da `Origin` solicitação especificada pelo cabeçalho deve diferir do host que recebe a solicitação. Se a solicitação não *for de origem* cruzada `Origin` com base no valor do cabeçalho:

* Não há necessidade de o CORS Middleware processar a solicitação.
* Os cabeçalhos cors não são devolvidos na resposta.

## <a name="cors-in-iis"></a>CORS no IIS

Ao implantar no IIS, o CORS deve ser executado antes da Autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo. Para suportar esse cenário, o [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.

## <a name="additional-resources"></a>Recursos adicionais

* [CORS (Compartilhamento de Recursos entre Origens)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Começando com o módulo IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
