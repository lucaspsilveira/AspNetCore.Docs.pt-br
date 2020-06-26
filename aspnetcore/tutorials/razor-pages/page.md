---
title: Parte 3, com Scaffold Razor páginas em ASP.NET Core
author: rick-anderson
description: Parte 3 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 898f3be045755a0ee14c738b6ffc104408ded7b6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407766"
---
# <a name="part-3-scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="bb262-103">Parte 3, com Scaffold Razor páginas em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bb262-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb262-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb262-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bb262-105">Este tutorial examina as Razor páginas criadas por scaffolding no [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="bb262-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="bb262-106">As páginas Create, Delete, Details e Edit</span><span class="sxs-lookup"><span data-stu-id="bb262-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bb262-107">Examine o Modelo de Página, *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb262-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor<span data-ttu-id="bb262-108">As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="bb262-108"> Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bb262-109">Por convenção, a classe derivada de `PageModel` é chamada de `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="bb262-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="bb262-110">O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página.</span><span class="sxs-lookup"><span data-stu-id="bb262-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="bb262-111">Todas as páginas geradas por scaffolding seguem esse padrão.</span><span class="sxs-lookup"><span data-stu-id="bb262-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="bb262-112">Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bb262-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bb262-113">Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bb262-114">`OnGetAsync`ou `OnGet` é chamado para inicializar o estado da página.</span><span class="sxs-lookup"><span data-stu-id="bb262-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="bb262-115">Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.</span><span class="sxs-lookup"><span data-stu-id="bb262-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bb262-116">Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task` , nenhuma instrução de retorno é usada.</span><span class="sxs-lookup"><span data-stu-id="bb262-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="bb262-117">Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno.</span><span class="sxs-lookup"><span data-stu-id="bb262-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bb262-118">Por exemplo, o método *pages/Movies/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="bb262-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="bb262-119">Examine a página *páginas/filmes/index. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bb262-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor<span data-ttu-id="bb262-120">pode fazer a transição de HTML para C# ou para uma Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="bb262-120"> can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bb262-121">Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica, caso contrário, ele faz a transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="bb262-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="bb262-122">A diretiva @page</span><span class="sxs-lookup"><span data-stu-id="bb262-122">The @page directive</span></span>

<span data-ttu-id="bb262-123">A `@page` Razor diretiva torna o arquivo uma ação MVC, o que significa que ele pode lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="bb262-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bb262-124">`@page`deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="bb262-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bb262-125">`@page`é um exemplo de transição para Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="bb262-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bb262-126">Consulte a [ Razor sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bb262-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="bb262-127">Examine a expressão lambda usada no auxiliar HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="bb262-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bb262-128">O auxiliar HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="bb262-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bb262-129">A expressão lambda é inspecionada em vez de avaliada.</span><span class="sxs-lookup"><span data-stu-id="bb262-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bb262-130">Isso significa que não há nenhuma violação de acesso quando `model` , `model.Movie` ou `model.Movie[0]` está `null` ou vazio.</span><span class="sxs-lookup"><span data-stu-id="bb262-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="bb262-131">Quando a expressão lambda é avaliada (por exemplo, com `@Html.DisplayFor(modelItem => item.Title)`), os valores de propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="bb262-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bb262-132">A diretiva @model</span><span class="sxs-lookup"><span data-stu-id="bb262-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bb262-133">A `@model` diretiva especifica o tipo do modelo passado para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bb262-134">No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bb262-135">O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.</span><span class="sxs-lookup"><span data-stu-id="bb262-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="bb262-136">A página de layout</span><span class="sxs-lookup"><span data-stu-id="bb262-136">The layout page</span></span>

<span data-ttu-id="bb262-137">Selecione os links de menu (**RazorPagesMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="bb262-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="bb262-138">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="bb262-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="bb262-139">O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bb262-140">Abra o arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bb262-141">Os modelos de [layout](xref:mvc/views/layout) permitem que o layout do contêiner HTML seja:</span><span class="sxs-lookup"><span data-stu-id="bb262-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="bb262-142">Especificado em um único lugar.</span><span class="sxs-lookup"><span data-stu-id="bb262-142">Specified in one place.</span></span>
* <span data-ttu-id="bb262-143">Aplicado a várias páginas no site.</span><span class="sxs-lookup"><span data-stu-id="bb262-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="bb262-144">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="bb262-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bb262-145">`RenderBody` é um espaço reservado em que todas as exibições específicas da página são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="bb262-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bb262-146">Por exemplo, selecione o link **Privacidade** e a exibição *Pages/Privacy.cshtml* será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="bb262-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bb262-147">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="bb262-147">ViewData and layout</span></span>

<span data-ttu-id="bb262-148">Considere a seguinte marcação do arquivo *Pages/Movies/Index.cshtml* file:</span><span class="sxs-lookup"><span data-stu-id="bb262-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bb262-149">A marcação realçada anterior é um exemplo de Razor transição para C#.</span><span class="sxs-lookup"><span data-stu-id="bb262-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bb262-150">Os caracteres `{` e `}` circunscrevem um bloco de código C#.</span><span class="sxs-lookup"><span data-stu-id="bb262-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bb262-151">A `PageModel` classe base contém uma `ViewData` Propriedade Dictionary que pode ser usada para passar dados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="bb262-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="bb262-152">Adicione objetos ao dicionário `ViewData` usando um padrão de chave/valor.</span><span class="sxs-lookup"><span data-stu-id="bb262-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="bb262-153">No exemplo anterior, a propriedade `"Title"` é adicionada ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="bb262-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bb262-154">A propriedade `"Title"` é usada no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bb262-155">A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="bb262-156">A linha `@*Markup removed for brevity.*@` é um Razor comentário.</span><span class="sxs-lookup"><span data-stu-id="bb262-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="bb262-157">Ao contrário dos comentários HTML ( `<!-- -->` ), Razor os comentários não são enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="bb262-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bb262-158">Atualizar o layout</span><span class="sxs-lookup"><span data-stu-id="bb262-158">Update the layout</span></span>

<span data-ttu-id="bb262-159">Altere o elemento `<title>` no arquivo *Pages/Shared/_Layout.cshtml* para exibir **Movie**, em vez de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="bb262-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="bb262-160">Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="bb262-161">Substitua o elemento anterior pela marcação a seguir:</span><span class="sxs-lookup"><span data-stu-id="bb262-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="bb262-162">O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bb262-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bb262-163">Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bb262-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bb262-164">O `asp-page="/Movies/Index"` atributo e o valor da marca auxiliar cria um link para a `/Movies/Index` Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bb262-165">O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link.</span><span class="sxs-lookup"><span data-stu-id="bb262-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bb262-166">Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bb262-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="bb262-167">Salve suas alterações e teste o aplicativo clicando no link **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="bb262-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="bb262-168">Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.</span><span class="sxs-lookup"><span data-stu-id="bb262-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="bb262-169">Teste os outros links (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**).</span><span class="sxs-lookup"><span data-stu-id="bb262-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="bb262-170">Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.</span><span class="sxs-lookup"><span data-stu-id="bb262-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bb262-171">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="bb262-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bb262-172">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve tomar medidas para globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb262-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="bb262-173">Confira [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.</span><span class="sxs-lookup"><span data-stu-id="bb262-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bb262-174">A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bb262-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bb262-175">A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os Razor arquivos na pasta *pages* .</span><span class="sxs-lookup"><span data-stu-id="bb262-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bb262-176">Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bb262-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="bb262-177">O modelo Criar página</span><span class="sxs-lookup"><span data-stu-id="bb262-177">The Create page model</span></span>

<span data-ttu-id="bb262-178">Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb262-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bb262-179">O método `OnGet` inicializa qualquer estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="bb262-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bb262-180">A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado.</span><span class="sxs-lookup"><span data-stu-id="bb262-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bb262-181">Apresentamos um exemplo de inicialização de estado `OnGet` posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="bb262-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="bb262-182">O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bb262-183">A propriedade `Movie` usa o atributo `[BindProperty]` para aceitar o [model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="bb262-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bb262-184">Quando o formulário Criar posta os valores de formulário, o runtime do ASP.NET Core associa os valores postados ao modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="bb262-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bb262-185">O método `OnPostAsync` é executado quando a página posta dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="bb262-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bb262-186">Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="bb262-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bb262-187">A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado.</span><span class="sxs-lookup"><span data-stu-id="bb262-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bb262-188">Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data.</span><span class="sxs-lookup"><span data-stu-id="bb262-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bb262-189">A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="bb262-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bb262-190">Se não há nenhum erro de modelo, os dados são salvos e o navegador é redirecionado à página Índice.</span><span class="sxs-lookup"><span data-stu-id="bb262-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="bb262-191">A Razor página criar</span><span class="sxs-lookup"><span data-stu-id="bb262-191">The Create Razor Page</span></span>

<span data-ttu-id="bb262-192">Examine o arquivo de página *páginas/filmes/Create. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bb262-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bb262-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb262-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bb262-194">O Visual Studio exibe as marcas a seguir em uma fonte em negrito diferente usada em auxiliares de marcações:</span><span class="sxs-lookup"><span data-stu-id="bb262-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Exibição de VS17 da página Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bb262-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bb262-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bb262-197">Os auxiliares de marcações a seguir são exibidos na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="bb262-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bb262-198">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bb262-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bb262-199">O Visual Studio exibe as marcas a seguir em uma fonte em negrito diferente usada em auxiliares de marcações:</span><span class="sxs-lookup"><span data-stu-id="bb262-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="bb262-200">O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bb262-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bb262-201">O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="bb262-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bb262-202">O mecanismo scaffolding cria a Razor marcação para cada campo no modelo (exceto a ID) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="bb262-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bb262-203">Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação.</span><span class="sxs-lookup"><span data-stu-id="bb262-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bb262-204">A validação será abordada em mais detalhes posteriormente nesta série.</span><span class="sxs-lookup"><span data-stu-id="bb262-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bb262-205">A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `for` atributo para a `Title` propriedade.</span><span class="sxs-lookup"><span data-stu-id="bb262-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="bb262-206">O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="bb262-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="bb262-207">Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bb262-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb262-208">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bb262-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bb262-209">[Anterior: adicionando um modelo](xref:tutorials/razor-pages/model) 
>  [Próximo: banco de dados](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bb262-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bb262-210">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb262-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bb262-211">Este tutorial examina as Razor páginas criadas por scaffolding no [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="bb262-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="bb262-212">[Exiba ou baixe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) a amostra.</span><span class="sxs-lookup"><span data-stu-id="bb262-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="bb262-213">As páginas Create, Delete, Details e Edit</span><span class="sxs-lookup"><span data-stu-id="bb262-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bb262-214">Examine o Modelo de Página, *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb262-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor<span data-ttu-id="bb262-215">As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="bb262-215"> Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bb262-216">Por convenção, a classe derivada de `PageModel` é chamada de `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="bb262-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="bb262-217">O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página.</span><span class="sxs-lookup"><span data-stu-id="bb262-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="bb262-218">Todas as páginas geradas por scaffolding seguem esse padrão.</span><span class="sxs-lookup"><span data-stu-id="bb262-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="bb262-219">Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bb262-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bb262-220">Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bb262-221">`OnGetAsync`ou `OnGet` é chamado em uma Razor página para inicializar o estado da página.</span><span class="sxs-lookup"><span data-stu-id="bb262-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="bb262-222">Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.</span><span class="sxs-lookup"><span data-stu-id="bb262-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bb262-223">Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task`, então nenhum método de retorno é usado.</span><span class="sxs-lookup"><span data-stu-id="bb262-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="bb262-224">Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno.</span><span class="sxs-lookup"><span data-stu-id="bb262-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bb262-225">Por exemplo, o método *pages/Movies/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="bb262-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="bb262-226">Examine a página *páginas/filmes/index. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bb262-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor<span data-ttu-id="bb262-227">pode fazer a transição de HTML para C# ou para uma Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="bb262-227"> can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bb262-228">Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica, caso contrário, ele faz a transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="bb262-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="bb262-229">A `@page` Razor diretiva transforma o arquivo em uma ação MVC, o que significa que ele pode lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="bb262-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bb262-230">`@page`deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="bb262-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bb262-231">`@page`é um exemplo de transição para Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="bb262-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bb262-232">Consulte a [ Razor sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bb262-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="bb262-233">Examine a expressão lambda usada no auxiliar HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="bb262-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bb262-234">O auxiliar HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="bb262-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bb262-235">A expressão lambda é inspecionada em vez de avaliada.</span><span class="sxs-lookup"><span data-stu-id="bb262-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bb262-236">Isso significa que não há nenhuma violação de acesso quando `model`, `model.Movie` ou `model.Movie[0]` são `null` ou vazios.</span><span class="sxs-lookup"><span data-stu-id="bb262-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="bb262-237">Quando a expressão lambda é avaliada (por exemplo, com `@Html.DisplayFor(modelItem => item.Title)`), os valores de propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="bb262-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bb262-238">A diretiva @model</span><span class="sxs-lookup"><span data-stu-id="bb262-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bb262-239">A `@model` diretiva especifica o tipo do modelo passado para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bb262-240">No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bb262-241">O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.</span><span class="sxs-lookup"><span data-stu-id="bb262-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="bb262-242">A página de layout</span><span class="sxs-lookup"><span data-stu-id="bb262-242">The layout page</span></span>

<span data-ttu-id="bb262-243">Selecione os links de menu (**RazorPagesMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="bb262-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="bb262-244">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="bb262-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="bb262-245">O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bb262-246">Abra o arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bb262-247">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="bb262-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="bb262-248">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="bb262-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bb262-249">`RenderBody` é um espaço reservado em que todas as visualizações específicas da página criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="bb262-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bb262-250">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Pages/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="bb262-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bb262-251">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="bb262-251">ViewData and layout</span></span>

<span data-ttu-id="bb262-252">Considere o seguinte código do arquivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bb262-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bb262-253">O código realçado anterior é um exemplo de Razor transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="bb262-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bb262-254">Os caracteres `{` e `}` circunscrevem um bloco de código C#.</span><span class="sxs-lookup"><span data-stu-id="bb262-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bb262-255">A classe base `PageModel` tem uma propriedade de dicionário `ViewData` que pode ser usada para adicionar os dados que você deseja passar para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="bb262-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="bb262-256">Você adiciona objetos ao dicionário `ViewData` usando um padrão de chave/valor.</span><span class="sxs-lookup"><span data-stu-id="bb262-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="bb262-257">No exemplo anterior, a propriedade "Title" é adicionada ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="bb262-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bb262-258">A propriedade "Título" é usada no arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bb262-259">A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="bb262-260">A linha `@*Markup removed for brevity.*@` é um Razor comentário que não aparece no arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="bb262-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="bb262-261">Ao contrário dos comentários HTML ( `<!-- -->` ), Razor os comentários não são enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="bb262-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bb262-262">Atualizar o layout</span><span class="sxs-lookup"><span data-stu-id="bb262-262">Update the layout</span></span>

<span data-ttu-id="bb262-263">Altere o elemento `<title>` no arquivo *Pages/Shared/_Layout.cshtml* para exibir **Movie**, em vez de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="bb262-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="bb262-264">Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="bb262-265">Substitua o elemento anterior pela marcação a seguir.</span><span class="sxs-lookup"><span data-stu-id="bb262-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="bb262-266">O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bb262-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bb262-267">Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bb262-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bb262-268">O `asp-page="/Movies/Index"` atributo e o valor da marca auxiliar cria um link para a `/Movies/Index` Razor página.</span><span class="sxs-lookup"><span data-stu-id="bb262-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bb262-269">O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link.</span><span class="sxs-lookup"><span data-stu-id="bb262-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bb262-270">Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bb262-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="bb262-271">Salve suas alterações e teste o aplicativo clicando no link **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="bb262-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="bb262-272">Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.</span><span class="sxs-lookup"><span data-stu-id="bb262-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="bb262-273">Teste os outros links (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**).</span><span class="sxs-lookup"><span data-stu-id="bb262-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="bb262-274">Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.</span><span class="sxs-lookup"><span data-stu-id="bb262-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bb262-275">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="bb262-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bb262-276">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve tomar medidas para globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bb262-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="bb262-277">Veja [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.</span><span class="sxs-lookup"><span data-stu-id="bb262-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bb262-278">A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bb262-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bb262-279">A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os Razor arquivos na pasta *pages* .</span><span class="sxs-lookup"><span data-stu-id="bb262-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bb262-280">Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bb262-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="bb262-281">O modelo Criar página</span><span class="sxs-lookup"><span data-stu-id="bb262-281">The Create page model</span></span>

<span data-ttu-id="bb262-282">Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb262-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bb262-283">O método `OnGet` inicializa qualquer estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="bb262-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bb262-284">A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado.</span><span class="sxs-lookup"><span data-stu-id="bb262-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bb262-285">Mais adiante no tutorial, você verá o estado de inicialização do método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="bb262-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="bb262-286">O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bb262-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bb262-287">A propriedade `Movie` usa o atributo `[BindProperty]` para aceitar o [model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="bb262-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bb262-288">Quando o formulário Criar posta os valores de formulário, o runtime do ASP.NET Core associa os valores postados ao modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="bb262-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bb262-289">O método `OnPostAsync` é executado quando a página posta dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="bb262-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bb262-290">Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="bb262-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bb262-291">A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado.</span><span class="sxs-lookup"><span data-stu-id="bb262-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bb262-292">Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data.</span><span class="sxs-lookup"><span data-stu-id="bb262-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bb262-293">A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="bb262-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bb262-294">Se não há nenhum erro de modelo, os dados são salvos e o navegador é redirecionado à página Índice.</span><span class="sxs-lookup"><span data-stu-id="bb262-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="bb262-295">A Razor página criar</span><span class="sxs-lookup"><span data-stu-id="bb262-295">The Create Razor Page</span></span>

<span data-ttu-id="bb262-296">Examine o arquivo de página *páginas/filmes/Create. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bb262-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bb262-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb262-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bb262-298">O Visual Studio exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas:</span><span class="sxs-lookup"><span data-stu-id="bb262-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Exibição de VS17 da página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bb262-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bb262-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bb262-301">Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bb262-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bb262-302">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bb262-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bb262-303">O Visual Studio para Mac exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas.</span><span class="sxs-lookup"><span data-stu-id="bb262-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="bb262-304">O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bb262-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bb262-305">O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="bb262-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bb262-306">O mecanismo scaffolding cria a Razor marcação para cada campo no modelo (exceto a ID) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="bb262-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bb262-307">Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação.</span><span class="sxs-lookup"><span data-stu-id="bb262-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bb262-308">A validação será abordada em mais detalhes posteriormente nesta série.</span><span class="sxs-lookup"><span data-stu-id="bb262-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bb262-309">A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `for` atributo para a `Title` propriedade.</span><span class="sxs-lookup"><span data-stu-id="bb262-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="bb262-310">O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="bb262-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb262-311">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bb262-311">Additional resources</span></span>

* [<span data-ttu-id="bb262-312">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="bb262-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="bb262-313">[Anterior: adicionando um modelo](xref:tutorials/razor-pages/model) 
>  [Próximo: banco de dados](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bb262-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
