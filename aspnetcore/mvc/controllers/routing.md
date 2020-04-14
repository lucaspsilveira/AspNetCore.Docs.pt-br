---
title: Roteamento para ações do controlador no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core MVC usa o middleware de roteamento para corresponder a URLs das solicitações de entrada e mapeá-las para ações.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277126"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Roteamento para ações do controlador no ASP.NET Core

Por [Ryan Nowak,](https://github.com/rynowak) [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET controladores Core usam o [middleware](xref:fundamentals/middleware/index) Routing para corresponder aos URLs das solicitações recebidas e mapeá-las para [ações](#action).  Modelos de rotas:

* São definidos em código de inicialização ou atributos.
* Descreva como os caminhos de URL são combinados com [as ações](#action).
* São usados para gerar URLs para links. Os links gerados são normalmente retornados em respostas.

As ações são [convencionalmente roteadas](#cr) ou [roteadas por atributos](#ar). Colocar uma rota no controlador ou [ação](#action) torna-a roteada por atributos. Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).

Este documento:

* Explica as interações entre MVC e roteamento:
  * Como aplicativos MVC típicos fazem uso de recursos de roteamento.
  * Cobre ambos:
    * [Roteamento convencionalnormalmente](#cr) usado com controladores e visualizações.
    * *Roteamento de atributos* usado com APIs REST. Se você estiver interessado principalmente em roteamento para APIs REST, pule para o [roteamento de atributo satisfaz a](#ar) seção REST APIs.
  * Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes avançados de roteamento.
* Refere-se ao sistema de roteamento padrão adicionado em ASP.NET Core 3.0, chamado de roteamento de ponto final. É possível usar controladores com a versão anterior de roteamento para fins de compatibilidade. Consulte o [guia de migração 2.2-3.0](xref:migration/22-to-30) para obter instruções. Consulte a [versão 2.2 deste documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para obter material de referência no sistema de roteamento legado.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Configurar a rota convencional

`Startup.Configure`tipicamente tem código semelhante ao seguinte ao usar [roteamento convencional:](#crd)

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Dentro da <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>chamada <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> para , é usado para criar uma única rota. A rota única `default` é nomeada rota. A maioria dos aplicativos com controladores e `default` visualizações usa um modelo de rota semelhante à rota. As APIs REST devem usar [roteamento de atributos](#ar).

O modelo `"{controller=Home}/{action=Index}/{id?}"`de rota:

* Corresponde a um caminho de URL como`/Products/Details/5`
* Extrai os `{ controller = Products, action = Details, id = 5 }` valores da rota tokenizando o caminho. A extração de valores de rota resulta `ProductsController` em `Details` uma correspondência se o aplicativo tiver um controlador nomeado e uma ação:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`modelo liga o `id = 5` valor de `id` definir `5`o parâmetro para . Consulte [Model Binding](xref:mvc/models/model-binding) para obter mais detalhes.
* `{controller=Home}`define `Home` como padrão `controller`.
* `{action=Index}`define `Index` como padrão `action`.
*  O `?` personagem `{id?}` define `id` como opcional.
  * Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência. Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.
* Corresponde ao `/`caminho da URL .
* Produz os valores `{ controller = Home, action = Index }`da rota.

Os valores para `controller` e `action` fazer uso dos valores padrão. `id`não produz um valor, uma vez que não há segmento correspondente no caminho da URL. `/`só corresponde se `HomeController` existir `Index` a e ação:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Usando a definição do controlador `HomeController.Index` anterior e o modelo de rota, a ação é executada para os seguintes caminhos de URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

O caminho `/` url usa `Home` os controladores padrão e `Index` a ação do modelo de rota. O caminho `/Home` url usa `Index` a ação padrão do modelo de rota.

O método de conveniência <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Substitui:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> O roteamento é <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> configurado usando o middleware e o middleware. Para usar controladores:
>
> * Ligue <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` para dentro para mapear [controladores roteados de atributos.](#ar)
> * Ligue <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>ou, para mapear controladores [roteados convencionalmente.](#cr)

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Roteamento convencional

O roteamento convencional é usado com controladores e visualizações. A rota `default`:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

é um exemplo de *roteamento convencional*. É chamado de *roteamento convencional* porque estabelece uma *convenção* para caminhos de URL:

* O primeiro segmento `{controller=Home}`de caminho, mapeia o nome do controlador.
* O segundo `{action=Index}`segmento, mapas para o nome da [ação.](#action)
* O terceiro `{id?}` segmento é usado `id`para um opcional . A `?` `{id?}` in torna opcional. `id`é usado para mapear para uma entidade modelo.

Usando `default` essa rota, o caminho da URL:

* `/Products/List`mapas para `ProductsController.List` a ação.
* `/Blog/Article/17`mapas `BlogController.Article` para e tipicamente `id` modelo liga o parâmetro a 17.

Este mapeamento:

* É baseado apenas no controlador e **nomes** [de ação](#action) .
* Não é baseado em namespaces, locais de arquivo de origem ou parâmetros de método.

O uso do roteamento convencional com a rota padrão permite criar o aplicativo sem ter que criar um novo padrão de URL para cada ação. Para um aplicativo com ações de estilo [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) ter consistência para os URLs entre os controladores:

* Ajuda a simplificar o código.
* Torna a ui mais previsível.

> [!WARNING]
> O `id` código anterior é definido como opcional pelo modelo de rota. As ações podem ser executadas sem o ID opcional fornecido como parte da URL. Geralmente, quando`id` é omitido da URL:
>
> * `id`é definido `0` por vinculação modelo.
> * Nenhuma entidade é encontrada `id == 0`na correspondência do banco de dados .
>
> [O roteamento de atributos](#ar) fornece controle fino para tornar o ID necessário para algumas ações e não para outras. Por convenção, a documentação `id` inclui parâmetros opcionais, como quando é provável que apareçam no uso correto.

A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas. A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:

* Dá suporte a um esquema de roteamento básico e descritivo.
* É um ponto de partida útil para aplicativos baseados em interface do usuário.
* É o único modelo de rota necessário para muitos aplicativos de interface do web. Para aplicativos maiores de interface do web, outra rota usando [Áreas](#areas) se freqüentemente tudo o que é necessário.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>e: <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>

* Atribua automaticamente um valor **de pedido** aos seus pontos finais com base na ordem em que são invocados.

Roteamento de ponto final no ASP.NET Núcleo 3.0 e posteriores:

* Não tem um conceito de rotas.
* Não fornece garantias de pedidos para a execução da extensibilidade, todos os pontos finais são processados de uma só vez.

Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.

[O roteamento de atributos](#ar) é explicado mais tarde neste documento.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Múltiplas rotas convencionais

Várias [rotas convencionais](#cr) podem ser adicionadas dentro `UseEndpoints` adicionando mais chamadas e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>. Isso permite definir várias convenções ou adicionar rotas convencionais dedicadas a uma [ação](#action)específica, tais como:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

A `blog` rota no código anterior é uma **rota convencional dedicada.** É chamada de rota convencional dedicada porque:

* Ele usa [roteamento convencional.](#cr)
* É dedicado a uma [ação](#action)específica.

Porque `controller` `action` e não aparecem no `"blog/{*article}"` modelo de rota como parâmetros:

* Eles só podem ter `{ controller = "Blog", action = "Article" }`os valores padrão.
* Esta rota sempre mapeia para a ação. `BlogController.Article`

`/Blog`, `/Blog/Article`e `/Blog/{any-string}` são os únicos caminhos de URL que correspondem à rota do blog.

O exemplo anterior:

* `blog`rota tem uma prioridade maior `default` para partidas do que a rota porque é adicionada primeiro.
* É e exemplo de roteamento estilo [Slug](https://developer.mozilla.org/docs/Glossary/Slug) onde é típico ter um nome de artigo como parte da URL.

> [!WARNING]
> Em ASP.NET Núcleo 3.0 e posterior, o roteamento não:
> * Defina um conceito chamado *rota.* `UseRouting`adiciona correspondência de rota ao pipeline de middleware. O `UseRouting` middleware analisa o conjunto de pontos finais definidos no aplicativo e seleciona a melhor correspondência de ponto final com base na solicitação.
> * Fornecer garantias sobre a ordem de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>execução de extensibilidade como ou .
>
>Consulte [Roteamento](xref:fundamentals/routing) para obter material de referência no roteamento.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Ordem de roteamento convencional

O roteamento convencional corresponde apenas a uma combinação de ação e controlador que são definidas pelo aplicativo. O objetivo é simplificar os casos em que as rotas convencionais se sobrepõem.
Adicionando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>rotas <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>usando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , e atribua automaticamente um valor de pedido aos seus pontos finais com base na ordem em que são invocados. As correspondências de uma rota que aparece anteriormente têm uma prioridade maior. O roteamento convencional é dependente da ordem. Em geral, rotas com áreas devem ser colocadas mais cedo, pois são mais específicas do que rotas sem área. [Rotas convencionais dedicadas](#dcr) com `{*article}` captura de todos os parâmetros de rota, como podem fazer uma rota muito [gananciosa,](xref:fundamentals/routing#greedy)o que significa que combina com URLs que você pretendia combinar com outras rotas. Coloque as rotas gananciosas mais tarde na tabela de rotas para evitar partidas gananciosas.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Resolução de ações ambíguas

Quando dois pontos finais correspondem através do roteamento, o roteamento deve fazer um dos seguintes:

* Escolha o melhor candidato.
* Gera uma exceção.

Por exemplo:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

O controlador anterior define duas ações que correspondem:

* O caminho da URL`/Products33/Edit/17`
* Dados `{ controller = Products33, action = Edit, id = 17 }`de rota .

Este é um padrão típico para controladores MVC:

* `Edit(int)`exibe um formulário para editar um produto.
* `Edit(int, Product)`processa o formulário postado.

Para resolver a rota correta:

* `Edit(int, Product)`é selecionado quando a `POST`solicitação é um HTTP .
* `Edit(int)`é selecionado quando o [verbo HTTP é](#verb) qualquer outra coisa. `Edit(int)`é geralmente chamado `GET`via .

O <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, , é fornecido para roteamento para que ele possa escolher com base no método HTTP da solicitação. O `HttpPostAttribute` `Edit(int, Product)` faz um `Edit(int)`jogo melhor do que .

É importante entender o papel de `HttpPostAttribute`atributos como . Atributos semelhantes são definidos para outros [verbos HTTP](#verb). No [roteamento convencional,](#cr)é comum que as ações usem o mesmo nome de ação quando fazem parte de um formulário show, enviem o fluxo de trabalho do formulário. Por exemplo, consulte [Examine os dois métodos de ação Editar](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Se o roteamento não puder <xref:System.Reflection.AmbiguousMatchException> escolher o melhor candidato, um é lançado, listando os vários pontos finais combinados.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Nomes de rotas convencionais

As `"blog"` strings `"default"` e nos seguintes exemplos são nomes de rota convencionais:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Os nomes da rota dão à rota um nome lógico. A rota nomeada pode ser usada para geração de URL. O uso de uma rota nomeada simplifica a criação de URL quando o pedido de rotas pode complicar a geração de URL. Os nomes das rotas devem ser exclusivos de aplicação.

Nomes da rota:

* Não tenha nenhum impacto na correspondência de URL ou no manuseio de solicitações.
* São usados apenas para geração de URL.

O conceito de nome de rota é representado no roteamento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Os termos **nome da rota** e nome do ponto **final:**

* São intercambiáveis.
* Qual deles é usado na documentação e código depende da API ser descrita.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Roteamento de atributos para APIs REST

As APIs REST devem usar roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos onde as operações são representadas por [verbos HTTP](#verb).

O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota. O `StartUp.Configure` código a seguir é típico de uma API REST e é usado na próxima amostra:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> é `UseEndpoints` chamado dentro para mapear controladores roteados de atributos.

No exemplo a seguir:

* O `Configure` método anterior é usado.
* `HomeController`corresponde a um conjunto de URLs `{controller=Home}/{action=Index}/{id?}` semelhante ao que a rota convencional padrão corresponde.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

A `HomeController.Index` ação é executada para `/`qualquer `/Home` `/Home/Index`um `/Home/Index/3`dos caminhos de URL, ou .

Este exemplo destaca uma diferença de programação chave entre roteamento de atributos e [roteamento convencional](#cr). O roteamento de atributos requer mais entrada para especificar uma rota. A rota padrão convencional lida com rotas de forma mais sucinta. No entanto, o roteamento de atributos permite e requer um controle preciso de quais modelos de rota se aplicam a cada [ação](#action).

Com o roteamento de atributos, o nome do controlador e os nomes de ação não desempenham **nenhum** papel no qual a ação é combinada. O exemplo a seguir corresponde aos mesmos URLs do exemplo anterior:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

O código a seguir `action` usa `controller`substituição de token para e:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

O código a `[Route("[controller]/[action]")]` seguir se aplica ao controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

No código anterior, `Index` os modelos `/` de `~/` método devem ser preparados para os modelos de rota. Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.

Consulte [a precedência do modelo de rota](xref:fundamentals/routing#rtp) para obter informações sobre a seleção do modelo de rota.

## <a name="reserved-routing-names"></a>Nomes reservados de roteamento

As seguintes palavras-chave são nomes de parâmetros de rota reservados ao usar Controladores ou Páginas de Navalha:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Usar `page` como parâmetro de rota com roteamento de atributos é um erro comum. Fazer isso resulta em um comportamento inconsistente e confuso com a geração de URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Os nomes de parâmetros especiais são usados pela geração de URL para determinar se uma operação de geração de URL se refere a uma página de navalha ou a um controlador.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Modelos de verbos HTTP

ASP.NET Core tem os seguintes modelos de verbos HTTP:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[Httphead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Modelos de rota

ASP.NET Core tem os seguintes modelos de rota:

* Todos os [modelos de verbo HTTP](#verb) são modelos de rota.
* [[Rota]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Roteamento de atributos com atributos verbo http

Considere o seguinte controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

No código anterior:

* Cada ação `[HttpGet]` contém o atributo, que restringe a correspondência apenas às solicitações HTTP GET.
* A `GetProduct` ação inclui `"{id}"` o modelo, portanto, `id` `"api/[controller]"` é anexado ao modelo no controlador. O modelo de `"api/[controller]/"{id}""`métodos é . Portanto, esta ação corresponde apenas aos `/api/test2/xyz``/api/test2/123`pedidos`/api/test2/{any string}`get para o formulário, , etc.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* A `GetIntProduct` ação `"int/{id:int}")` contém o modelo. A `:int` parte do modelo `id` limita os valores de rota a strings que podem ser convertidas em um inteiro. Uma solicitação `/api/test2/int/abc`GET para:
  * Não combina com essa ação.
  * Retorna um erro [404 Não Encontrado.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* A `GetInt2Product` ação `{id}` contém no modelo, mas `id` não se restringe a valores que podem ser convertidos em um inteiro. Uma solicitação `/api/test2/int2/abc`GET para:
  * Combina com essa rota.
  * A vinculação `abc` do modelo não se converte em um inteiro. O `id` parâmetro do método é inteiro.
  * Retorna uma [solicitação ruim de 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque a vinculação do modelo não foi convertida`abc` em um inteiro.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

O roteamento <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> de atributos pode usar atributos como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>e <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>. Todos os atributos [do verbo HTTP](#verb) aceitam um modelo de rota. O exemplo a seguir mostra duas ações que correspondem ao mesmo modelo de rota:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Usando o `/products3`caminho url:

* A `MyProductsController.ListProducts` ação é executada quando `GET`o [verbo HTTP](#verb) é .
* A `MyProductsController.CreateProduct` ação é executada quando `POST`o [verbo HTTP](#verb) é .

Ao construir uma API REST, é raro que `[Route(...)]` você precise usar em um método de ação porque a ação aceita todos os métodos HTTP. É melhor usar o [atributo http verbo](#verb) mais específico para ser preciso sobre o que sua API suporta. Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.

As APIs REST devem usar roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos onde as operações são representadas por verbos HTTP. Isso significa que muitas operações, por exemplo, GET e POST no mesmo recurso lógico usam a mesma URL. O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.

Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota. No exemplo a `id` seguir, é necessário como parte do caminho da URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

A `Products2ApiController.GetProduct(int)` ação:

* É executado com caminho de URL como`/products2/3`
* Não é executado com `/products2`o caminho da URL.

O atributo [[Consums]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite uma ação para limitar os tipos de conteúdo de solicitação suportados. Para obter mais informações, consulte Definir tipos de [conteúdo de solicitação suportados com o atributo Consums](xref:web-api/index#consumes).

 Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.

Para obter `[ApiController]`mais informações sobre , consulte [atributo ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nome da rota

O código a seguir define um nome da rota como `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica. Nomes da rota:

* Não tenha nenhum impacto no comportamento correspondente de URL do roteamento.
* São usados apenas para geração de URL.

Nomes de rotas devem ser exclusivos no nível do aplicativo.

Contraste o código anterior com a rota `id` padrão convencional,`{id?}`que define o parâmetro como opcional ( ). A capacidade de especificar precisamente APIs `/products` `/products/5` tem vantagens, como permitir e ser despachada para diferentes ações.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Combinando rotas de atributos

Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais. Modelos de rota definidos no controlador precedem modelos de rota nas ações.  Colocar um atributo de rota no controlador foz com que **todas** as ações no controlador usem o roteamento de atributo.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

No exemplo anterior:

* O caminho `/products` da URL pode corresponder`ProductsApi.ListProducts`
* O caminho `/products/5` da `ProductsApi.GetProduct(int)`URL pode coincidir .

Ambas as ações só `GET` correspondem a HTTP `[HttpGet]` porque estão marcadas com o atributo.

Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador. O exemplo a seguir corresponde a um conjunto de caminhos de URL semelhantes à rota padrão.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

A tabela a `[Route]` seguir explica os atributos no código anterior:

| Atributo               | Combina com`[Route("Home")]` | Define modelo de rota |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Sim | `"Home"` |
| `[Route("Index")]` | Sim | `"Home/Index"` |
| `[Route("/")]` | **Não** | `""` |
| `[Route("About")]` | Sim | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Ordem da rota de atributo

O roteamento constrói uma árvore e corresponde a todos os pontos finais simultaneamente:

* As entradas de rota se comportam como se fossem colocadas em um pedido ideal.
* As rotas mais específicas têm a chance de executar antes das rotas mais gerais.

Por exemplo, uma `blog/search/{topic}` rota de atributo como `blog/{*article}`é mais específica do que uma rota de atributo como . A `blog/search/{topic}` rota tem maior prioridade, por padrão, porque é mais específica. Usando [o roteamento convencional,](#cr)o desenvolvedor é responsável por colocar rotas na ordem desejada.

As rotas de atributos <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> podem configurar um pedido usando a propriedade. Todos os [atributos](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) de `Order` rota fornecidos pela estrutura incluem . As rotas são processadas segundo uma classificação crescente da propriedade `Order`. A ordem padrão é `0`. Definir uma `Order = -1` rota usando corridas antes de rotas que não definem um pedido. Definir uma `Order = 1` rota usando executa após o pedido de rota padrão.

**Evite** depender `Order`de . Se o espaço de URL de um aplicativo requer valores de pedidos explícitos para rodar corretamente, então é provável que seja confuso para os clientes também. Em geral, o roteamento de atributos seleciona a rota correta com a correspondência de URL. Se o pedido padrão usado para geração de URL não estiver funcionando, usar um `Order` nome de rota como uma substituição é geralmente mais simples do que aplicar a propriedade.

Considere os dois controladores a seguir `/home`que ambos definem a correspondência de rota:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Solicitar `/home` com o código anterior lança uma exceção semelhante à seguinte:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Adicionar `Order` a um dos atributos da rota resolve a ambiguidade:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Com o código `/home` anterior, executa o `HomeController.Index` ponto final. Para chegar `MyDemoController.MyIndex`ao `/home/MyIndex`, solicitação . **Observação**:

* O código anterior é um exemplo ou um design de roteamento ruim. Foi usado para `Order` ilustrar a propriedade.
* A `Order` propriedade só resolve a ambiguidade, esse modelo não pode ser correspondido. Seria melhor remover o `[Route("Home")]` modelo.

Consulte [a rota de páginas de barbear e convenções de aplicativos: Ordem de rota](xref:razor-pages/razor-pages-conventions#route-order) para obter informações sobre o pedido de rota com páginas de barbear.

Em alguns casos, um erro HTTP 500 é retornado com rotas ambíguas. Use [o registro](xref:fundamentals/logging/index) para ver `AmbiguousMatchException`quais pontos finais causaram o .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Substituição de token em modelos de rota [controlador], [ação], [área]

Para conveniência, as rotas de atributo susem a substituição de tokens para parâmetros de rota reservados, anexando um token em um dos seguintes:

* Aparelhos quadrados:`[]`
* Aparelhos encaracolados:`{}`

Os tokens `[area]`, `[controller]` e são substituídos `[action]`pelos valores do nome da ação, nome da área e nome do controlador da ação onde a rota é definida:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

No código anterior:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Corresponde`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Corresponde`/Products0/Edit/{id}`

A substituição de token ocorre como a última etapa da criação das rotas de atributo. O exemplo anterior se comporta da mesma forma que o seguinte código:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Rotas de atributo também podem ser combinadas com herança. Isso é poderoso combinado com a substituição de tokens. A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`gera um nome de rota único para cada ação:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 gera um nome de rota exclusivo para cada ação.

Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar um transformador de parâmetro para personalizar a substituição de token

A substituição do token pode ser personalizada usando um transformador de parâmetro. Um transformador de parâmetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e transforma o valor dos parâmetros. Por exemplo, `SlugifyParameterTransformer` um transformador de `SubscriptionManagement` parâmetro `subscription-management`personalizado altera o valor da rota para:

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

O <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> é uma convenção de modelo de aplicativo que:

* Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.
* Personaliza os valores de token de rota de atributo conforme eles são substituídos.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

O `ListAll` método `/subscription-management/list-all`anterior corresponde .

O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Consulte [os web docs do MDN no Slug](https://developer.mozilla.org/docs/Glossary/Slug) para a definição de Slug.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Várias rotas de atributos

O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação. O uso mais comum desse recurso é para simular o comportamento da rota convencional padrão, conforme mostrado no exemplo a seguir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Colocar vários atributos de rota no controlador significa que cada um combina com cada um dos atributos de rota nos métodos de ação:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Todas as restrições [de rota do verbo HTTP](#verb) implementam `IActionConstraint`.

Quando vários atributos <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> de rota que implementam são colocados em uma ação:

* Cada restrição de ação combina com o modelo de rota aplicado ao controlador.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Usar várias rotas em ações pode parecer útil e poderoso, é melhor manter o espaço de URL do seu aplicativo básico e bem definido. Use várias rotas em ações **apenas** quando necessário, por exemplo, para dar suporte aos clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo

Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

No código anterior, `[HttpPost("product/{id:int}")]` aplica-se uma restrição de rota. A `ProductsController.ShowProduct` ação é combinada apenas `/product/3`por caminhos de URL como . A parte `{id:int}` do modelo de rota restringe esse segmento a apenas inteiros.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de rota personalizados usando IRouteTemplateProvider

Todos os [atributos](#rt) de rota implementam <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. O tempo de execução do ASP.NET Core:

* Procura atributos nas classes de controladores e métodos de ação quando o aplicativo é iniciado.
* Usa os atributos que implementam `IRouteTemplateProvider` para construir o conjunto inicial de rotas.

Implementar `IRouteTemplateProvider` para definir atributos de rota personalizados. Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

O `Get` método `Order = 2, Template = api/MyTestApi`anterior retorna .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Use o modelo de aplicativo para personalizar rotas de atributos

O modelo de aplicação:

* É um modelo de objeto criado na inicialização.
* Contém todos os metadados usados pelo ASP.NET Core para rodar e executar as ações em um aplicativo.

O modelo de aplicação inclui todos os dados coletados a partir de atributos de rota. Os dados dos atributos de `IRouteTemplateProvider` rota são fornecidos pela implementação. Convenções:

* Pode ser escrito para modificar o modelo do aplicativo para personalizar como o roteamento se comporta.
* São lidos na inicialização do aplicativo.

Esta seção mostra um exemplo básico de personalização do roteamento usando o modelo do aplicativo. O código a seguir faz com que as rotas se alintem aproximadamente com a estrutura da pasta do projeto.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

O código a `namespace` seguir impede que a convenção seja aplicada aos controladores que são atributos roteados:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Por exemplo, o controlador a `NamespaceRoutingConvention`seguir não usa:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

O método `NamespaceRoutingConvention.Apply`:

* Não faz nada se o controlador for atribuído.
* Define o modelo de `namespace`controladores com `namespace` base no , com a base removida.

O `NamespaceRoutingConvention` pode ser `Startup.ConfigureServices`aplicado em:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Por exemplo, considere o seguinte controlador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

No código anterior:

* A `namespace` base `My.Application`é.
* O nome completo do `My.Application.Admin.Controllers.UsersController`controlador anterior é .
* O `NamespaceRoutingConvention` conjunto define o `Admin/Controllers/Users/[action]/{id?`modelo de controladores para .

O `NamespaceRoutingConvention` também pode ser aplicado como um atributo em um controlador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Roteamento misto: roteamento de atributo versus roteamento convencional

ASP.NET aplicativos Core podem misturar o uso de roteamento convencional e roteamento de atributos. É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.

As ações são roteadas convencionalmente ou segundo os atributos. Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo. Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa. **Qualquer** atributo de rota no controlador faz com que **todas as** ações no atributo controlador sejam roteadas.

Roteamento de atributos e roteamento convencional usam o mesmo motor de roteamento.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Geração de URL e valores ambientais

Os aplicativos podem usar recursos de geração de URL de roteamento para gerar links de URL para ações. A geração de URLs elimina URLs de codificação dura, tornando o código mais robusto e sustentável. Esta seção se concentra nos recursos de geração de URL fornecidos pela MVC e apenas cobre os fundamentos de como a geração de URL funciona. Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.

A <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface é o elemento subjacente da infra-estrutura entre MVC e roteamento para geração de URL. Uma instância `IUrlHelper` de está `Url` disponível através da propriedade em controladores, visualizações e componentes de exibição.

No exemplo a `IUrlHelper` seguir, a `Controller.Url` interface é usada através da propriedade para gerar uma URL para outra ação.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Se o aplicativo estiver usando a rota `url` convencional padrão, o `/UrlGeneration/Destination`valor da variável é a seqüência de caminho suiço . Esse caminho de URL é criado por roteamento combinando:

* Os valores de rota da solicitação atual, que são chamados **de valores ambientais.**
* Os valores `Url.Action` passaram e substituindo esses valores no modelo de rota:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente. Um parâmetro de rota que não tem um valor pode:

* Use um valor padrão se ele tiver um.
* Seja ignorado se for opcional. Por exemplo, `id` o modelo `{controller}/{action}/{id?}`de rota do modelo de rota .

A geração de URL falha se algum parâmetro de rota necessário não tiver um valor correspondente. Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.

O exemplo `Url.Action` anterior de assume [o roteamento convencional.](#cr) A geração de URL funciona de forma semelhante com [o roteamento de atributos,](#ar)embora os conceitos sejam diferentes. Com roteamento convencional:

* Os valores de rota são usados para expandir um modelo.
* Os valores `controller` `action` de rota para e geralmente aparecem nesse modelo. Isso funciona porque as URLs coincidiram com o encaminhamento de uma convenção.

O exemplo a seguir usa roteamento de atributos:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

A `Source` ação no código `custom/url/to/destination`anterior gera .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>foi adicionado no ASP.NET Núcleo 3.0 como alternativa a `IUrlHelper`. `LinkGenerator`oferece funcionalidade semelhante, mas mais flexível. Cada método `IUrlHelper` em tem uma família `LinkGenerator` correspondente de métodos também.

### <a name="generating-urls-by-action-name"></a>Gerando URLs pelo nome da ação

[Url.Action,](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e todas as sobrecargas relacionadas são projetadas para gerar o ponto final de destino especificando um nome de controlador e nome de ação.

Ao `Url.Action`usar, os valores `action` de rota atuais são `controller` fornecidos pelo tempo de execução:

* O valor `controller` `action` e fazem parte dos valores e [valores ambientais.](#ambient) O `Url.Action` método sempre usa `action` os `controller` valores atuais e gera um caminho de URL que direciona para a ação atual.

O roteamento tenta usar os valores em valores ambientais para preencher informações que não foram fornecidas ao gerar uma URL. Considere uma `{a}/{b}/{c}/{d}` rota como `{ a = Alice, b = Bob, c = Carol, d = David }`com valores ambientais:

* O roteamento tem informações suficientes para gerar uma URL sem quaisquer valores adicionais.
* O roteamento tem informações suficientes porque todos os parâmetros de rota têm um valor.

Se o `{ d = Donovan }` valor for adicionado:

* O `{ d = David }` valor é ignorado.
* O caminho de `Alice/Bob/Carol/Donovan`URL gerado é .

**Aviso**: os caminhos de URL são hierárquicos. No exemplo anterior, se `{ c = Cheryl }` o valor for adicionado:

* Ambos os `{ c = Carol, d = David }` valores são ignorados.
* Não há mais um `d` valor para e a geração de URL falha.
* Os valores `c` desejados e `d` devem ser especificados para gerar uma URL.  

Você pode esperar para acertar este `{controller}/{action}/{id?}`problema com a rota padrão . Esse problema é raro `Url.Action` na prática porque `controller` `action` sempre especifica explicitamente um e valor.

Várias sobrecargas de [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) levam um objeto de `controller` valores de rota para fornecer valores para parâmetros de rota que não sejam e `action`. O objeto de valores `id`de rota é frequentemente usado com . Por exemplo, `Url.Action("Buy", "Products", new { id = 17 })`. O objeto de valores de rota:

* Por convenção é geralmente um objeto de tipo anônimo.
* Pode ser `IDictionary<>` um [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

O código `/Products/Buy/17?color=red`anterior gera .

O código a seguir gera uma URL absoluta:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Para criar uma URL absoluta, use um dos seguintes:

* Uma sobrecarga que `protocol`aceita um. Por exemplo, o código anterior.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que gera URIs absolutas por padrão.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Gerar URLs por rota

O código anterior demonstrou a geração de uma URL passando no controlador e no nome da ação. `IUrlHelper`também fornece a família de métodos [Url.RouteUrl.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) Esses métodos são semelhantes ao [Url.Action,](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)mas não `action` `controller` copiam os valores atuais de e para os valores de rota. O uso mais `Url.RouteUrl`comum de:

* Especifica um nome de rota para gerar a URL.
* Geralmente não especifica um controlador ou nome de ação.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

O seguinte arquivo Razor gera `Destination_Route`um link HTML para:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Gerar URLs em HTML e Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>fornece <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> os métodos [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para gerar `<form>` e `<a>` elementos, respectivamente. Esses métodos usam o método [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para gerar uma URL e aceitam argumentos semelhantes. O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.

TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`. Ambos usam `IUrlHelper` para sua implementação. Consulte [Tag Helpers em formulários](xref:mvc/views/working-with-forms) para obter mais informações.

Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Geração de URL em Resultados de Ação

Os exemplos anteriores `IUrlHelper` mostraram o uso em um controlador. O uso mais comum em um controlador é gerar uma URL como parte de um resultado de ação.

As classes base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação. Um uso típico é redirecionar depois de aceitar a entrada do usuário:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

A ação resulta em <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> métodos de fábrica como `IUrlHelper`e seguem um padrão semelhante aos métodos em .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rotas convencionais dedicadas

[O roteamento convencional](#cr) pode usar um tipo especial de definição de rota chamada [rota convencional dedicada](#dcr). No exemplo a seguir, `blog` a rota nomeada é uma rota convencional dedicada:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Usando as definições `Url.Action("Index", "Home")` de rota `/` anteriores, gera o caminho de URL usando a `default` rota, mas por quê? Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.

[As rotas convencionais dedicadas](#dcr) dependem de um comportamento especial de valores padrão que não possuem um parâmetro de rota correspondente que impede que a rota seja muito [gananciosa](xref:fundamentals/routing#greedy) com a geração de URL. Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota. Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão. A geração `blog` de URL `{ controller = Home, action = Index }` usando falha `{ controller = Blog, action = Article }`porque os valores não correspondem . O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

[As áreas](xref:mvc/controllers/areas) são um recurso de MVC usado para organizar funcionalidades relacionadas em um grupo separadamente:

* Roteamento do namespace para ações do controlador.
* Estrutura de pastas para visualizações.

O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que tenham áreas diferentes. O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`. Esta seção discute como o roteamento interage com as áreas. Consulte [Áreas](xref:mvc/controllers/areas) para obter detalhes sobre como as áreas são usadas com vistas.

O exemplo a seguir configura mvc para `area` usar a `area` `Blog`rota convencional padrão e uma rota para um nomeado :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> é chamado `"blog_route"`para criar o . O segundo parâmetro `"Blog"`é o nome da área.

Ao combinar um `/Manage/Users/AddUser`caminho `"blog_route"` de URL como, a rota gera os valores `{ area = Blog, controller = Users, action = AddUser }`de rota . O `area` valor da rota é `area`produzido por um valor padrão para . A rota criada `MapAreaControllerRoute` por é equivalente à seguinte:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`. O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.

O roteamento convencional é dependente da ordem. Em geral, rotas com áreas devem ser colocadas mais cedo, pois são mais específicas do que rotas sem área.

Usando o exemplo anterior, `{ area = Blog, controller = Users, action = AddUser }` os valores de rota correspondem à seguinte ação:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

O atributo [[Área]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) é o que denota um controlador como parte de uma área. Este controlador está `Blog` na área. Os controladores `[Area]` sem um atributo não são membros `area` de nenhuma área e **não** correspondem quando o valor da rota é fornecido pelo roteamento. No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

O namespace de cada controlador é mostrado aqui para completude. Se os controladores anteriores usarem o mesmo namespace, um erro do compilador será gerado. Namespaces de classe não têm efeito sobre o roteamento do MVC.

Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`. O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.

<a name="aa"></a>

Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.

Ao executar uma ação dentro de uma `area` área, o valor de rota para está disponível como um [valor ambiente](#ambient) para roteamento para geração de URL. Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

O código a seguir `/Zebra/Users/AddUser`gera uma URL para:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definição de ação

Métodos públicos em um controlador, exceto aqueles com o atributo [NonAction,](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) são ações.

## <a name="sample-code"></a>Código de exemplo

 * O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no download da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.
* [Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O ASP.NET Core MVC usa o [middleware](xref:fundamentals/middleware/index) de Roteamento para fazer as correspondências das URLs de solicitações de entrada e mapeá-las para ações. As rotas são definidas em atributos ou no código de inicialização. Elas descrevem como deve ser feita a correspondência entre caminhos de URL e ações. As rotas também são usadas para gerar URLs (para links) enviados em resposta.

As ações são roteadas convencionalmente ou segundo os atributos. Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo. Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).

Este documento explicará as interações entre o MVC e o roteamento e como aplicativos MVC comuns usam recursos de roteamento. Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes sobre o roteamento avançado.

## <a name="setting-up-routing-middleware"></a>Configurando o middleware de Roteamento

No método *Configurar*, você poderá ver código semelhante a:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Dentro da chamada para `UseMvc`, `MapRoute` é usado para criar uma única rota, que chamaremos de rota `default`. A maioria dos aplicativos MVC usa uma rota com um modelo semelhante à rota `default`.

O modelo de rota `"{controller=Home}/{action=Index}/{id?}"` pode corresponder a um caminho de URL como `/Products/Details/5` e extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` gerando tokens para o caminho. O MVC tentará localizar um controlador chamado `ProductsController` e executar a ação `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Observe que, neste exemplo, o model binding usaria o valor de `id = 5` para definir o parâmetro `id` como `5` ao invocar essa ação. Consulte [Model binding](../models/model-binding.md) para obter mais detalhes.

Usando a rota `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

O modelo da rota:

* `{controller=Home}` define `Home` como o `controller` padrão

* `{action=Index}` define `Index` como o `action` padrão

* `{id?}` define `id` como opcional

Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência. Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.

`"{controller=Home}/{action=Index}/{id?}"` pode corresponder ao caminho da URL `/` e produzirá os valores de rota `{ controller = Home, action = Index }`. Os valores de `controller` e `action` usam os valores padrão, `id` não produz um valor, uma vez que não há nenhum segmento correspondente no caminho da URL. O MVC usaria esses valores de rota para selecionar a ação `HomeController` e `Index`:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Usando essa definição de controlador e modelo de rota, a ação `HomeController.Index` seria executada para qualquer um dos caminhos de URL a seguir:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

O método de conveniência `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Pode ser usado para substituir:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` e `UseMvcWithDefaultRoute` adicionam uma instância de `RouterMiddleware` ao pipeline de middleware. O MVC não interage diretamente com o middleware e usa o roteamento para tratar das solicitações. O MVC é conectado às rotas por meio de uma instância de `MvcRouteHandler`. O código dentro de `UseMvc` é semelhante ao seguinte:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` não define diretamente nenhuma rota, ele adiciona um espaço reservado à coleção de rotas para a rota `attribute`. A sobrecarga `UseMvc(Action<IRouteBuilder>)` permite adicionar suas próprias rotas e também dá suporte ao roteamento de atributos.  `UseMvc`e todas as suas variações adicionam um espaço reservado para a rota de `UseMvc`atributos - o roteamento de atributos está sempre disponível independentemente de como você configura . `UseMvcWithDefaultRoute` define uma rota padrão e dá suporte ao roteamento de atributos. A seção [Roteamento de atributos](#attribute-routing-ref-label) inclui mais detalhes sobre o roteamento de atributos.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Roteamento convencional

A rota `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

O código anterior é um exemplo de um roteamento convencional. Esse estilo é chamado de roteamento convencional porque estabelece uma *convenção* para caminhos de URL:

* O primeiro segmento de caminho mapeia o nome do controlador.
* O segundo mapeia o nome da ação.
* O terceiro segmento é `id`usado para um opcional . `id`mapas para uma entidade modelo.

Usando essa rota `default`, o caminho da URL `/Products/List` é mapeado para a ação `ProductsController.List` e `/Blog/Article/17` é mapeado para `BlogController.Article`. Esse mapeamento é baseado **somente** nos nomes do controlador e da ação e não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.

> [!TIP]
> Usar o roteamento convencional com a rota padrão permite compilar o aplicativo rapidamente sem precisar criar um novo padrão de URL para cada ação que você definir. Para um aplicativo com ações de estilo CRUD, ter consistência para as URLs em seus controladores pode ajudar a simplificar seu código e a tornar sua interface do usuário mais previsível.

> [!WARNING]
> O `id` é definido como opcional pelo modelo de rota, o que significa que suas ações podem ser executadas sem a ID fornecida como parte da URL. Normalmente, o que acontecerá se `id` for omitido da URL é que ele será definido como `0` pelo model binding e, dessa forma, não será encontrada no banco de dados nenhuma entidade correspondente a `id == 0`. O roteamento de atributos pode lhe proporcionar controle refinado para tornar a ID obrigatória para algumas ações e não para outras. Por convenção, a documentação incluirá parâmetros opcionais, como `id`, quando for provável que eles apareçam no uso correto.

## <a name="multiple-routes"></a>Várias rotas

É possível adicionar várias rotas dentro de `UseMvc` adicionando mais chamadas para `MapRoute`. Fazer isso permite que você defina várias convenções ou que adicione rotas convencionais dedicadas a uma ação específica, como:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

A rota `blog` aqui é uma *rota convencional dedicada*, o que significa que ela usa o sistema de roteamento convencional, mas é dedicada a uma ação específica. Como `controller` e `action` não aparecem no modelo de rota como parâmetros, eles só podem ter os valores padrão e, portanto, essa rota sempre será mapeada para a ação `BlogController.Article`.

As rotas na coleção de rotas são ordenadas e serão processadas na ordem em que forem adicionadas. Portanto, neste exemplo, a rota `blog` será tentada antes da rota `default`.

> [!NOTE]
> *Rotas convencionais dedicadas* geralmente usam `{*article}` parâmetros de rota de captura de todos **os** gostos, como capturar a parte restante do caminho da URL. Isso pode fazer com que uma rota fique "muito ambiciosa", ou seja, que faça a correspondência com URLs que deveriam ser correspondidas com outras rotas. Coloque as rotas "ambiciosas" mais adiante na tabela de rotas para solucionar esse problema.

### <a name="fallback"></a>Fallback

Como parte do processamento de solicitações, o MVC verificará se o valores das rotas podem ser usados para encontrar um controlador e uma ação em seu aplicativo. Se os valores das rotas não corresponderem a uma ação, a rota não será considerada correspondente e a próxima rota será tentada. Isso é chamado de *fallback* e sua finalidade é simplificar casos em que rotas convencionais se sobrepõem.

### <a name="disambiguating-actions"></a>Desambiguação de ações

Quando duas ações correspondem por meio do roteamento, o MVC precisa resolver a ambiguidade para escolher a "melhor" candidata ou lançar uma exceção. Por exemplo:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Esse controlador define duas ações que fariam a correspondência entre caminho da URL `/Products/Edit/17` e a os dados da rota `{ controller = Products, action = Edit, id = 17 }`. Este é um padrão comum para controladores MVC em que `Edit(int)` mostra um formulário para editar um produto e `Edit(int, Product)` processa o formulário postado. Para que isso seja possível, o MVC precisa escolher `Edit(int, Product)` quando a solicitação é um `POST` HTTP e `Edit(int)` quando o verbo HTTP é qualquer outra coisa.

O `HttpPostAttribute` (`[HttpPost]`) é uma implementação de `IActionConstraint` que só permite que a ação seja selecionada quando o verbo HTTP é `POST`. A presença de um `IActionConstraint` faz do `Edit(int, Product)` uma "melhor" correspondência do que `Edit(int)`, portanto, `Edit(int, Product)` será tentado primeiro.

Você só precisará gravar implementações personalizadas de `IActionConstraint` em cenários especializados, mas é importante compreender a função de atributos como `HttpPostAttribute` – atributos semelhantes são definidos para outros verbos HTTP. No roteamento convencional, é comum que ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de `show form -> submit form`. A conveniência desse padrão ficará mais aparente após você revisar a seção [Noções básicas sobre IActionConstraint](#understanding-iactionconstraint).

Se várias rotas corresponderem e o MVC não puder encontrar uma rota "melhor", ele gerará um `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Nomes de rotas

As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Os nomes de rotas dão a uma rota um nome lógico, de modo que a rota nomeada possa ser usada para geração de URL. Isso simplifica muito a criação de URLs quando a ordenação de rotas poderia complicá-la. Nomes de rotas devem ser exclusivos no nível do aplicativo.

Os nomes de rotas não têm impacto sobre a correspondência de URLs ou o tratamento de solicitações; eles são usados apenas para a geração de URLs. [Roteamento](xref:fundamentals/routing) tem informações mais detalhadas sobre geração de URLs, incluindo a geração de URLs em auxiliares específicos do MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Roteamento de atributo

O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota. No exemplo a seguir, `app.UseMvc();` é usado no método `Configure` e nenhuma rota é passada. O `HomeController` corresponderá a um conjunto de URLs semelhantes ao que a rota padrão `{controller=Home}/{action=Index}/{id?}` corresponderia:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

A ação `HomeController.Index()` será executada para qualquer um dos caminhos de URL `/`, `/Home` ou `/Home/Index`.

> [!NOTE]
> Este exemplo destaca uma diferença importante de programação entre o roteamento de atributo e o roteamento convencional. O roteamento de atributo requer mais entradas para especificar uma rota; a rota padrão convencional manipula as rotas de forma mais sucinta. No entanto, o roteamento de atributo permite (e exige) o controle preciso de quais modelos de rota se aplicam a cada ação.

Com o roteamento de atributo, o nome do controlador e os nomes de ação não desempenham **nenhuma** função quanto a qual ação é selecionada. Este exemplo corresponderá as mesmas URLs que o exemplo anterior.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Os modelos de rota acima não definem parâmetros de rota para `action`, `area` e `controller`. Na verdade, esses parâmetros de rota não são permitidos em rotas de atributo. Uma vez que o modelo de rota já está associado a uma ação, não faria sentido analisar o nome da ação da URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Roteamento de atributo com atributos Http[Verb]

O roteamento de atributo também pode usar atributos `Http[Verb]`, como `HttpPostAttribute`. Todos esses atributos podem aceitar um modelo de rota. Este exemplo mostra duas ações que correspondem ao mesmo modelo de rota:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Para um caminho de URL como `/products`, a ação `ProductsApi.ListProducts` será executada quando o verbo HTTP for `GET` e `ProductsApi.CreateProduct` será executado quando o verbo HTTP for `POST`. Primeiro, o roteamento de atributo faz a correspondência da URL com o conjunto de modelos de rota definidos por atributos de rota. Quando um modelo de rota for correspondente, restrições de `IActionConstraint` serão aplicadas para determinar quais ações podem ser executadas.

> [!TIP]
> Ao construir uma API REST, é raro que `[Route(...)]` você queira usar em um método de ação, pois a ação aceitará todos os métodos HTTP. É melhor usar o `Http*Verb*Attributes` mais específico para ser preciso quanto ao que tem suporte de sua API. Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.

Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota. Neste exemplo, `id` é obrigatório como parte do caminho da URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

A ação `ProductsApi.GetProduct(int)` será executada para um caminho de URL como `/products/3`, mas não para um caminho de URL como `/products`. Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.

## <a name="route-name"></a>Nome da rota

O código a seguir define um *nome da rota* como `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica. Nomes de rota não têm impacto sobre o comportamento de correspondência da URL e são usados somente para geração de URLs. Nomes de rotas devem ser exclusivos no nível do aplicativo.

> [!NOTE]
> Compare isso com a *rota padrão* convencional, que define o parâmetro `id` como opcional (`{id?}`). Essa capacidade de especificar APIs de forma específica tem vantagens, como permitir que `/products` e `/products/5` sejam expedidos para ações diferentes.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Combinando rotas

Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais. Modelos de rota definidos no controlador precedem modelos de rota nas ações.  Colocar um atributo de rota no controlador foz com que **todas** as ações no controlador usem o roteamento de atributo.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

Neste exemplo, o caminho de URL `/products` pode corresponder a `ProductsApi.ListProducts` e o caminho de URL `/products/5` pode corresponder a `ProductsApi.GetProduct(int)`. Ambas as ações só `GET` correspondem a HTTP `HttpGetAttribute`porque estão marcadas com o .

Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador. Este exemplo corresponde a um conjunto de caminhos de URL semelhante à *rota padrão*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordenando rotas de atributos

Em contraste com as rotas convencionais, que são executadas em uma ordem definida, o roteamento de atributos constrói uma árvore e corresponde a todas as rotas simultaneamente. O comportamento é como se as entradas de rota fossem colocadas em uma ordem ideal; as rotas mais específicas têm uma chance de ser executadas antes das rotas mais gerais.

Por exemplo, uma rota como `blog/search/{topic}` é mais específica que uma rota como `blog/{*article}`. Em termos da lógica, a rota `blog/search/{topic}` é "executada" primeiro, por padrão, porque essa é a única ordem que faz sentido. Usando o roteamento convencional, o desenvolvedor é responsável por colocar as rotas na ordem desejada.

Rotas de atributos podem configurar uma ordem, usando a propriedade `Order` de todos os atributos de rota fornecidos pela estrutura. As rotas são processadas segundo uma classificação crescente da propriedade `Order`. A ordem padrão é `0`. Uma rota definida usando `Order = -1` será executada antes de rotas que não definem uma ordem. Uma rota definida usando `Order = 1` será executada após a ordem das rotas padrão.

> [!TIP]
> Evite depender de `Order`. Se o seu espaço de URL exigir valores de ordem explícita para fazer o roteamento corretamente, provavelmente ele também será confuso para os clientes. De modo geral, o roteamento de atributos selecionará a rota correta com a correspondência de URL. Se a ordem padrão usada para a geração de URL não estiver funcionando, usar o nome da rota como uma substituição geralmente será mais simples do que aplicar a propriedade `Order`.

Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação. Informações sobre a ordem de rota nos tópicos do Razor Pages estão disponíveis em [Convenções de rota e aplicativo do Razor Pages: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Substituição de token em modelos de rota ([controlador] [ação] [área])

Para conveniência, as rotas de atributo suportam a substituição`[`de `]` *tokens,* anexando um token em chaves quadradas (, ). Os tokens `[action]`, `[area]` e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida. No exemplo a seguir, as ações correspondem a caminhos de URL conforme descrito nos comentários:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

A substituição de token ocorre como a última etapa da criação das rotas de atributo. O exemplo acima se comportará da mesma forma que o código a seguir:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Rotas de atributo também podem ser combinadas com herança. Isso é especialmente eficiente em combinação com a substituição de token.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

A substituição de token também se aplica a nomes de rota definidos por rotas de atributo. `[Route("[controller]/[action]", Name="[controller]_[action]")]` gera um nome de rota exclusivo para cada ação.

Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar um transformador de parâmetro para personalizar a substituição de token

A substituição do token pode ser personalizada usando um transformador de parâmetro. Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros. Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.

O `RouteTokenTransformerConvention` é uma convenção de modelo de aplicativo que:

* Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.
* Personaliza os valores de token de rota de atributo conforme eles são substituídos.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Várias rotas

O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação. O uso mais comum desse recurso é para simular o comportamento da *rota convencional padrão*, conforme mostrado no exemplo a seguir:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Colocar vários atributos de rota no controlador significa que cada um deles será combinado com cada um dos atributos de rota nos métodos de ação.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Quando vários atributos de rota (que implementam `IActionConstraint`) são colocados em uma ação, cada restrição da ação combina com o modelo de rota do atributo que a definiu.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Embora o uso de várias rotas em ações possa parecer eficaz, é melhor manter o espaço de URL de seu aplicativo simples e bem definido. Use várias rotas em ações somente quando for necessário; por exemplo, para dar suporte a clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo

Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de rota personalizados usando `IRouteTemplateProvider`

Todos os atributos de rota fornecidos na estrutura ( `[Route(...)]`, `[HttpGet(...)]` etc.) implementam a interface `IRouteTemplateProvider`. O MVC procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado e usa aqueles que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.

Você pode implementar `IRouteTemplateProvider` para definir seus próprios atributos de rota. Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

O atributo do exemplo acima configura automaticamente o `Template` como `"api/[controller]"` quando `[MyApiController]` é aplicado.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Usando o Modelo de Aplicativo para personalizar rotas de atributo

O *modelo de aplicativo* é um modelo de objeto criado durante a inicialização com todos os metadados usados pelo MVC para rotear e executar suas ações. O *modelo de aplicativo* inclui todos os dados reunidos dos atributos de rota (por meio de `IRouteTemplateProvider`). Você pode escrever *convenções* para modificar o modelo do aplicativo no momento da inicialização para personalizar o comportamento do roteamento. Esta seção mostra um exemplo simples de personalização de roteamento usando o modelo de aplicativo.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Roteamento misto: roteamento de atributo versus roteamento convencional

Aplicativos MVC podem combinar o uso do roteamento convencional e do roteamento de atributo. É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.

As ações são roteadas convencionalmente ou segundo os atributos. Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo. Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa. **Qualquer** atributo de rota no controlador faz com que todas as ações no atributo de controlador sejam roteadas.

> [!NOTE]
> O que diferencia os dois tipos de sistemas de roteamento é o processo aplicado após uma URL corresponder a um modelo de rota. No roteamento convencional, os valores de rota da correspondência são usados para escolher a ação e o controlador em uma tabela de pesquisa com todas as ações roteadas convencionais. No roteamento de atributo, cada modelo já está associado a uma ação e nenhuma pesquisa adicional é necessária.

## <a name="complex-segments"></a>Segmentos complexos

Segmentos complexos (por exemplo, `[Route("/dog{token}cat")]`), são processados combinando literais da direita para a esquerda de uma maneira não Greedy. Veja [o código-fonte](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) para obter uma descrição. Para obter mais informações, confira [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Geração de URL

Aplicativos MVC podem usar os recursos de geração de URL do roteamento para gerar links de URL para ações. Gerar URLs elimina a necessidade de codificar URLs, tornando seu código mais robusto e sustentável. Esta seção tem como foco os recursos de geração de URL fornecidos pelo MVC e só aborda as noções básicas de como a geração de URL funciona. Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.

A interface `IUrlHelper` é a parte subjacente da infraestrutura entre o MVC e o roteamento para geração de URL. Você encontrará uma instância de `IUrlHelper` disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.

Neste exemplo, a interface `IUrlHelper` é usada por meio a propriedade `Controller.Url` para gerar uma URL para outra ação.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho de URL `/UrlGeneration/Destination`. Esse caminho de URL é criado pelo roteamento combinando os valores de rota da solicitação atual (valores de ambiente) com os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente. Um parâmetro de rota que não tem um valor pode usar um valor padrão se houver um ou pode ser ignorado se for opcional (como no caso de `id` neste exemplo). A geração de URL falhará se qualquer parâmetro de rota obrigatório não tiver um valor correspondente. Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.

O exemplo de `Url.Action` acima pressupõe que o roteamento seja convencional, mas a geração de URL funciona de forma semelhante com o roteamento de atributo, embora os conceitos sejam diferentes. Com o roteamento convencional, os valores de rota são usados para expandir um modelo e os valores de rota para `controller` e `action` normalmente são exibidos no modelo – isso funciona porque as URLs correspondidas pelo roteamento aderem a uma *convenção*. No roteamento de atributo, os valores de rota para `controller` e `action` não podem ser exibidos no modelo; em vez disso, eles são usados para pesquisar o modelo a ser usado.

Este exemplo usa o roteamento de atributo:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

O MVC cria uma tabela de pesquisa de todas as ações de atributo roteadas e faz a correspondência dos valores de `controller` e `action` para selecionar o modelo de rota a ser usado para geração de URL. Na amostra acima, `custom/url/to/destination` é gerado.

### <a name="generating-urls-by-action-name"></a>Gerando URLs pelo nome da ação

`Url.Action` (`IUrlHelper` . `Action`) e todas as sobrecargas relacionadas são baseadas na ideia de que você deseja especificar ao que está vinculando, especificando um nome do controlador e um nome da ação.

> [!NOTE]
> Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são especificados para você – o valor de `controller` e `action` fazem parte de *valores de ambiente* **e de ** *valores*. O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.

O roteamento tenta usar os valores em valores de ambiente para preencher informações que você não forneceu ao gerar uma URL. Usando uma rota como `{a}/{b}/{c}/{d}` e valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, o roteamento tem informações suficientes para gerar uma URL sem valores adicionais – uma vez que todos os parâmetros de rota têm um valor. Se você tiver adicionado o valor `{ d = Donovan }`, o valor `{ d = David }` será ignorado e o caminho de URL gerado será `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Caminhos de URL são hierárquicos. No exemplo acima, se você tiver adicionado o valor `{ c = Cheryl }`, ambos os valores `{ c = Carol, d = David }` serão ignorados. Nesse caso, não teremos mais um valor para `d` e a geração de URL falhará. Você precisaria especificar o valor desejado de `c` e `d`.  Você pode esperar se deparar com esse problema com a rota padrão (`{controller}/{action}/{id?}`) – mas raramente encontrará esse comportamento na prática, pois `Url.Action` sempre especificará explicitamente um valor de `controller` e `action`.

Sobrecargas maiores de `Url.Action` também usam um objeto adicional de *valores de rota* para fornecer valores para parâmetros de rota diferentes de `controller` e `action`. É mais comum ver isso com `id` como `Url.Action("Buy", "Products", new { id = 17 })`. Por convenção, o objeto de *valores de rota* geralmente é um objeto de tipo anônimo, mas também pode ser um `IDictionary<>` ou um *objeto .NET simples*. Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Para criar uma URL absoluta, use uma sobrecarga que aceita um `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Gerando URLs pela rota

O código acima demonstrou a geração de uma URL passando o nome do controlador e da ação. `IUrlHelper` também fornece a família de métodos `Url.RouteUrl`. Esses métodos são semelhantes a `Url.Action`, mas não copiam os valores atuais de `action` e `controller` para os valores de rota. O uso mais comum é especificar um nome de rota para usar uma rota específica para gerar a URL, geralmente *sem* especificar um nome de controlador ou de ação.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Gerar URLs em HTML

`IHtmlHelper` fornece o métodos `Html.BeginForm` e `Html.ActionLink` de `HtmlHelper` para gerar elementos `<form>` e `<a>` respectivamente. Esses métodos usam o método `Url.Action` para gerar uma URL e aceitam argumentos semelhantes. O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.

TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`. Ambos usam `IUrlHelper` para sua implementação. Consulte [Trabalhando com Formulários](../views/working-with-forms.md) para obter mais informações.

Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Gerando URLS nos resultados da ação

Os exemplos acima mostraram o uso de `IUrlHelper` em um controlador, enquanto o uso mais comum em um controlador é gerar uma URL como parte do resultado de uma ação.

As classes base `ControllerBase` e `Controller` fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação. Um uso típico é para redirecionar após aceitar a entrada do usuário.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Os métodos de fábrica dos resultados da ação seguem um padrão semelhante aos métodos em `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rotas convencionais dedicadas

O roteamento convencional pode usar um tipo especial de definição de rota chamado *rota convencional dedicada*. No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Usando essas definições de rota, `Url.Action("Index", "Home")` gerará o caminho de URL `/` com a rota `default`, mas por quê? Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.

Rotas convencionais dedicadas dependem de um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impeça que a rota seja "muito ambiciosa" com a geração de URLs. Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota. Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão. A geração de URL usando `blog` falhará porque os valores de `{ controller = Home, action = Index }` não correspondem a `{ controller = Blog, action = Article }`. O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

[Áreas](areas.md) são um recurso do MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace de roteamento (para ações do controlador) e estrutura de pasta (para exibições) separada. O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que tenham *áreas* diferentes. O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`. Esta seção aborda como o roteamento interage com as áreas. Consulte [Áreas](areas.md) para obter detalhes sobre como as áreas são usadas com exibições.

O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma *rota de área* para uma área chamada `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a primeira rota produzirá os valores de rota `{ area = Blog, controller = Users, action = AddUser }`. O valor de rota `area` é produzido por um valor padrão para `area`. De fato, a rota criada por `MapAreaRoute` é equivalente à seguinte:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`. O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.

> [!TIP]
> O roteamento convencional é dependente da ordem. De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.

Usando o exemplo acima, os valores de rota corresponderiam à ação a seguir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

O `AreaAttribute` é o que indica que um controlador faz parte de uma área; dizemos que esse controlador está na área `Blog`. Controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** corresponderão quando o valor de rota `area` for fornecido pelo roteamento. No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> O namespace de cada controlador é mostrado aqui para fins de integridade – caso contrário, os controladores teriam um conflito de nomenclatura e gerariam um erro do compilador. Namespaces de classe não têm efeito sobre o roteamento do MVC.

Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`. O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.

> [!NOTE]
> Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.

Ao executar uma ação dentro de uma área, o valor de rota para `area` estará disponível como um *valor de ambiente* para o roteamento usar para geração de URL. Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Entendendo IActionConstraint

> [!NOTE]
> Esta seção é uma análise aprofundada dos elementos internos da estrutura e de como o MVC escolhe uma ação para ser executada. Um aplicativo típico não precisará de um `IActionConstraint` personalizado

Provavelmente, você já usou `IActionConstraint` mesmo que não esteja familiarizado com a interface. O atributo `[HttpGet]` e atributos `[Http-VERB]` semelhantes implementam `IActionConstraint` para limitar a execução de um método de ação.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Presumindo a rota convencional padrão, o caminho de URL `/Products/Edit` produziria os valores `{ controller = Products, action = Edit }`, que corresponderiam a **ambas** as ações mostradas aqui. Na terminologia `IActionConstraint`, diríamos que essas duas ações são consideradas candidatas – uma vez que ambas correspondem aos dados da rota.

Quando for executado, `HttpGetAttribute` indicará que *Edit()* corresponde a *GET* e não corresponde a nenhum outro verbo HTTP. A ação `Edit(...)` não tem restrições definidas e, portanto, corresponderá a qualquer verbo HTTP. Sendo assim, supondo um `POST`, `Edit(...)` será correspondente. Mas, para um `GET`, ambas as ações ainda podem corresponder – no entanto, uma ação com um `IActionConstraint` sempre é considerada *melhor* que uma ação sem. Assim, como `Edit()` tem `[HttpGet]`, ela é considerada mais específica e será selecionada se as duas ações puderem corresponder.

Conceitualmente, `IActionConstraint` é uma forma de *sobrecarga*, mas em vez de uma sobrecarga de métodos com o mesmo nome, trata-se da sobrecarga entre ações que correspondem à mesma URL. O roteamento de atributo também usa `IActionConstraint` e pode fazer com que ações de controladores diferentes sejam consideradas candidatas.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementando IActionConstraint

A maneira mais simples de implementar um `IActionConstraint` é criar uma classe derivada de `System.Attribute` e colocá-la em suas ações e controladores. O MVC descobrirá automaticamente qualquer `IActionConstraint` que for aplicado como atributo. Você pode usar o modelo de aplicativo para aplicar restrições e, provavelmente, essa é a abordagem mais flexível, pois permite a você faça uma metaprogramação de como elas são aplicadas.

No exemplo a seguir, uma restrição escolhe uma ação com base em um código de *país* a partir dos dados da rota. O [exemplo completo no GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Você é responsável por implementar o método `Accept` e por escolher uma "ordem" na qual a restrição deve ser executada. Nesse caso, o método `Accept` retorna `true` para indicar que a ação é correspondente quando o valor de rota `country` é correspondente. Isso é diferente de um `RouteValueAttribute`, pois permite o fallback para uma ação não atribuída. O exemplo mostra que se você definir uma ação `en-US`, um código de país como `fr-FR` fará o fallback para um controlador mais genérico que não tem `[CountrySpecific(...)]` aplicado.

A propriedade `Order` decide de qual *estágio* a restrição faz parte. Restrições de ação são executadas em grupos com base no `Order`. Por exemplo, todos atributos de método HTTP fornecidos pela estrutura usam o mesmo valor de `Order` para que sejam executados no mesmo estágio. Você pode ter tantos estágios quantos forem necessários para implementar suas políticas desejadas.

> [!TIP]
> Para decidir o valor para `Order`, considere se sua restrição deve ou não deve ser aplicada antes de métodos HTTP. Números inferiores são executados primeiro.

::: moniker-end
