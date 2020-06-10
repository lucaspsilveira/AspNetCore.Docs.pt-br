---
title: Parte 3, com Scaffold Razor páginas em ASP.NET Core
author: rick-anderson
description: Parte 3 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 6195982f902c17d835d2675c1231eed347d603c2
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652805"
---
# <a name="part-3-scaffolded-razor-pages-in-aspnet-core"></a>Parte 3, com Scaffold Razor páginas em ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial examina as Razor páginas criadas por scaffolding no [tutorial anterior](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>As páginas Create, Delete, Details e Edit

Examine o Modelo de Página, *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

RazorAs páginas são derivadas de `PageModel` . Por convenção, a classe derivada de `PageModel` é chamada de `<PageName>Model`. O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página. Todas as páginas geradas por scaffolding seguem esse padrão. Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.

Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a Razor página. `OnGetAsync`ou `OnGet` é chamado para inicializar o estado da página. Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.

Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task` , nenhuma instrução de retorno é usada. Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno. Por exemplo, o método *pages/Movies/Create. cshtml. cs* `OnPostAsync` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Examine a página *páginas/filmes/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razorpode fazer a transição de HTML para C# ou para uma Razor marcação específica. Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica, caso contrário, ele faz a transição para o C#.

### <a name="the-page-directive"></a>A diretiva @page

A `@page` Razor diretiva torna o arquivo uma ação MVC, o que significa que ele pode lidar com solicitações. `@page`deve ser a primeira Razor diretiva em uma página. `@page`é um exemplo de transição para Razor marcação específica. Consulte a [ Razor sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.

Examine a expressão lambda usada no auxiliar HTML a seguir:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

O auxiliar HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição. A expressão lambda é inspecionada em vez de avaliada. Isso significa que não há nenhuma violação de acesso quando `model` , `model.Movie` ou `model.Movie[0]` está `null` ou vazio. Quando a expressão lambda é avaliada (por exemplo, com `@Html.DisplayFor(modelItem => item.Title)`), os valores de propriedade do modelo são avaliados.

<a name="md"></a>

### <a name="the-model-directive"></a>A diretiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

A `@model` diretiva especifica o tipo do modelo passado para a Razor página. No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a Razor página. O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.

### <a name="the-layout-page"></a>A página de layout

Selecione os links de menu (**RazorPagesMovie**, **Página Inicial** e **Privacidade**). Cada página mostra o mesmo layout de menu. O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*. Abra o arquivo *Pages/Shared/_Layout.cshtml*.

Os modelos de [layout](xref:mvc/views/layout) permitem que o layout do contêiner HTML seja:

* Especificado em um único lugar.
* Aplicado a várias páginas no site.

Localize a linha `@RenderBody()`. `RenderBody` é um espaço reservado em que todas as exibições específicas da página são mostradas, *encapsuladas* na página de layout. Por exemplo, selecione o link **Privacidade** e a exibição *Pages/Privacy.cshtml* será renderizada dentro do método `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData e layout

Considere a seguinte marcação do arquivo *Pages/Movies/Index.cshtml* file:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

A marcação realçada anterior é um exemplo de Razor transição para C#. Os caracteres `{` e `}` circunscrevem um bloco de código C#.

A `PageModel` classe base contém uma `ViewData` Propriedade Dictionary que pode ser usada para passar dados para uma exibição. Adicione objetos ao dicionário `ViewData` usando um padrão de chave/valor. No exemplo anterior, a propriedade `"Title"` é adicionada ao dicionário `ViewData`.

A propriedade `"Title"` é usada no arquivo *Pages/Shared/_Layout.cshtml*. A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

A linha `@*Markup removed for brevity.*@` é um Razor comentário. Ao contrário dos comentários HTML ( `<!-- -->` ), Razor os comentários não são enviados ao cliente.

### <a name="update-the-layout"></a>Atualizar o layout

Altere o elemento `<title>` no arquivo *Pages/Shared/_Layout.cshtml* para exibir **Movie**, em vez de **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Substitua o elemento anterior pela marcação a seguir:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro). Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). O `asp-page="/Movies/Index"` atributo e o valor da marca auxiliar cria um link para a `/Movies/Index` Razor página. O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link. Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.

Salve suas alterações e teste o aplicativo clicando no link **RpMovie**. Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.

Teste os outros links (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**). Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.

> [!NOTE]
> Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve tomar medidas para globalizar o aplicativo. Confira [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.

A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os Razor arquivos na pasta *pages* . Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.

### <a name="the-create-page-model"></a>O modelo Criar página

Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

O método `OnGet` inicializa qualquer estado necessário para a página. A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado. Apresentamos um exemplo de inicialização de estado `OnGet` posteriormente no tutorial. O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.

A propriedade `Movie` usa o atributo `[BindProperty]` para aceitar o [model binding](xref:mvc/models/model-binding). Quando o formulário Criar posta os valores de formulário, o runtime do ASP.NET Core associa os valores postados ao modelo `Movie`.

O método `OnPostAsync` é executado quando a página posta dados de formulário:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados. A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado. Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data. A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.

Se não há nenhum erro de modelo, os dados são salvos e o navegador é redirecionado à página Índice.

### <a name="the-create-razor-page"></a>A Razor página criar

Examine o arquivo de página *páginas/filmes/Create. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

O Visual Studio exibe as marcas a seguir em uma fonte em negrito diferente usada em auxiliares de marcações:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Exibição de VS17 da página Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Os auxiliares de marcações a seguir são exibidos na marcação anterior:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

O Visual Studio exibe as marcas a seguir em uma fonte em negrito diferente usada em auxiliares de marcações:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper). O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).

O mecanismo scaffolding cria a Razor marcação para cada campo no modelo (exceto a ID) semelhante ao seguinte:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação. A validação será abordada em mais detalhes posteriormente nesta série.

A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `for` atributo para a `Title` propriedade.

O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.

Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: adicionando um modelo](xref:tutorials/razor-pages/model) 
>  [Próximo: banco de dados](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial examina as Razor páginas criadas por scaffolding no [tutorial anterior](xref:tutorials/razor-pages/model).

[Exiba ou baixe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) a amostra.

## <a name="the-create-delete-details-and-edit-pages"></a>As páginas Create, Delete, Details e Edit

Examine o Modelo de Página, *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

RazorAs páginas são derivadas de `PageModel` . Por convenção, a classe derivada de `PageModel` é chamada de `<PageName>Model`. O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página. Todas as páginas geradas por scaffolding seguem esse padrão. Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.

Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a Razor página. `OnGetAsync`ou `OnGet` é chamado em uma Razor página para inicializar o estado da página. Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.

Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task`, então nenhum método de retorno é usado. Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno. Por exemplo, o método *pages/Movies/Create. cshtml. cs* `OnPostAsync` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>Examine a página *páginas/filmes/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razorpode fazer a transição de HTML para C# ou para uma Razor marcação específica. Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica, caso contrário, ele faz a transição para o C#.

A `@page` Razor diretiva transforma o arquivo em uma ação MVC, o que significa que ele pode lidar com solicitações. `@page`deve ser a primeira Razor diretiva em uma página. `@page`é um exemplo de transição para Razor marcação específica. Consulte a [ Razor sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.

Examine a expressão lambda usada no auxiliar HTML a seguir:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

O auxiliar HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição. A expressão lambda é inspecionada em vez de avaliada. Isso significa que não há nenhuma violação de acesso quando `model`, `model.Movie` ou `model.Movie[0]` são `null` ou vazios. Quando a expressão lambda é avaliada (por exemplo, com `@Html.DisplayFor(modelItem => item.Title)`), os valores de propriedade do modelo são avaliados.

<a name="md"></a>

### <a name="the-model-directive"></a>A diretiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

A `@model` diretiva especifica o tipo do modelo passado para a Razor página. No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a Razor página. O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.

### <a name="the-layout-page"></a>A página de layout

Selecione os links de menu (**RazorPagesMovie**, **Página Inicial** e **Privacidade**). Cada página mostra o mesmo layout de menu. O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*. Abra o arquivo *Pages/Shared/_Layout.cshtml*.

Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site. Localize a linha `@RenderBody()`. `RenderBody` é um espaço reservado em que todas as visualizações específicas da página criadas são mostradas, *encapsuladas* na página de layout. Por exemplo, se você selecionar o link **Privacidade**, a exibição **Pages/Privacy.cshtml** será renderizada dentro do método `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData e layout

Considere o seguinte código do arquivo *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

O código realçado anterior é um exemplo de Razor transição para o C#. Os caracteres `{` e `}` circunscrevem um bloco de código C#.

A classe base `PageModel` tem uma propriedade de dicionário `ViewData` que pode ser usada para adicionar os dados que você deseja passar para uma exibição. Você adiciona objetos ao dicionário `ViewData` usando um padrão de chave/valor. No exemplo anterior, a propriedade "Title" é adicionada ao dicionário `ViewData`.

A propriedade "Título" é usada no arquivo *Pages/_Layout.cshtml*. A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

A linha `@*Markup removed for brevity.*@` é um Razor comentário que não aparece no arquivo de layout. Ao contrário dos comentários HTML ( `<!-- -->` ), Razor os comentários não são enviados ao cliente.

### <a name="update-the-layout"></a>Atualizar o layout

Altere o elemento `<title>` no arquivo *Pages/Shared/_Layout.cshtml* para exibir **Movie**, em vez de **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Substitua o elemento anterior pela marcação a seguir.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro). Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). O `asp-page="/Movies/Index"` atributo e o valor da marca auxiliar cria um link para a `/Movies/Index` Razor página. O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link. Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.

Salve suas alterações e teste o aplicativo clicando no link **RpMovie**. Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.

Teste os outros links (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**). Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.

> [!NOTE]
> Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve tomar medidas para globalizar o aplicativo. Veja [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.

A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os Razor arquivos na pasta *pages* . Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.

### <a name="the-create-page-model"></a>O modelo Criar página

Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

O método `OnGet` inicializa qualquer estado necessário para a página. A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado. Mais adiante no tutorial, você verá o estado de inicialização do método `OnGet`. O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.

A propriedade `Movie` usa o atributo `[BindProperty]` para aceitar o [model binding](xref:mvc/models/model-binding). Quando o formulário Criar posta os valores de formulário, o runtime do ASP.NET Core associa os valores postados ao modelo `Movie`.

O método `OnPostAsync` é executado quando a página posta dados de formulário:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados. A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado. Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data. A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.

Se não há nenhum erro de modelo, os dados são salvos e o navegador é redirecionado à página Índice.

### <a name="the-create-razor-page"></a>A Razor página criar

Examine o arquivo de página *páginas/filmes/Create. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

O Visual Studio exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas:

![Exibição de VS17 da página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

O Visual Studio para Mac exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas.

---

O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper). O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).

O mecanismo scaffolding cria a Razor marcação para cada campo no modelo (exceto a ID) semelhante ao seguinte:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação. A validação será abordada em mais detalhes posteriormente nesta série.

A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `for` atributo para a `Title` propriedade.

O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Anterior: adicionando um modelo](xref:tutorials/razor-pages/model) 
>  [Próximo: banco de dados](xref:tutorials/razor-pages/sql)

::: moniker-end
