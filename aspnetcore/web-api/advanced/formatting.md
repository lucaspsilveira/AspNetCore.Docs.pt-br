---
title: Formatar dados de resposta na API Web ASP.NET Core
author: ardalis
description: Saiba como formatar dados de resposta na API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642706"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formatar dados de resposta na API Web ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

ASP.NET O Core MVC tem suporte para formatação de dados de resposta. Os dados de resposta podem ser formatados usando formatos específicos ou em resposta ao formato solicitado pelo cliente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Resultados de ação específicos para formato

Alguns tipos de resultado de ação são específicos a um formato específico, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>. As ações podem retornar resultados formatados em um formato específico, independentemente das preferências do cliente. Por exemplo, `JsonResult` o retorno de dados formatados por JSON. O `ContentResult` retorno ou uma seqüência retorna dados de seqüência formatados em texto simples.

Uma ação não é necessária para retornar qualquer tipo específico. ASP.NET Core suporta qualquer valor de retorno de objeto.  Os resultados de ações que <xref:Microsoft.AspNetCore.Mvc.IActionResult> retornam objetos que <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> não são tipos são serializados usando a implementação apropriada. Para obter mais informações, consulte <xref:web-api/action-return-types>.

O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> de ajuda incorporado retorna dados formatados por JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

O download da amostra retorna a lista de autores. Usando as ferramentas de desenvolvedor do navegador F12 ou [Carteiro](https://www.getpostman.com/tools) com o código anterior:

* O cabeçalho de resposta que contém **o tipo de conteúdo:** `application/json; charset=utf-8` é exibido.
* Os cabeçalhos de solicitação são exibidos. Por exemplo, `Accept` o cabeçalho. O `Accept` cabeçalho é ignorado pelo código anterior.

Para retornar dados formatados como texto sem formatação, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> e o auxiliar <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

No código anterior, `Content-Type` o `text/plain`retornado é . Retornando uma seqüência `Content-Type` de entregas de: `text/plain`

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Para ações com vários `IActionResult`tipos de retorno, retorne . Por exemplo, retornando diferentes códigos de status HTTP com base no resultado das operações realizadas.

## <a name="content-negotiation"></a>Negociação de conteúdo

A negociação de conteúdo ocorre quando o cliente especifica um [cabeçalho Aceitar](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). O formato padrão usado por ASP.NET Core é [JSON](https://json.org/). Negociação de conteúdo é:

* Implementado <xref:Microsoft.AspNetCore.Mvc.ObjectResult>por .
* Incorporada aos resultados de ação específicos do código de status retornados dos métodos auxiliares. Os métodos auxiliares dos `ObjectResult`resultados de ação são baseados em .

Quando um tipo de modelo é `ObjectResult`retornado, o tipo de retorno é .

O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Por padrão, ASP.NET `application/json`Core `text/json`suporta `text/plain` , e tipos de mídia. Ferramentas como [Fiddler](https://www.telerik.com/fiddler) ou [Carteiro](https://www.getpostman.com/tools) `Accept` podem definir o cabeçalho de solicitação para especificar o formato de retorno. Quando `Accept` o cabeçalho contém um tipo que o servidor suporta, esse tipo é retornado. A próxima seção mostra como adicionar matérias adicionais.

As ações do controlador podem retornar POCOs (Plain Old CLR Objects). Quando um POCO é devolvido, o tempo `ObjectResult` de execução cria automaticamente um que envolve o objeto. O cliente recebe o objeto serializado formatado. Se o objeto que `null`está `204 No Content` sendo devolvido é, uma resposta é devolvida.

Retornando um tipo de objeto:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

No código anterior, uma solicitação de um `200 OK` alias de autor válido retorna uma resposta com os dados do autor. Uma solicitação de um alias `204 No Content` inválido retorna uma resposta.

### <a name="the-accept-header"></a>O cabeçalho Accept

A *negociação* de `Accept` conteúdo ocorre quando um cabeçalho aparece na solicitação. Quando uma solicitação contém um cabeçalho de aceitação, ASP.NET Core:

* Enumera os tipos de mídia no cabeçalho de aceitação na ordem de preferência.
* Tenta encontrar um formatter que possa produzir uma resposta em um dos formatos especificados.

Se não for encontrado, não for encontrado, isso possa satisfazer a solicitação do cliente, ASP.NET Núcleo:

* Retorna `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> se tiver sido definido, ou -
* Tenta encontrar o primeiro formatter que pode produzir uma resposta.

Se não for configurado para o formato solicitado, o primeiro formatter que pode formatar o objeto é usado. Se `Accept` nenhum cabeçalho aparecer na solicitação:

* O primeiro assunto que pode lidar com o objeto é usado para serializar a resposta.
* Não há nenhuma negociação acontecendo. O servidor está determinando qual formato retornar.

Se o cabeçalho Aceitar contiver, `*/*`o cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como verdadeiro . <xref:Microsoft.AspNetCore.Mvc.MvcOptions>

### <a name="browsers-and-content-negotiation"></a>Navegadores e negociação de conteúdo

Ao contrário dos clientes típicos da API, os navegadores da Web fornecem `Accept` cabeçalhos. O navegador da Web especifica muitos formatos, incluindo curingas. Por padrão, quando a estrutura detectar que a solicitação está vindo de um navegador:

* O `Accept` cabeçalho é ignorado.
* O conteúdo é devolvido em JSON, a menos que configurado de outra forma.

Isso fornece uma experiência mais consistente entre os navegadores ao consumir APIs.

Para configurar um aplicativo para honrar os <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`cabeçalhos de aceitação do navegador, defina:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Configurar matérias-de-matérias

Aplicativos que precisam de suporte a formatos adicionais podem adicionar os pacotes NuGet apropriados e configurar o suporte. Há formatadores separados para entrada e saída. Os assuntos de entrada são usados pelo [Model Binding](xref:mvc/models/model-binding). Os assuntos de saída são usados para formatar respostas. Para obter informações sobre a criação de um formatter personalizado, consulte [Formatters personalizados](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Adicionar suporte ao formato XML

Os assuntos de <xref:System.Xml.Serialization.XmlSerializer> foro xml <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>implementados usando são configurados por chamada:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

O código anterior serializa `XmlSerializer`os resultados usando .

Ao usar o código anterior, os métodos do controlador `Accept` retornam o formato apropriado com base no cabeçalho da solicitação.

### <a name="configure-systemtextjson-based-formatters"></a>Configurar formatadores com base em System.Text.Json

Os recursos do formatadores com base em `System.Text.Json` podem ser configurados, usando `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

As opções de serialização de saída, por `JsonResult`ação, podem ser configuradas usando . Por exemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Adicionar suporte ao formato JSON com base em Newtonsoft.Json

Antes de ASP.NET O Core 3.0, o padrão `Newtonsoft.Json` usado JSON formatters implementado usando o pacote. No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`. O `Newtonsoft.Json` suporte para assuntos e recursos baseados está disponível instalando o pacote [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet e configurando-o em `Startup.ConfigureServices`.

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Alguns recursos podem `System.Text.Json`não funcionar bem com assuntos `Newtonsoft.Json`forobaseados e requerem uma referência aos assuntos formatters baseados em -. Continue usando `Newtonsoft.Json`os formatters baseados em formatters se o aplicativo:

* Usa `Newtonsoft.Json` atributos. Por exemplo, `[JsonProperty]` ou `[JsonIgnore]`.
* Personaliza as configurações de serialização.
* Conta com `Newtonsoft.Json` recursos que fornecem.
* Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.
* Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Os recursos `Newtonsoft.Json`para assuntos foretais baseados podem ser configurados usando: `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

As opções de serialização de saída, por `JsonResult`ação, podem ser configuradas usando . Por exemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Adicionar suporte ao formato XML

A formatação XML requer o pacote [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.

Os assuntos de <xref:System.Xml.Serialization.XmlSerializer> foro xml <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>implementados usando são configurados por chamada:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

O código anterior serializa `XmlSerializer`os resultados usando .

Ao usar o código anterior, os métodos do controlador `Accept` devem retornar o formato apropriado com base no cabeçalho da solicitação.

::: moniker-end

### <a name="specify-a-format"></a>Especificar um formato

Para restringir os formatos [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) de resposta, aplique o filtro. Como a maioria `[Produces]` [dos filtros,](xref:mvc/controllers/filters)pode ser aplicado no escopo de ação, controlador ou global:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

O [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro anterior:

* Força todas as ações dentro do controlador a retornar respostas formatadas em JSON.
* Se outros assuntos forem configurados e o cliente especificar um formato diferente, o JSON será devolvido.

Para obter mais informações, consulte [Filtros](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Caso especial para assuntos

Alguns casos especiais são implementados com formatadores internos. Por `string` padrão, os tipos de devolução são formatados como `Accept` *texto/simples* *(texto/html,* se solicitado através do cabeçalho). Esse comportamento pode ser excluído <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>removendo o . Os assuntos formatters `ConfigureServices` são removidos no método. Ações que possuem um tipo `204 No Content` de `null`retorno do objeto modelo retornam ao retornar . Esse comportamento pode ser excluído <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>removendo o . O código a seguir remove o `StringOutputFormatter` e o `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Sem `StringOutputFormatter`os formatos de retorno de `string` matéria-entrada JSON incorporados. Se a matéria-matéria JSON incorporada for removida e uma matéria xml estiver `string` disponível, os formatos de matéria-por-matéria XML são tipos de retorno. Caso contrário, `string` os `406 Not Acceptable`tipos de retorno retornam .

Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado. Por exemplo:

* O assunto json retorna uma resposta `null`com um corpo de .
* O formatter XML retorna um elemento `xsi:nil="true"` XML vazio com o conjunto de atributos.

## <a name="response-format-url-mappings"></a>Mapeamentos de URL de formato de resposta

Os clientes podem solicitar um formato específico como parte da URL, por exemplo:

* Na seqüência de consulta ou parte do caminho.
* Usando uma extensão de arquivo específica para formato, como .xml ou .json.

O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API. Por exemplo:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

A rota anterior permite que o formato solicitado seja especificado como uma extensão de arquivo opcional. O [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) atributo verifica a existência do `RouteData` valor do formato no e mapeia o formato de resposta para o formatter apropriado quando a resposta é criada.

|           Rota        |             Formatador              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    O formatador de saída padrão    |
| `/api/products/5.json` | O formatador JSON (se configurado) |
| `/api/products/5.xml`  | O formatador XML (se configurado)  |
