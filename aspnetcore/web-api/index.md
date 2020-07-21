---
title: Criar APIs Web com o ASP.NET Core
author: scottaddie
description: Aprenda os fundamentos da criação de uma API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/20/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/index
ms.openlocfilehash: 98fb8c0a26f5f8e7ce5f07066f2f36e748ab2398
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568737"
---
# <a name="create-web-apis-with-aspnet-core"></a>Criar APIs Web com o ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie) e [Tom Dykstra](https://github.com/tdykstra)

O ASP.NET Core permite a criação de serviços RESTful, também conhecidos como APIs Web, usando C#. Para lidar com solicitações, uma API Web usa controladores. Em uma API Web, os *controladores* são classes que derivam de `ControllerBase`. Este artigo mostra como usar controladores para manipular solicitações de API da Web.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([Como baixar](xref:index#how-to-download-a-sample).)

## <a name="controllerbase-class"></a>Classe ControllerBase

Uma API da Web consiste em uma ou mais classes de controlador que derivam de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . O modelo de projeto de API Web fornece um controlador de início:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Não crie um controlador de API Web derivando da classe base <xref:Microsoft.AspNetCore.Mvc.Controller>. `Controller` é derivado de `ControllerBase` e agrega suporte para exibições; portanto, serve para manipulação de páginas da Web, não para solicitações de API Web. Há uma exceção a essa regra: se você planeja usar o mesmo controlador para exibições e APIs da Web, derive-o de `Controller` .

A classe `ControllerBase` fornece muitas propriedades e métodos úteis para lidar com solicitações HTTP. Por exemplo, `ControllerBase.CreatedAtAction` retorna um código de status 201:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Veja mais alguns exemplos de métodos fornecidos por `ControllerBase`.

|Método   |Observações    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Retorna o código de status 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Retorna o código de status 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Retorna um arquivo.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Invoca [model binding](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Invoca [validação de modelo](xref:mvc/models/validation).|

Confira uma lista com todos os métodos e propriedades disponíveis em <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Atributos

O namespace <xref:Microsoft.AspNetCore.Mvc> fornece atributos que podem ser usados para configurar o comportamento de controladores de API Web e dos métodos de ação. O exemplo a seguir usa atributos para especificar o verbo de ação HTTP com suporte e quaisquer códigos de status HTTP conhecidos que poderiam ser retornados:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Confira mais alguns exemplos de atributos disponíveis.

|Atributo|Observações|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Especifica o padrão de URL para um controlador ou ação.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Especifica o prefixo e as propriedades que serão incluídos no model binding.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifica uma ação que dá suporte ao verbo de ação HTTP GET.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Especifica os tipos de dados aceitos por uma ação.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Especifica os tipos de dados retornados por uma ação.|

Veja uma lista que inclui os atributos disponíveis no namespace <xref:Microsoft.AspNetCore.Mvc>.

## <a name="apicontroller-attribute"></a>Atributo ApiController

O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo pode ser aplicado a uma classe de controlador para habilitar os seguintes conceituada, comportamentos específicos de API:

::: moniker range=">= aspnetcore-2.2"

* [Requisito de roteamento de atributo](#attribute-routing-requirement)
* [Respostas HTTP 400 automáticas](#automatic-http-400-responses)
* [Inferência de parâmetro de origem da associação](#binding-source-parameter-inference)
* [Inferência de solicitação de várias partes/dados de formulário](#multipartform-data-request-inference)
* [Detalhes do problema dos códigos de status de erro](#problem-details-for-error-status-codes)

O recurso *detalhes do problema para códigos de status de erro* requer uma versão de [compatibilidade](xref:mvc/compatibility-version) do 2,2 ou posterior. Os outros recursos exigem uma versão de compatibilidade do 2,1 ou posterior.

::: moniker-end

* [Requisito de roteamento de atributo](#attribute-routing-requirement)
* [Respostas HTTP 400 automáticas](#automatic-http-400-responses)
* [Inferência de parâmetro de origem da associação](#binding-source-parameter-inference)
* [Inferência de solicitação de várias partes/dados de formulário](#multipartform-data-request-inference)

Esses recursos exigem [compatibilidade com a versão](xref:mvc/compatibility-version) 2.1 ou posterior.

### <a name="attribute-on-specific-controllers"></a>Atributo em controladores específicos

O atributo `[ApiController]` pode ser aplicado a controladores específicos, como no exemplo a seguir do modelo de projeto:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Atributo em vários controladores

Uma abordagem ao uso do atributo em mais de um controlador é a criação de uma classe de controlador base personalizada anotada com o atributo `[ApiController]`. O exemplo a seguir mostra uma classe base personalizada e um controlador que deriva dele:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Atributo em um assembly

Se [versão de compatibilidade](xref:mvc/compatibility-version) estiver definida como 2.2 ou posterior, o atributo `[ApiController]` poderá ser aplicado a um assembly. A anotação dessa maneira aplica o comportamento da API Web para todos os controladores no assembly. Não é possível recusar controladores individuais. Aplique o atributo de nível de assembly à declaração de namespace em torno da `Startup` classe:

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Requisito de roteamento de atributo

O atributo `[ApiController]` transforma em requisito o roteamento de atributo. Por exemplo:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas pelo `UseEndpoints` , <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> no `Startup.Configure` .

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> em `Startup.Configure`.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Respostas automáticas do HTTP 400

O atributo `[ApiController]` faz com que os erros de validação do modelo disparem automaticamente uma resposta HTTP 400. Consequentemente, o código a seguir se torna desnecessário em um método de ação:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC usa o <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> filtro de ação para fazer a verificação anterior.

### <a name="default-badrequest-response"></a>Resposta BadRequest padrão

Com uma versão de compatibilidade de 2,1, o tipo de resposta padrão para uma resposta HTTP 400 é <xref:Microsoft.AspNetCore.Mvc.SerializableError> . O corpo da solicitação a seguir é um exemplo do tipo serializado:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

Com uma versão de compatibilidade do 2,2 ou posterior, o tipo de resposta padrão para uma resposta HTTP 400 é <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . O corpo da solicitação a seguir é um exemplo do tipo serializado:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

O `ValidationProblemDetails` tipo:

* Fornece um formato legível por máquina para especificar erros nas respostas da API Web.
* Está em conformidade com a [especificação RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

Para tornar as respostas automáticas e personalizadas consistentes, chame o <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> método em vez de <xref:System.Web.Http.ApiController.BadRequest%2A> . `ValidationProblem`Retorna um <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> objeto, bem como a resposta automática.

### <a name="log-automatic-400-responses"></a>Registrar respostas de 400 automática

Consulte [como registrar em log as respostas automáticas 400 em erros de validação de modelo (dotNet/AspNetCore.Docs # 12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Desabilitar resposta automática 400

Para desabilitar o comportamento 400 automático, defina a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> como `true`. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Inferência de parâmetro de origem de associação

Um atributo de origem de associação define o local no qual o valor do parâmetro de uma ação é encontrado. Os seguintes atributos da origem da associação existem:

|Atributo|Fonte de associação |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Corpo da solicitação |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Dados do formulário no corpo da solicitação |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Cabeçalho da solicitação |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Parâmetro de cadeia de caracteres de consulta de solicitação |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Dados de rota da solicitação atual |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | O serviço de solicitação inserido como um parâmetro de ação |

> [!WARNING]
> Não use `[FromRoute]` quando os valores puderem conter `%2f` (ou seja, `/`). `%2f` não ficará sem escape para `/`. Use `[FromQuery]`, se o valor puder conter `%2f`.

Sem o atributo `[ApiController]` ou outros atributos de origem da associação, como `[FromQuery]`, o runtime do ASP.NET Core tenta usar o associador de modelos de objeto complexo. O associador de modelos de objeto complexo extrai os dados dos provedores de valor em uma ordem definida.

No exemplo a seguir, o atributo `[FromQuery]` indica que o valor do parâmetro `discontinuedOnly` é fornecido na cadeia de caracteres de consulta da URL de solicitação:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

O atributo `[ApiController]` aplica regras de inferência para as fontes de dados padrão dos parâmetros de ação. Essas regras poupam você da necessidade de identificar as origens de associação manualmente aplicando atributos aos parâmetros de ação. As regras de inferência da origem de associação se comportam da seguinte maneira:

* `[FromBody]` é inferido para parâmetros de tipo complexo. Uma exceção à regra de inferência `[FromBody]` é qualquer tipo interno complexo com um significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> e <xref:System.Threading.CancellationToken>. O código de inferência da origem da associação ignora esses tipos especiais.
* `[FromForm]` é inferido para parâmetros de ação do tipo <xref:Microsoft.AspNetCore.Http.IFormFile> e <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. Ele não é inferido para qualquer tipo simples ou definido pelo usuário.
* `[FromRoute]` é inferido para qualquer nome de parâmetro de ação correspondente a um parâmetro no modelo de rota. Quando mais de uma rota correspondem a um parâmetro de ação, qualquer valor de rota é considerado `[FromRoute]`.
* `[FromQuery]` é inferido para todos os outros parâmetros de ação.

### <a name="frombody-inference-notes"></a>Notas de inferência FromBody

`[FromBody]` não é inferido para tipos simples, como `string` ou `int`. Portanto, o atributo `[FromBody]` deve ser usado para tipos simples quando essa funcionalidade for necessária.

Quando uma ação tiver mais de um parâmetro associado ao corpo da solicitação, uma exceção será lançada. Por exemplo, todas as assinaturas de método de ação a seguir causam uma exceção:

* `[FromBody]` inferido em ambos, pois são tipos complexos.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* O atributo `[FromBody]` em um, inferido no outro, porque é um tipo complexo.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* Atributo `[FromBody]` em ambos.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> No ASP.NET Core 2.1, os parâmetros de tipo de coleção, como listas e matrizes, são inferidos incorretamente como `[FromQuery]`. O atributo `[FromBody]` deve ser usado para esses parâmetros se eles forem vinculados ao corpo da solicitação. Esse comportamento é corrigido no ASP.NET Core 2.2 ou posterior, onde os parâmetros do tipo de coleção são inferidos para serem associados ao corpo por padrão.

::: moniker-end

### <a name="disable-inference-rules"></a>Desabilitar regras de inferência

Para desabilitar a inferência da origem da associação, defina <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> como `true`. Adicione o seguinte código em `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Inferência de solicitação de várias partes/dados de formulário

O `[ApiController]` atributo aplica uma regra de inferência quando um parâmetro de ação é anotado com o [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) atributo. O `multipart/form-data` tipo de conteúdo da solicitação é inferido.

Para desabilitar o comportamento padrão, defina a <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> propriedade como `true` em `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Detalhes do problema dos códigos de status de erro

Quando a versão de compatibilidade for 2.2 ou posterior, o MVC transformará um resultado de erro (um resultado com o código de status 400 ou superior) em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. O tipo `ProblemDetails` tem base na [especificação RFC 7807](https://tools.ietf.org/html/rfc7807) para fornecer detalhes de erro legíveis por computador em uma resposta HTTP.

Considere o seguinte código em uma ação do controlador:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

O `NotFound` método produz um código de status HTTP 404 com um `ProblemDetails` corpo. Por exemplo:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Desabilitar a resposta de ProblemDetails

A criação automática de `ProblemDetails` códigos de status de erro é desabilitada quando a <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> propriedade é definida como `true` . Adicione o seguinte código em `Startup.ConfigureServices`:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Definir tipos de conteúdo de solicitação com suporte com o atributo [Consumes]

Por padrão, uma ação dá suporte a todos os tipos de conteúdo de solicitação disponíveis. Por exemplo, se um aplicativo estiver configurado para dar suporte a [formatadores de entrada](xref:mvc/models/model-binding#input-formatters)JSON e XML, uma ação dará suporte a vários tipos de conteúdo, incluindo `application/json` e `application/xml` .

O atributo [[consume]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que uma ação limite os tipos de conteúdo de solicitação com suporte. Aplique o `[Consumes]` atributo a uma ação ou controlador, especificando um ou mais tipos de conteúdo:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

No código anterior, a `CreateProduct` ação especifica o tipo de conteúdo `application/xml` . As solicitações roteadas para esta ação devem especificar um `Content-Type` cabeçalho de `application/xml` . Solicitações que não especificam um `Content-Type` cabeçalho de `application/xml` resultado em uma resposta de [tipo de mídia sem suporte 415](https://developer.mozilla.org/docs/Web/HTTP/Status/415) .

O `[Consumes]` atributo também permite que uma ação influencie sua seleção com base no tipo de conteúdo de uma solicitação de entrada aplicando uma restrição de tipo. Considere o exemplo a seguir:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

No código anterior, `ConsumesController` é configurado para tratar as solicitações enviadas para a `https://localhost:5001/api/Consumes` URL. Ambas as ações do controlador `PostJson` e `PostForm` processam solicitações post com a mesma URL. Sem o `[Consumes]` atributo aplicar uma restrição de tipo, uma exceção de correspondência ambígua é gerada.

O `[Consumes]` atributo é aplicado a ambas as ações. A `PostJson` ação manipula solicitações enviadas com um `Content-Type` cabeçalho de `application/json` . A `PostForm` ação manipula solicitações enviadas com um `Content-Type` cabeçalho de `application/x-www-form-urlencoded` . 

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
