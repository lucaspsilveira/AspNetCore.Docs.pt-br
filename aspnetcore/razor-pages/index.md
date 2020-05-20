---
title: Introdução às Razor páginas no ASP.NET Core
author: Rick-Anderson
description: Saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 6939d285838a6dd971f530c1d65d73273b5b14e7
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424557"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="4bbab-103">Introdução a Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4bbab-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bbab-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="4bbab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="4bbab-105">Razor Pages pode tornar os cenários voltados para a página de codificação mais fáceis e produtivos do que usar controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="4bbab-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="4bbab-106">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="4bbab-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="4bbab-107">Este documento proporciona uma introdução a páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="4bbab-108">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="4bbab-109">Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="4bbab-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="4bbab-110">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="4bbab-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4bbab-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4bbab-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bbab-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bbab-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bbab-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bbab-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bbab-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4bbab-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="4bbab-115">Criar um projeto do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4bbab-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bbab-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bbab-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bbab-117">Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4bbab-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bbab-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bbab-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4bbab-119">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bbab-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4bbab-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4bbab-121">Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4bbab-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="4bbab-122">Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="4bbab-122">Razor Pages</span></span>

<span data-ttu-id="4bbab-123">O Páginas do Razor está habilitado em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="4bbab-124">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="4bbab-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="4bbab-125">O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="4bbab-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="4bbab-126">O que o torna diferente é a [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="4bbab-127">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="4bbab-128">`@page` deve ser a primeira diretiva do Razor em uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="4bbab-129">`@page`afeta o comportamento de outras construções do [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="4bbab-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="4bbab-130">Razor Pages nomes de arquivo têm um sufixo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="4bbab-131">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="4bbab-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="4bbab-132">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="4bbab-133">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="4bbab-134">Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="4bbab-135">Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="4bbab-136">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="4bbab-137">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="4bbab-138">A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="4bbab-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="4bbab-139">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="4bbab-139">File name and path</span></span>               | <span data-ttu-id="4bbab-140">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="4bbab-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="4bbab-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="4bbab-142">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="4bbab-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="4bbab-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="4bbab-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="4bbab-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="4bbab-146">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="4bbab-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="4bbab-147">Observações:</span><span class="sxs-lookup"><span data-stu-id="4bbab-147">Notes:</span></span>

* <span data-ttu-id="4bbab-148">O runtime procura arquivos de Páginas do Razor na pasta *Pages* por padrão.</span><span class="sxs-lookup"><span data-stu-id="4bbab-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="4bbab-149">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="4bbab-150">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="4bbab-150">Write a basic form</span></span>

<span data-ttu-id="4bbab-151">Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="4bbab-152">[Model binding](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="4bbab-153">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="4bbab-154">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="4bbab-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="4bbab-155">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="4bbab-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="4bbab-156">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="4bbab-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="4bbab-157">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="4bbab-158">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="4bbab-159">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="4bbab-160">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="4bbab-161">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="4bbab-162">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="4bbab-162">This separation allows:</span></span>

* <span data-ttu-id="4bbab-163">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4bbab-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="4bbab-164">Testes de unidade</span><span class="sxs-lookup"><span data-stu-id="4bbab-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="4bbab-165">A página tem um  *método de manipulador*`OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="4bbab-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="4bbab-166">Métodos de manipulador para qualquer verbo HTTP podem ser adicionados.</span><span class="sxs-lookup"><span data-stu-id="4bbab-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="4bbab-167">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="4bbab-167">The most common handlers are:</span></span>

* <span data-ttu-id="4bbab-168">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="4bbab-169">No código anterior, o `OnGet` método exibe a página Razor *CREATEMODEL. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="4bbab-170">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="4bbab-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="4bbab-171">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="4bbab-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="4bbab-172">O código anterior é comum para as Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="4bbab-173">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="4bbab-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="4bbab-174">O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="4bbab-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="4bbab-175">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation)e resultados de ação, funcionam da mesma forma com controladores e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4bbab-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="4bbab-176">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="4bbab-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="4bbab-177">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="4bbab-178">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-178">Check for validation errors.</span></span>

* <span data-ttu-id="4bbab-179">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="4bbab-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="4bbab-180">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="4bbab-181">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="4bbab-182">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="4bbab-183">O HTML renderizado de *pages/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="4bbab-184">No código anterior, postando o formulário:</span><span class="sxs-lookup"><span data-stu-id="4bbab-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="4bbab-185">Com dados válidos:</span><span class="sxs-lookup"><span data-stu-id="4bbab-185">With valid data:</span></span>

  * <span data-ttu-id="4bbab-186">O `OnPostAsync` método de manipulador chama o <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> método auxiliar.</span><span class="sxs-lookup"><span data-stu-id="4bbab-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="4bbab-187">`RedirectToPage` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="4bbab-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="4bbab-189">É um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-189">Is an action result.</span></span>
    * <span data-ttu-id="4bbab-190">É semelhante a `RedirectToAction` ou `RedirectToRoute` (usado em controladores e exibições).</span><span class="sxs-lookup"><span data-stu-id="4bbab-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="4bbab-191">É personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="4bbab-191">Is customized for pages.</span></span> <span data-ttu-id="4bbab-192">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="4bbab-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="4bbab-193">`RedirectToPage`é detalhado na seção [geração de URL para páginas](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="4bbab-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="4bbab-194">Com erros de validação que são passados para o servidor:</span><span class="sxs-lookup"><span data-stu-id="4bbab-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="4bbab-195">O `OnPostAsync` método de manipulador chama o <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> método auxiliar.</span><span class="sxs-lookup"><span data-stu-id="4bbab-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="4bbab-196">`Page` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="4bbab-197">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="4bbab-198">`PageResult`é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="4bbab-199">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="4bbab-200">No exemplo anterior, a postagem do formulário sem nenhum resultado em [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) retorna false.</span><span class="sxs-lookup"><span data-stu-id="4bbab-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="4bbab-201">Neste exemplo, nenhum erro de validação é exibido no cliente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="4bbab-202">A entrega do erro de validação será abordada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="4bbab-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="4bbab-203">Com erros de validação detectados pela validação no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="4bbab-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="4bbab-204">Os dados **não** são postados no servidor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="4bbab-205">A validação no lado do cliente é explicada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="4bbab-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="4bbab-206">A `Customer` propriedade usa o [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atributo para aceitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="4bbab-207">`[BindProperty]`**não** deve ser usado em modelos que contêm propriedades que não devem ser alteradas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="4bbab-208">Para obter mais informações, consulte [superpostando](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="4bbab-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="4bbab-209">O Razor Pages, por padrão, associa propriedades somente com verbos não `GET`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="4bbab-210">A associação a Propriedades remove a necessidade de escrever código para converter dados HTTP no tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="4bbab-211">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="4bbab-212">Examinando o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4bbab-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="4bbab-213">No código anterior, o [auxiliar de marca de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` associa o `<input>` elemento HTML à `Customer.Name` expressão do modelo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="4bbab-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)torna os auxiliares de marca disponíveis.</span><span class="sxs-lookup"><span data-stu-id="4bbab-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="4bbab-215">Home page</span><span class="sxs-lookup"><span data-stu-id="4bbab-215">The home page</span></span>

<span data-ttu-id="4bbab-216">*Index. cshtml* é o Home Page:</span><span class="sxs-lookup"><span data-stu-id="4bbab-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="4bbab-217">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="4bbab-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4bbab-218">O arquivo *index. cshtml* contém a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="4bbab-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="4bbab-219">O `<a /a>` [auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o `asp-route-{value}` atributo para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="4bbab-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="4bbab-220">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="4bbab-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="4bbab-221">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="4bbab-222">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="4bbab-223">O arquivo *index. cshtml* contém marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="4bbab-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="4bbab-224">O HTML renderizado:</span><span class="sxs-lookup"><span data-stu-id="4bbab-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="4bbab-225">Quando o botão de exclusão é renderizado em HTML, seu [formsaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="4bbab-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="4bbab-226">A ID de contato do cliente, especificada pelo `asp-route-id` atributo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="4bbab-227">O `handler` , especificado pelo `asp-page-handler` atributo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="4bbab-228">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="4bbab-229">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="4bbab-230">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="4bbab-231">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="4bbab-232">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="4bbab-233">Obtém o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="4bbab-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="4bbab-234">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="4bbab-235">Se o contato do cliente for encontrado, ele é removido e o banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="4bbab-236">Chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="4bbab-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="4bbab-237">O arquivo Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="4bbab-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="4bbab-238">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="4bbab-239">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="4bbab-240">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="4bbab-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="4bbab-241">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="4bbab-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="4bbab-242">O arquivo *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="4bbab-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="4bbab-243">Validação</span><span class="sxs-lookup"><span data-stu-id="4bbab-243">Validation</span></span>

<span data-ttu-id="4bbab-244">Regras de validação:</span><span class="sxs-lookup"><span data-stu-id="4bbab-244">Validation rules:</span></span>

* <span data-ttu-id="4bbab-245">São especificadas declarativamente na classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="4bbab-246">São impostos em todos os lugares no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="4bbab-247">O <xref:System.ComponentModel.DataAnnotations> namespace fornece um conjunto de atributos de validação internos que são aplicados declarativamente a uma classe ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="4bbab-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="4bbab-248">Annotations também contém atributos de formatação como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) essa ajuda com a formatação e não fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="4bbab-249">Considere o `Customer` modelo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="4bbab-250">Usando o seguinte arquivo de exibição *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4bbab-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="4bbab-251">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="4bbab-251">The preceding code:</span></span>

* <span data-ttu-id="4bbab-252">Inclui scripts de validação jQuery e jQuery.</span><span class="sxs-lookup"><span data-stu-id="4bbab-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="4bbab-253">Usa os `<div />` `<span />` [auxiliares de marcação](xref:mvc/views/tag-helpers/intro) e para habilitar:</span><span class="sxs-lookup"><span data-stu-id="4bbab-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="4bbab-254">Validação no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-254">Client-side validation.</span></span>
  * <span data-ttu-id="4bbab-255">Renderização de erro de validação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-255">Validation error rendering.</span></span>

* <span data-ttu-id="4bbab-256">Gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="4bbab-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="4bbab-257">A postagem do valor de criar formulário sem um nome exibe a mensagem de erro "o campo nome é obrigatório".</span><span class="sxs-lookup"><span data-stu-id="4bbab-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="4bbab-258">no formulário.</span><span class="sxs-lookup"><span data-stu-id="4bbab-258">on the form.</span></span> <span data-ttu-id="4bbab-259">Se o JavaScript estiver habilitado no cliente, o navegador exibirá o erro sem postar no servidor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="4bbab-260">O `[StringLength(10)]` atributo é gerado `data-val-length-max="10"` no HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="4bbab-261">`data-val-length-max`impede que os navegadores insiram mais do que o comprimento máximo especificado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="4bbab-262">Se uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) for usada para editar e reproduzir a postagem:</span><span class="sxs-lookup"><span data-stu-id="4bbab-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="4bbab-263">Com o nome maior que 10.</span><span class="sxs-lookup"><span data-stu-id="4bbab-263">With the name longer than 10.</span></span>
* <span data-ttu-id="4bbab-264">A mensagem de erro "o nome do campo deve ser uma cadeia de caracteres com um comprimento máximo de 10".</span><span class="sxs-lookup"><span data-stu-id="4bbab-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="4bbab-265">é retornado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-265">is returned.</span></span>

<span data-ttu-id="4bbab-266">Considere o seguinte `Movie` modelo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="4bbab-267">Os atributos de validação especificam o comportamento a ser aplicado nas propriedades de modelo às quais eles são aplicados:</span><span class="sxs-lookup"><span data-stu-id="4bbab-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="4bbab-268">Os `Required` `MinimumLength` atributos e indicam que uma propriedade deve ter um valor, mas nada impede que um usuário insira espaço em branco para atender a essa validação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="4bbab-269">O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="4bbab-270">No código anterior, "Gênero":</span><span class="sxs-lookup"><span data-stu-id="4bbab-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="4bbab-271">Deve usar apenas letras.</span><span class="sxs-lookup"><span data-stu-id="4bbab-271">Must only use letters.</span></span>
  * <span data-ttu-id="4bbab-272">A primeira letra deve ser maiúscula.</span><span class="sxs-lookup"><span data-stu-id="4bbab-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="4bbab-273">Espaços em branco, números e caracteres especiais não são permitidos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="4bbab-274">A "Classificação" `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="4bbab-275">Exige que o primeiro caractere seja uma letra maiúscula.</span><span class="sxs-lookup"><span data-stu-id="4bbab-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="4bbab-276">Permite caracteres especiais e números em espaços subsequentes.</span><span class="sxs-lookup"><span data-stu-id="4bbab-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="4bbab-277">"PG-13" é válido para uma classificação, mas é um erro em "Gênero".</span><span class="sxs-lookup"><span data-stu-id="4bbab-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="4bbab-278">O atributo `Range` restringe um valor a um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="4bbab-279">O `StringLength` atributo define o comprimento máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu comprimento mínimo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="4bbab-280">Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="4bbab-281">A página criar para o `Movie` modelo mostra exibe erros com valores inválidos:</span><span class="sxs-lookup"><span data-stu-id="4bbab-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="4bbab-283">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="4bbab-283">For more information, see:</span></span>

* [<span data-ttu-id="4bbab-284">Adicionar validação ao aplicativo de filme</span><span class="sxs-lookup"><span data-stu-id="4bbab-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="4bbab-285">[Validação de modelo no ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="4bbab-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="4bbab-286">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="4bbab-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="4bbab-287">`HEAD`as solicitações permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="4bbab-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="4bbab-288">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="4bbab-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="4bbab-289">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="4bbab-290">Razor Pages retornará para chamar o `OnGet` manipulador se nenhum `OnHead` manipulador for definido.</span><span class="sxs-lookup"><span data-stu-id="4bbab-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="4bbab-291">XSRF/CSRF e Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="4bbab-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="4bbab-292">Razor Pages são protegidos pela [validação de antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="4bbab-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="4bbab-293">O [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML.</span><span class="sxs-lookup"><span data-stu-id="4bbab-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="4bbab-294">Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="4bbab-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="4bbab-295">As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="4bbab-296">Layouts, parciais, modelos, auxiliares de marca, *_ViewStart. cshtml*e *_ViewImports. cshtml* funcionam da mesma maneira que fazem para exibições convencionais do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="4bbab-297">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="4bbab-298">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="4bbab-299">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="4bbab-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="4bbab-300">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="4bbab-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="4bbab-301">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="4bbab-302">O conteúdo da página Razor é renderizado onde `@RenderBody()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="4bbab-303">Para obter mais informações, consulte [página de layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="4bbab-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="4bbab-304">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="4bbab-305">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="4bbab-306">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="4bbab-307">Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="4bbab-308">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="4bbab-309">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="4bbab-310">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="4bbab-310">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="4bbab-311">As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="4bbab-311">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="4bbab-312">A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="4bbab-313">Os layouts, modelos e parciais usados com controladores MVC e exibições convencionais do Razor *funcionam apenas*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="4bbab-314">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="4bbab-315">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="4bbab-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="4bbab-316">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="4bbab-317">A `@namespace` diretiva definida em uma página:</span><span class="sxs-lookup"><span data-stu-id="4bbab-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="4bbab-318">A `@namespace` diretiva define o namespace para a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="4bbab-319">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="4bbab-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="4bbab-320">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="4bbab-321">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="4bbab-322">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="4bbab-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="4bbab-323">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="4bbab-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="4bbab-324">O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="4bbab-325">`@namespace` *também funciona com exibições convencionais do Razor.*</span><span class="sxs-lookup"><span data-stu-id="4bbab-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="4bbab-326">Considere o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4bbab-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="4bbab-327">O arquivo de exibição *páginas/Create. cshtml* atualizado com *_ViewImports. cshtml* e o arquivo de layout anterior:</span><span class="sxs-lookup"><span data-stu-id="4bbab-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="4bbab-328">No código anterior, o *_ViewImports. cshtml* importou os auxiliares de namespace e de marca.</span><span class="sxs-lookup"><span data-stu-id="4bbab-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="4bbab-329">O arquivo de layout importou os arquivos JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4bbab-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="4bbab-330">O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="4bbab-331">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="4bbab-332">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="4bbab-332">URL generation for Pages</span></span>

<span data-ttu-id="4bbab-333">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="4bbab-334">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="4bbab-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="4bbab-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="4bbab-335">*/Pages*</span></span>

  * <span data-ttu-id="4bbab-336">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="4bbab-337">*Privacidade. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="4bbab-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="4bbab-338">*/Customers*</span></span>

    * <span data-ttu-id="4bbab-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="4bbab-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="4bbab-341">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-341">*Index.cshtml*</span></span>

<span data-ttu-id="4bbab-342">As páginas *pages/Customers/Create. cshtml* e *pages/Customers/Edit. cshtml* redirecionam para *pages/Customers/index. cshtml* após o êxito.</span><span class="sxs-lookup"><span data-stu-id="4bbab-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="4bbab-343">A cadeia de caracteres `./Index` é um nome de página relativo usado para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="4bbab-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="4bbab-344">Ele é usado para gerar URLs para a página *pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="4bbab-345">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="4bbab-346">O nome de página absoluto `/Index` é usado para gerar URLs para a página *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4bbab-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="4bbab-347">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="4bbab-348">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="4bbab-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="4bbab-349">Os exemplos de geração de URL anteriores oferecem opções avançadas e recursos funcionais por meio da codificação de uma URL.</span><span class="sxs-lookup"><span data-stu-id="4bbab-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="4bbab-350">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="4bbab-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="4bbab-351">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="4bbab-352">A tabela a seguir mostra qual página de índice é selecionada usando `RedirectToPage` parâmetros diferentes em *pages/Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="4bbab-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="4bbab-353">RedirectToPage(x)</span></span>| <span data-ttu-id="4bbab-354">Página</span><span class="sxs-lookup"><span data-stu-id="4bbab-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="4bbab-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="4bbab-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="4bbab-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-356">*Pages/Index*</span></span> |
| <span data-ttu-id="4bbab-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="4bbab-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="4bbab-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="4bbab-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="4bbab-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="4bbab-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-360">*Pages/Index*</span></span> |
| <span data-ttu-id="4bbab-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="4bbab-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="4bbab-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="4bbab-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="4bbab-364">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="4bbab-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="4bbab-365">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="4bbab-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="4bbab-366">Quando nomes relativos são usados para vincular entre páginas em uma pasta:</span><span class="sxs-lookup"><span data-stu-id="4bbab-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="4bbab-367">Renomear uma pasta não interrompe os links relativos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="4bbab-368">Os links não são desfeitos porque não incluem o nome da pasta.</span><span class="sxs-lookup"><span data-stu-id="4bbab-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="4bbab-369">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="4bbab-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="4bbab-370">Para obter mais informações, consulte <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="4bbab-371">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="4bbab-371">ViewData attribute</span></span>

<span data-ttu-id="4bbab-372">Os dados podem ser passados para uma página com <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="4bbab-373">As propriedades com o `[ViewData]` atributo têm seus valores armazenados e carregados do <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="4bbab-374">No exemplo a seguir, o `AboutModel` aplica o `[ViewData]` atributo à `Title` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="4bbab-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="4bbab-375">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="4bbab-376">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="4bbab-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="4bbab-377">TempData</span><span class="sxs-lookup"><span data-stu-id="4bbab-377">TempData</span></span>

<span data-ttu-id="4bbab-378">ASP.NET Core expõe o <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="4bbab-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="4bbab-379">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-379">This property stores data until it's read.</span></span> <span data-ttu-id="4bbab-380">Os métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="4bbab-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="4bbab-381">`TempData`é útil para redirecionamento, quando os dados são necessários para mais do que uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="4bbab-382">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="4bbab-383">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="4bbab-384">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="4bbab-385">Para obter mais informações, consulte [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="4bbab-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="4bbab-386">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="4bbab-386">Multiple handlers per page</span></span>

<span data-ttu-id="4bbab-387">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="4bbab-388">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="4bbab-389">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="4bbab-390">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="4bbab-391">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="4bbab-392">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="4bbab-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="4bbab-393">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="4bbab-394">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="4bbab-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="4bbab-395">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="4bbab-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="4bbab-396">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="4bbab-397">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="4bbab-398">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="4bbab-399">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="4bbab-399">Custom routes</span></span>

<span data-ttu-id="4bbab-400">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="4bbab-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="4bbab-401">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-401">Specify a custom route to a page.</span></span> <span data-ttu-id="4bbab-402">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="4bbab-403">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-403">Append segments to a page's default route.</span></span> <span data-ttu-id="4bbab-404">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="4bbab-405">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="4bbab-406">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="4bbab-407">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="4bbab-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="4bbab-408">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="4bbab-409">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="4bbab-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="4bbab-410">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a `@page` diretiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="4bbab-411">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="4bbab-412">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="4bbab-413">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="4bbab-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="4bbab-414">Configuração e configurações avançadas</span><span class="sxs-lookup"><span data-stu-id="4bbab-414">Advanced configuration and settings</span></span>

<span data-ttu-id="4bbab-415">A configuração e as configurações nas seções a seguir não são exigidas pela maioria dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="4bbab-416">Para configurar opções avançadas, use o método de extensão <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> :</span><span class="sxs-lookup"><span data-stu-id="4bbab-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="4bbab-417">Use o <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para definir o diretório raiz para páginas ou adicione convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="4bbab-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="4bbab-418">Para obter mais informações sobre convenções, consulte [Razor Pages convenções de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="4bbab-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="4bbab-419">Para pré-compilar exibições, consulte [compilação de exibição do Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="4bbab-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="4bbab-420">Especificar que as Páginas Razor estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="4bbab-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="4bbab-421">Por padrão, as Páginas Razor estão na raiz do diretório */Pages*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="4bbab-422">Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que seus Razor Pages estão na [raiz do conteúdo](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="4bbab-423">Especificar que as Páginas Razor estão em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="4bbab-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="4bbab-424">Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que Razor Pages estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="4bbab-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="4bbab-425">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4bbab-425">Additional resources</span></span>

* <span data-ttu-id="4bbab-426">Consulte Introdução [ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução</span><span class="sxs-lookup"><span data-stu-id="4bbab-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="4bbab-427">Baixar ou exibir código de exemplo</span><span class="sxs-lookup"><span data-stu-id="4bbab-427">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4bbab-428">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="4bbab-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="4bbab-429">Páginas do Razor é um novo aspecto do ASP.NET Core MVC que torna a codificação de cenários focados em página mais fácil e produtiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-429">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="4bbab-430">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="4bbab-430">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="4bbab-431">Este documento proporciona uma introdução a páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-431">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="4bbab-432">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-432">It's not a step by step tutorial.</span></span> <span data-ttu-id="4bbab-433">Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="4bbab-433">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="4bbab-434">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="4bbab-434">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4bbab-435">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4bbab-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bbab-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bbab-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bbab-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bbab-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bbab-438">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4bbab-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="4bbab-439">Criar um projeto do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4bbab-439">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bbab-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bbab-440">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bbab-441">Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4bbab-441">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bbab-442">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4bbab-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4bbab-443">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-443">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="4bbab-444">Abra o arquivo *.csproj* gerado do Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="4bbab-444">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bbab-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bbab-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4bbab-446">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-446">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="4bbab-447">Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="4bbab-447">Razor Pages</span></span>

<span data-ttu-id="4bbab-448">O Páginas do Razor está habilitado em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-448">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="4bbab-449">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="4bbab-449">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="4bbab-450">O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="4bbab-450">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="4bbab-451">O que o torna diferentes é a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-451">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="4bbab-452">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-452">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="4bbab-453">`@page` deve ser a primeira diretiva do Razor em uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-453">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="4bbab-454">`@page` afeta o comportamento de outros constructos do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-454">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="4bbab-455">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="4bbab-455">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="4bbab-456">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-456">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="4bbab-457">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-457">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="4bbab-458">Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-458">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="4bbab-459">Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-459">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="4bbab-460">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-460">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="4bbab-461">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-461">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="4bbab-462">A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="4bbab-462">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="4bbab-463">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="4bbab-463">File name and path</span></span>               | <span data-ttu-id="4bbab-464">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="4bbab-464">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="4bbab-465">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-465">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="4bbab-466">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="4bbab-466">`/` or `/Index`</span></span> |
| <span data-ttu-id="4bbab-467">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-467">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="4bbab-468">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-468">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="4bbab-469">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-469">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="4bbab-470">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="4bbab-470">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="4bbab-471">Observações:</span><span class="sxs-lookup"><span data-stu-id="4bbab-471">Notes:</span></span>

* <span data-ttu-id="4bbab-472">O runtime procura arquivos de Páginas do Razor na pasta *Pages* por padrão.</span><span class="sxs-lookup"><span data-stu-id="4bbab-472">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="4bbab-473">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-473">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="4bbab-474">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="4bbab-474">Write a basic form</span></span>

<span data-ttu-id="4bbab-475">Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-475">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="4bbab-476">[Model binding](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-476">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="4bbab-477">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-477">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="4bbab-478">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="4bbab-478">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="4bbab-479">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="4bbab-479">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="4bbab-480">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="4bbab-480">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="4bbab-481">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-481">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="4bbab-482">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-482">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="4bbab-483">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-483">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="4bbab-484">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-484">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="4bbab-485">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-485">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="4bbab-486">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="4bbab-486">This separation allows:</span></span>

* <span data-ttu-id="4bbab-487">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4bbab-487">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="4bbab-488">[Teste de unidade](xref:test/razor-pages-tests) das páginas.</span><span class="sxs-lookup"><span data-stu-id="4bbab-488">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="4bbab-489">A página tem um  *método de manipulador*`OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="4bbab-489">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="4bbab-490">Você pode adicionar métodos de manipulador para qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="4bbab-490">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="4bbab-491">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="4bbab-491">The most common handlers are:</span></span>

* <span data-ttu-id="4bbab-492">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-492">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="4bbab-493">Amostra de [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="4bbab-493">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="4bbab-494">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="4bbab-494">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="4bbab-495">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="4bbab-495">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="4bbab-496">O código anterior é comum para as Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-496">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="4bbab-497">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="4bbab-497">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="4bbab-498">O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="4bbab-498">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="4bbab-499">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation), [validação](xref:mvc/models/validation)e resultados de ação, é compartilhada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-499">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="4bbab-500">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="4bbab-500">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="4bbab-501">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-501">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="4bbab-502">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-502">Check for validation errors.</span></span>

* <span data-ttu-id="4bbab-503">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="4bbab-503">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="4bbab-504">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-504">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="4bbab-505">A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais.</span><span class="sxs-lookup"><span data-stu-id="4bbab-505">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="4bbab-506">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-506">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="4bbab-507">Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-507">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="4bbab-508">`RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="4bbab-508">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="4bbab-509">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="4bbab-509">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="4bbab-510">`RedirectToPage`é detalhado na seção [geração de URL para páginas](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="4bbab-510">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="4bbab-511">Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-511">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="4bbab-512">`Page` retorna uma instância de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-512">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="4bbab-513">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-513">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="4bbab-514">`PageResult`é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="4bbab-514">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="4bbab-515">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-515">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="4bbab-516">A propriedade `Customer` usa o atributo `[BindProperty]` para aceitar o model binding.</span><span class="sxs-lookup"><span data-stu-id="4bbab-516">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="4bbab-517">O Razor Pages, por padrão, associa propriedades somente com verbos não `GET`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-517">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="4bbab-518">A associação de propriedades pode reduzir a quantidade de código que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="4bbab-518">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="4bbab-519">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="4bbab-519">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="4bbab-520">A home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="4bbab-520">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="4bbab-521">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="4bbab-521">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="4bbab-522">O arquivo *cshtml* contém a marcação a seguir para criar um link de edição para cada contato:</span><span class="sxs-lookup"><span data-stu-id="4bbab-522">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="4bbab-523">O `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o `asp-route-{value}` atributo para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="4bbab-523">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="4bbab-524">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="4bbab-524">The link contains route data with the contact ID.</span></span> <span data-ttu-id="4bbab-525">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-525">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="4bbab-526">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-526">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="4bbab-527">Os Auxiliares de Marcação são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="4bbab-527">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="4bbab-528">O arquivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-528">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="4bbab-529">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-529">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="4bbab-530">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-530">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="4bbab-531">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="4bbab-531">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="4bbab-532">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="4bbab-532">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="4bbab-533">O arquivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-533">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="4bbab-534">O arquivo *Index.cshtml* também contém a marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="4bbab-534">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="4bbab-535">Quando o botão de exclusão é renderizado em HTML, seu `formaction` inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="4bbab-535">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="4bbab-536">A ID de contato do cliente especificada pelo atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-536">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="4bbab-537">O `handler` especificado pelo atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-537">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="4bbab-538">Este é um exemplo de um botão de exclusão renderizado com uma ID de contato do cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-538">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="4bbab-539">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-539">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="4bbab-540">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-540">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="4bbab-541">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-541">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="4bbab-542">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-542">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="4bbab-543">O código a seguir mostra o `OnPostDeleteAsync` manipulador:</span><span class="sxs-lookup"><span data-stu-id="4bbab-543">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="4bbab-544">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-544">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="4bbab-545">Aceita o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="4bbab-545">Accepts the `id` from the query string.</span></span> <span data-ttu-id="4bbab-546">Se a diretiva de página *index. cshtml* continha a restrição `"{id:int?}"` de roteamento, ela `id` virá de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="4bbab-546">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="4bbab-547">Os dados de rota para `id` são especificados no URI, como `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-547">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="4bbab-548">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-548">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="4bbab-549">Se o contato do cliente for encontrado, eles serão removidos da lista de contatos do cliente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-549">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="4bbab-550">O banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="4bbab-550">The database is updated.</span></span>
* <span data-ttu-id="4bbab-551">Chama `RedirectToPage` para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="4bbab-551">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="4bbab-552">Marque as propriedades da página conforme necessário</span><span class="sxs-lookup"><span data-stu-id="4bbab-552">Mark page properties as required</span></span>

<span data-ttu-id="4bbab-553">As propriedades em um `PageModel` podem ser marcadas com o atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :</span><span class="sxs-lookup"><span data-stu-id="4bbab-553">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="4bbab-554">Para obter mais informações, confira [Validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="4bbab-554">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="4bbab-555">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="4bbab-555">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="4bbab-556">As solicitações `HEAD` permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="4bbab-556">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="4bbab-557">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="4bbab-557">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="4bbab-558">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-558">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="4bbab-559">No ASP.NET Core 2.1 ou superior, o Razor Pages volta a chamar o manipulador `OnGet` quando nenhum manipulador `OnHead` está definido.</span><span class="sxs-lookup"><span data-stu-id="4bbab-559">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="4bbab-560">Esse comportamento é habilitado pela chamada para [SetCompatibilityVersion](xref:mvc/compatibility-version) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-560">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="4bbab-561">Os modelos padrão geram a chamada `SetCompatibilityVersion` no ASP.NET Core 2.1 e 2.2.</span><span class="sxs-lookup"><span data-stu-id="4bbab-561">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="4bbab-562">`SetCompatibilityVersion` define de forma eficiente a opção de Páginas Razor `AllowMappingHeadRequestsToGetHandler` como `true`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-562">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="4bbab-563">Em vez de aceitar todos os comportamentos com `SetCompatibilityVersion`, você pode aceitar explicitamente participar de comportamentos *específicos*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-563">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="4bbab-564">O código a seguir aceita permitir que solicitações `HEAD` sejam mapeadas para o manipulador `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-564">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="4bbab-565">XSRF/CSRF e Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="4bbab-565">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="4bbab-566">Você não precisa escrever nenhum código para [validação antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="4bbab-566">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="4bbab-567">Validação e geração de token antifalsificação são automaticamente incluídas nas Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-567">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="4bbab-568">Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="4bbab-568">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="4bbab-569">As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-569">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="4bbab-570">Layouts, parciais, modelos, auxiliares de marcas, *_ViewStart.cshtml* e *_ViewImports.cshtml* funcionam da mesma forma que funcionam exibições convencionais do Razor.</span><span class="sxs-lookup"><span data-stu-id="4bbab-570">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="4bbab-571">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-571">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="4bbab-572">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-572">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="4bbab-573">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="4bbab-573">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="4bbab-574">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="4bbab-574">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="4bbab-575">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="4bbab-575">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="4bbab-576">Veja [página de layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="4bbab-576">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="4bbab-577">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-577">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="4bbab-578">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-578">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="4bbab-579">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-579">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="4bbab-580">Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-580">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="4bbab-581">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-581">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="4bbab-582">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-582">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="4bbab-583">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="4bbab-583">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="4bbab-584">As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="4bbab-584">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="4bbab-585">A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-585">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="4bbab-586">Os layouts, modelos e parciais que você está usando com controladores MVC e exibições do Razor convencionais *apenas funcionam*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-586">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="4bbab-587">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-587">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="4bbab-588">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="4bbab-588">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="4bbab-589">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-589">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="4bbab-590">Quando a diretiva `@namespace` é usada explicitamente em uma página:</span><span class="sxs-lookup"><span data-stu-id="4bbab-590">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="4bbab-591">A diretiva define o namespace da página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-591">The directive sets the namespace for the page.</span></span> <span data-ttu-id="4bbab-592">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="4bbab-592">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="4bbab-593">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-593">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="4bbab-594">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-594">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="4bbab-595">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="4bbab-595">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="4bbab-596">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="4bbab-596">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="4bbab-597">O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-597">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="4bbab-598">`@namespace` *também funciona com exibições convencionais do Razor.*</span><span class="sxs-lookup"><span data-stu-id="4bbab-598">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="4bbab-599">O arquivo de exibição *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="4bbab-599">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="4bbab-600">O arquivo de exibição *Pages/Create.cshtml* atualizado:</span><span class="sxs-lookup"><span data-stu-id="4bbab-600">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="4bbab-601">O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-601">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="4bbab-602">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-602">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="4bbab-603">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="4bbab-603">URL generation for Pages</span></span>

<span data-ttu-id="4bbab-604">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-604">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="4bbab-605">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="4bbab-605">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="4bbab-606">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="4bbab-606">*/Pages*</span></span>

  * <span data-ttu-id="4bbab-607">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-607">*Index.cshtml*</span></span>
  * <span data-ttu-id="4bbab-608">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="4bbab-608">*/Customers*</span></span>

    * <span data-ttu-id="4bbab-609">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-609">*Create.cshtml*</span></span>
    * <span data-ttu-id="4bbab-610">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-610">*Edit.cshtml*</span></span>
    * <span data-ttu-id="4bbab-611">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4bbab-611">*Index.cshtml*</span></span>

<span data-ttu-id="4bbab-612">As páginas *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* redirecionam para o *Pages/Index.cshtml* após êxito.</span><span class="sxs-lookup"><span data-stu-id="4bbab-612">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="4bbab-613">A cadeia de caracteres `/Index` faz parte do URI para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="4bbab-613">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="4bbab-614">A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-614">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="4bbab-615">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-615">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="4bbab-616">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="4bbab-616">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="4bbab-617">Os exemplos anteriores de geração de URL oferecem opções avançadas e recursos funcionais para codificar uma URL.</span><span class="sxs-lookup"><span data-stu-id="4bbab-617">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="4bbab-618">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="4bbab-618">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="4bbab-619">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-619">URL generation for pages supports relative names.</span></span> <span data-ttu-id="4bbab-620">A tabela a seguir mostra qual página de Índice é selecionada com diferentes parâmetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4bbab-620">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="4bbab-621">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="4bbab-621">RedirectToPage(x)</span></span>| <span data-ttu-id="4bbab-622">Página</span><span class="sxs-lookup"><span data-stu-id="4bbab-622">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="4bbab-623">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="4bbab-623">RedirectToPage("/Index")</span></span> | <span data-ttu-id="4bbab-624">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-624">*Pages/Index*</span></span> |
| <span data-ttu-id="4bbab-625">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="4bbab-625">RedirectToPage("./Index");</span></span> | <span data-ttu-id="4bbab-626">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-626">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="4bbab-627">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="4bbab-627">RedirectToPage("../Index")</span></span> | <span data-ttu-id="4bbab-628">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-628">*Pages/Index*</span></span> |
| <span data-ttu-id="4bbab-629">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="4bbab-629">RedirectToPage("Index")</span></span>  | <span data-ttu-id="4bbab-630">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="4bbab-630">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="4bbab-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="4bbab-632">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="4bbab-632">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="4bbab-633">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="4bbab-633">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="4bbab-634">Se você usar nomes relativos para vincular entre páginas em uma pasta, você poderá renomear essa pasta.</span><span class="sxs-lookup"><span data-stu-id="4bbab-634">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="4bbab-635">Todos os links ainda funcionarão (porque eles não incluirão o nome da pasta).</span><span class="sxs-lookup"><span data-stu-id="4bbab-635">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="4bbab-636">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="4bbab-636">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="4bbab-637">Para obter mais informações, consulte <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="4bbab-637">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="4bbab-638">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="4bbab-638">ViewData attribute</span></span>

<span data-ttu-id="4bbab-639">Os dados podem ser passados para uma página com [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="4bbab-639">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="4bbab-640">As propriedades em controladores ou Razor modelos de página com o `[ViewData]` atributo têm seus valores armazenados e carregados do [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="4bbab-640">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="4bbab-641">No exemplo a seguir, o `AboutModel` contém uma `Title` propriedade marcada com `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="4bbab-641">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="4bbab-642">A propriedade `Title` está definida como o título da página Sobre:</span><span class="sxs-lookup"><span data-stu-id="4bbab-642">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="4bbab-643">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-643">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="4bbab-644">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="4bbab-644">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="4bbab-645">TempData</span><span class="sxs-lookup"><span data-stu-id="4bbab-645">TempData</span></span>

<span data-ttu-id="4bbab-646">O ASP.NET Core expõe a propriedade [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) em um [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="4bbab-646">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="4bbab-647">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="4bbab-647">This property stores data until it's read.</span></span> <span data-ttu-id="4bbab-648">Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="4bbab-648">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="4bbab-649">`TempData` é útil para redirecionamento nos casos em que os dados são necessários para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="4bbab-649">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="4bbab-650">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-650">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="4bbab-651">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-651">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="4bbab-652">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-652">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="4bbab-653">Para obter mais informações, confira [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="4bbab-653">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="4bbab-654">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="4bbab-654">Multiple handlers per page</span></span>

<span data-ttu-id="4bbab-655">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="4bbab-655">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="4bbab-656">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="4bbab-656">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="4bbab-657">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-657">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="4bbab-658">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-658">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="4bbab-659">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="4bbab-659">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="4bbab-660">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="4bbab-660">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="4bbab-661">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="4bbab-661">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="4bbab-662">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="4bbab-662">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="4bbab-663">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="4bbab-663">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="4bbab-664">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-664">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="4bbab-665">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-665">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="4bbab-666">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-666">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="4bbab-667">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="4bbab-667">Custom routes</span></span>

<span data-ttu-id="4bbab-668">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="4bbab-668">Use the `@page` directive to:</span></span>

* <span data-ttu-id="4bbab-669">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-669">Specify a custom route to a page.</span></span> <span data-ttu-id="4bbab-670">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-670">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="4bbab-671">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-671">Append segments to a page's default route.</span></span> <span data-ttu-id="4bbab-672">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-672">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="4bbab-673">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="4bbab-673">Append parameters to a page's default route.</span></span> <span data-ttu-id="4bbab-674">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-674">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="4bbab-675">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="4bbab-675">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="4bbab-676">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-676">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="4bbab-677">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="4bbab-677">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="4bbab-678">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a `@page` diretiva.</span><span class="sxs-lookup"><span data-stu-id="4bbab-678">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="4bbab-679">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-679">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="4bbab-680">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="4bbab-680">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="4bbab-681">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="4bbab-681">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="4bbab-682">Configuração e definições</span><span class="sxs-lookup"><span data-stu-id="4bbab-682">Configuration and settings</span></span>

<span data-ttu-id="4bbab-683">Para configurar opções avançadas, use o método de extensão `AddRazorPagesOptions` no construtor de MVC:</span><span class="sxs-lookup"><span data-stu-id="4bbab-683">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="4bbab-684">No momento, você pode usar o `RazorPagesOptions` para definir o diretório raiz para páginas ou adicionar as convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="4bbab-684">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="4bbab-685">Permitiremos mais extensibilidade dessa maneira no futuro.</span><span class="sxs-lookup"><span data-stu-id="4bbab-685">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="4bbab-686">Para pré-compilar exibições, consulte [ Razor Exibir compilação](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="4bbab-686">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="4bbab-687">[Baixar ou exibir código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="4bbab-687">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="4bbab-688">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="4bbab-688">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="4bbab-689">Especificar que Razor as páginas estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="4bbab-689">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="4bbab-690">Por padrão, Razor as páginas têm raiz no diretório */pages*</span><span class="sxs-lookup"><span data-stu-id="4bbab-690">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="4bbab-691">Adicione [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que suas Razor páginas estão na [raiz do conteúdo](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4bbab-691">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="4bbab-692">Especificar que as Razor páginas estejam em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="4bbab-692">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="4bbab-693">Adicione [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que suas Razor páginas estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="4bbab-693">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="4bbab-694">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4bbab-694">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
