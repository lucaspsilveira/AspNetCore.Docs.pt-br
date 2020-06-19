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
ms.openlocfilehash: 52c3dc82e51cb4375954a603a1bfde60fd667b56
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103048"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="eaca5-103">Introdução às Razor páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaca5-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaca5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="eaca5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="eaca5-105">As páginas podem tornar os cenários voltados para a página de codificação mais fáceis e produtivos do que usar controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="eaca5-105"> Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="eaca5-106">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="eaca5-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="eaca5-107">Este documento fornece uma introdução às Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="eaca5-108">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="eaca5-109">Se você encontrar algumas das seções muito avançadas, consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="eaca5-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="eaca5-110">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="eaca5-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eaca5-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="eaca5-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaca5-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaca5-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaca5-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaca5-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaca5-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eaca5-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="eaca5-115">Criar um Razor projeto de páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaca5-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaca5-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaca5-117">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um Razor projeto de páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaca5-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaca5-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eaca5-119">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaca5-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eaca5-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eaca5-121">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um Razor projeto de páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="eaca5-122">Pages</span><span class="sxs-lookup"><span data-stu-id="eaca5-122"> Pages</span></span>

Razor<span data-ttu-id="eaca5-123">As páginas estão habilitadas no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-123"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="eaca5-124">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="eaca5-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="eaca5-125">O código anterior é muito parecido com um [ Razor arquivo de exibição](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="eaca5-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="eaca5-126">O que o torna diferente é a [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="eaca5-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="eaca5-127">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="eaca5-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="eaca5-128">`@page`deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="eaca5-129">`@page`afeta o comportamento de outras [Razor](xref:mvc/views/razor) construções.</span><span class="sxs-lookup"><span data-stu-id="eaca5-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> Razor<span data-ttu-id="eaca5-130">Os nomes de arquivo de páginas têm um sufixo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eaca5-130"> Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="eaca5-131">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="eaca5-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="eaca5-132">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="eaca5-133">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="eaca5-134">Por convenção, o `PageModel` arquivo de classe tem o mesmo nome que o Razor arquivo de paginação com *. cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="eaca5-135">Por exemplo, a Razor página anterior é *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="eaca5-136">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="eaca5-137">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="eaca5-138">A tabela a seguir mostra um Razor caminho de página e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="eaca5-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="eaca5-139">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="eaca5-139">File name and path</span></span>               | <span data-ttu-id="eaca5-140">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="eaca5-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eaca5-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="eaca5-142">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="eaca5-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="eaca5-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="eaca5-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="eaca5-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="eaca5-146">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="eaca5-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="eaca5-147">Observações:</span><span class="sxs-lookup"><span data-stu-id="eaca5-147">Notes:</span></span>

* <span data-ttu-id="eaca5-148">O tempo de execução procura por Razor arquivos de páginas na pasta *páginas* por padrão.</span><span class="sxs-lookup"><span data-stu-id="eaca5-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="eaca5-149">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="eaca5-150">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="eaca5-150">Write a basic form</span></span>

Razor<span data-ttu-id="eaca5-151">As páginas são projetadas para tornar os padrões comuns usados com navegadores da Web fáceis de implementar ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-151"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="eaca5-152">[Associação de modelo](xref:mvc/models/model-binding), [auxiliares de marca](xref:mvc/views/tag-helpers/intro)e auxiliares HTML *funcionam apenas* com as propriedades definidas em uma Razor classe de página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="eaca5-153">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="eaca5-154">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="eaca5-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="eaca5-155">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="eaca5-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="eaca5-156">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="eaca5-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="eaca5-157">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="eaca5-158">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="eaca5-159">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="eaca5-160">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="eaca5-161">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="eaca5-162">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="eaca5-162">This separation allows:</span></span>

* <span data-ttu-id="eaca5-163">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eaca5-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="eaca5-164">Teste de unidade</span><span class="sxs-lookup"><span data-stu-id="eaca5-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="eaca5-165">A página tem um  *método de manipulador*`OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="eaca5-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="eaca5-166">Métodos de manipulador para qualquer verbo HTTP podem ser adicionados.</span><span class="sxs-lookup"><span data-stu-id="eaca5-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="eaca5-167">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="eaca5-167">The most common handlers are:</span></span>

* <span data-ttu-id="eaca5-168">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="eaca5-169">No código anterior, o `OnGet` método exibe a página *CREATEMODEL. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="eaca5-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="eaca5-170">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="eaca5-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="eaca5-171">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="eaca5-172">O código anterior é típico para Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="eaca5-173">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="eaca5-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="eaca5-174">O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="eaca5-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="eaca5-175">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation)e resultados de ação, funciona da mesma forma com controladores e Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="eaca5-176">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="eaca5-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="eaca5-177">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="eaca5-178">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-178">Check for validation errors.</span></span>

* <span data-ttu-id="eaca5-179">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="eaca5-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="eaca5-180">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="eaca5-181">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="eaca5-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="eaca5-182">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="eaca5-183">O HTML renderizado de *pages/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="eaca5-184">No código anterior, postando o formulário:</span><span class="sxs-lookup"><span data-stu-id="eaca5-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="eaca5-185">Com dados válidos:</span><span class="sxs-lookup"><span data-stu-id="eaca5-185">With valid data:</span></span>

  * <span data-ttu-id="eaca5-186">O `OnPostAsync` método de manipulador chama o <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> método auxiliar.</span><span class="sxs-lookup"><span data-stu-id="eaca5-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="eaca5-187">`RedirectToPage` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="eaca5-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="eaca5-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="eaca5-189">É um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-189">Is an action result.</span></span>
    * <span data-ttu-id="eaca5-190">É semelhante a `RedirectToAction` ou `RedirectToRoute` (usado em controladores e exibições).</span><span class="sxs-lookup"><span data-stu-id="eaca5-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="eaca5-191">É personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-191">Is customized for pages.</span></span> <span data-ttu-id="eaca5-192">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eaca5-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="eaca5-193">`RedirectToPage`é detalhado na seção [geração de URL para páginas](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="eaca5-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="eaca5-194">Com erros de validação que são passados para o servidor:</span><span class="sxs-lookup"><span data-stu-id="eaca5-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="eaca5-195">O `OnPostAsync` método de manipulador chama o <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> método auxiliar.</span><span class="sxs-lookup"><span data-stu-id="eaca5-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="eaca5-196">`Page` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="eaca5-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="eaca5-197">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="eaca5-198">`PageResult`é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="eaca5-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="eaca5-199">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="eaca5-200">No exemplo anterior, a postagem do formulário sem nenhum resultado em [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) retorna false.</span><span class="sxs-lookup"><span data-stu-id="eaca5-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="eaca5-201">Neste exemplo, nenhum erro de validação é exibido no cliente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="eaca5-202">A entrega do erro de validação será abordada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="eaca5-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="eaca5-203">Com erros de validação detectados pela validação no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="eaca5-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="eaca5-204">Os dados **não** são postados no servidor.</span><span class="sxs-lookup"><span data-stu-id="eaca5-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="eaca5-205">A validação no lado do cliente é explicada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="eaca5-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="eaca5-206">A `Customer` propriedade usa o [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atributo para aceitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="eaca5-207">`[BindProperty]`**não** deve ser usado em modelos que contêm propriedades que não devem ser alteradas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="eaca5-208">Para obter mais informações, consulte [superpostando](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="eaca5-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

Razor<span data-ttu-id="eaca5-209">As páginas, por padrão, associam propriedades somente a não `GET` verbos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-209"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="eaca5-210">A associação a Propriedades remove a necessidade de escrever código para converter dados HTTP no tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="eaca5-211">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="eaca5-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="eaca5-212">Examinando o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eaca5-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="eaca5-213">No código anterior, o [auxiliar de marca de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` associa o `<input>` elemento HTML à `Customer.Name` expressão do modelo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="eaca5-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)torna os auxiliares de marca disponíveis.</span><span class="sxs-lookup"><span data-stu-id="eaca5-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="eaca5-215">Home page</span><span class="sxs-lookup"><span data-stu-id="eaca5-215">The home page</span></span>

<span data-ttu-id="eaca5-216">*Index. cshtml* é o Home Page:</span><span class="sxs-lookup"><span data-stu-id="eaca5-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="eaca5-217">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="eaca5-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="eaca5-218">O arquivo *index. cshtml* contém a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="eaca5-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="eaca5-219">O `<a /a>` [auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o `asp-route-{value}` atributo para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="eaca5-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="eaca5-220">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="eaca5-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="eaca5-221">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="eaca5-222">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="eaca5-223">O arquivo *index. cshtml* contém marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="eaca5-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="eaca5-224">O HTML renderizado:</span><span class="sxs-lookup"><span data-stu-id="eaca5-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="eaca5-225">Quando o botão de exclusão é renderizado em HTML, seu [formsaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="eaca5-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="eaca5-226">A ID de contato do cliente, especificada pelo `asp-route-id` atributo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="eaca5-227">O `handler` , especificado pelo `asp-page-handler` atributo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="eaca5-228">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="eaca5-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="eaca5-229">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="eaca5-230">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="eaca5-231">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="eaca5-232">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="eaca5-233">Obtém o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="eaca5-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="eaca5-234">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="eaca5-235">Se o contato do cliente for encontrado, ele é removido e o banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="eaca5-236">Chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eaca5-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="eaca5-237">O arquivo Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="eaca5-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="eaca5-238">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="eaca5-239">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="eaca5-240">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="eaca5-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="eaca5-241">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="eaca5-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="eaca5-242">O arquivo *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="eaca5-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="eaca5-243">Validação</span><span class="sxs-lookup"><span data-stu-id="eaca5-243">Validation</span></span>

<span data-ttu-id="eaca5-244">Regras de validação:</span><span class="sxs-lookup"><span data-stu-id="eaca5-244">Validation rules:</span></span>

* <span data-ttu-id="eaca5-245">São especificadas declarativamente na classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="eaca5-246">São impostos em todos os lugares no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="eaca5-247">O <xref:System.ComponentModel.DataAnnotations> namespace fornece um conjunto de atributos de validação internos que são aplicados declarativamente a uma classe ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="eaca5-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="eaca5-248">Annotations também contém atributos de formatação como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) essa ajuda com a formatação e não fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="eaca5-249">Considere o `Customer` modelo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="eaca5-250">Usando o seguinte arquivo de exibição *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eaca5-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="eaca5-251">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="eaca5-251">The preceding code:</span></span>

* <span data-ttu-id="eaca5-252">Inclui scripts de validação jQuery e jQuery.</span><span class="sxs-lookup"><span data-stu-id="eaca5-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="eaca5-253">Usa os `<div />` `<span />` [auxiliares de marcação](xref:mvc/views/tag-helpers/intro) e para habilitar:</span><span class="sxs-lookup"><span data-stu-id="eaca5-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="eaca5-254">Validação no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-254">Client-side validation.</span></span>
  * <span data-ttu-id="eaca5-255">Renderização de erro de validação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-255">Validation error rendering.</span></span>

* <span data-ttu-id="eaca5-256">Gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="eaca5-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="eaca5-257">A postagem do valor de criar formulário sem um nome exibe a mensagem de erro "o campo nome é obrigatório".</span><span class="sxs-lookup"><span data-stu-id="eaca5-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="eaca5-258">no formulário.</span><span class="sxs-lookup"><span data-stu-id="eaca5-258">on the form.</span></span> <span data-ttu-id="eaca5-259">Se o JavaScript estiver habilitado no cliente, o navegador exibirá o erro sem postar no servidor.</span><span class="sxs-lookup"><span data-stu-id="eaca5-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="eaca5-260">O `[StringLength(10)]` atributo é gerado `data-val-length-max="10"` no HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="eaca5-261">`data-val-length-max`impede que os navegadores insiram mais do que o comprimento máximo especificado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="eaca5-262">Se uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) for usada para editar e reproduzir a postagem:</span><span class="sxs-lookup"><span data-stu-id="eaca5-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="eaca5-263">Com o nome maior que 10.</span><span class="sxs-lookup"><span data-stu-id="eaca5-263">With the name longer than 10.</span></span>
* <span data-ttu-id="eaca5-264">A mensagem de erro "o nome do campo deve ser uma cadeia de caracteres com um comprimento máximo de 10".</span><span class="sxs-lookup"><span data-stu-id="eaca5-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="eaca5-265">é retornado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-265">is returned.</span></span>

<span data-ttu-id="eaca5-266">Considere o seguinte `Movie` modelo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="eaca5-267">Os atributos de validação especificam o comportamento a ser aplicado nas propriedades de modelo às quais eles são aplicados:</span><span class="sxs-lookup"><span data-stu-id="eaca5-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="eaca5-268">Os `Required` `MinimumLength` atributos e indicam que uma propriedade deve ter um valor, mas nada impede que um usuário insira espaço em branco para atender a essa validação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="eaca5-269">O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="eaca5-270">No código anterior, "Gênero":</span><span class="sxs-lookup"><span data-stu-id="eaca5-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="eaca5-271">Deve usar apenas letras.</span><span class="sxs-lookup"><span data-stu-id="eaca5-271">Must only use letters.</span></span>
  * <span data-ttu-id="eaca5-272">A primeira letra deve ser maiúscula.</span><span class="sxs-lookup"><span data-stu-id="eaca5-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="eaca5-273">Espaços em branco, números e caracteres especiais não são permitidos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="eaca5-274">A "Classificação" `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="eaca5-275">Exige que o primeiro caractere seja uma letra maiúscula.</span><span class="sxs-lookup"><span data-stu-id="eaca5-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="eaca5-276">Permite caracteres especiais e números em espaços subsequentes.</span><span class="sxs-lookup"><span data-stu-id="eaca5-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="eaca5-277">"PG-13" é válido para uma classificação, mas é um erro em "Gênero".</span><span class="sxs-lookup"><span data-stu-id="eaca5-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="eaca5-278">O atributo `Range` restringe um valor a um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="eaca5-279">O `StringLength` atributo define o comprimento máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu comprimento mínimo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="eaca5-280">Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="eaca5-281">A página criar para o `Movie` modelo mostra exibe erros com valores inválidos:</span><span class="sxs-lookup"><span data-stu-id="eaca5-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="eaca5-283">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="eaca5-283">For more information, see:</span></span>

* [<span data-ttu-id="eaca5-284">Adicionar validação ao aplicativo de filme</span><span class="sxs-lookup"><span data-stu-id="eaca5-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="eaca5-285">[Validação de modelo no ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="eaca5-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="eaca5-286">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="eaca5-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="eaca5-287">`HEAD`as solicitações permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="eaca5-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="eaca5-288">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="eaca5-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="eaca5-289">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor<span data-ttu-id="eaca5-290">As páginas voltarão para chamar o `OnGet` manipulador se nenhum `OnHead` manipulador for definido.</span><span class="sxs-lookup"><span data-stu-id="eaca5-290"> Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="eaca5-291">XSRF/CSRF e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-291">XSRF/CSRF and Razor Pages</span></span>

Razor<span data-ttu-id="eaca5-292">As páginas são protegidas pela [validação de antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="eaca5-292"> Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="eaca5-293">O [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML.</span><span class="sxs-lookup"><span data-stu-id="eaca5-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="eaca5-294">Usando layouts, parciais, modelos e auxiliares de marca com Razor páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="eaca5-295">As páginas funcionam com todos os recursos do Razor mecanismo de exibição.</span><span class="sxs-lookup"><span data-stu-id="eaca5-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="eaca5-296">Layouts, parciais, modelos, auxiliares de marca, *_ViewStart. cshtml*e *_ViewImports. cshtml* funcionam da mesma maneira que fazem para Razor exibições convencionais.</span><span class="sxs-lookup"><span data-stu-id="eaca5-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="eaca5-297">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="eaca5-298">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="eaca5-299">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="eaca5-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="eaca5-300">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="eaca5-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="eaca5-301">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="eaca5-302">O conteúdo da Razor página é renderizado onde `@RenderBody()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="eaca5-303">Para obter mais informações, consulte [página de layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="eaca5-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="eaca5-304">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="eaca5-305">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="eaca5-306">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="eaca5-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="eaca5-307">Um layout na pasta *páginas/compartilhadas* pode ser usado em qualquer Razor página na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="eaca5-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="eaca5-308">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="eaca5-309">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="eaca5-310">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="eaca5-310">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="eaca5-311">As páginas destinam-se a contar com a hierarquia de pastas, não com convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="eaca5-311"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="eaca5-312">Exibir pesquisa de uma Razor página inclui a pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="eaca5-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="eaca5-313">Os layouts, modelos e parciais usados com controladores MVC e Razor exibições convencionais *funcionam apenas*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="eaca5-314">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="eaca5-315">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="eaca5-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="eaca5-316">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="eaca5-317">A `@namespace` diretiva definida em uma página:</span><span class="sxs-lookup"><span data-stu-id="eaca5-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="eaca5-318">A `@namespace` diretiva define o namespace para a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="eaca5-319">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="eaca5-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="eaca5-320">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="eaca5-321">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="eaca5-322">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="eaca5-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="eaca5-323">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="eaca5-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="eaca5-324">O namespace gerado para a página *pages/Customers/Edit. cshtml* Razor é o mesmo que a `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="eaca5-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="eaca5-325">`@namespace`*também funciona com Razor exibições convencionais.*</span><span class="sxs-lookup"><span data-stu-id="eaca5-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="eaca5-326">Considere o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eaca5-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="eaca5-327">O arquivo de exibição *páginas/Create. cshtml* atualizado com *_ViewImports. cshtml* e o arquivo de layout anterior:</span><span class="sxs-lookup"><span data-stu-id="eaca5-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="eaca5-328">No código anterior, o *_ViewImports. cshtml* importou os auxiliares de namespace e de marca.</span><span class="sxs-lookup"><span data-stu-id="eaca5-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="eaca5-329">O arquivo de layout importou os arquivos JavaScript.</span><span class="sxs-lookup"><span data-stu-id="eaca5-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="eaca5-330">O [ Razor projeto inicial de páginas](#rpvs17) contém as *páginas/_ValidationScriptsPartial. cshtml*, que conectam a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="eaca5-331">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="eaca5-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="eaca5-332">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-332">URL generation for Pages</span></span>

<span data-ttu-id="eaca5-333">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="eaca5-334">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="eaca5-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="eaca5-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="eaca5-335">*/Pages*</span></span>

  * <span data-ttu-id="eaca5-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="eaca5-337">*Privacidade. cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="eaca5-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="eaca5-338">*/Customers*</span></span>

    * <span data-ttu-id="eaca5-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="eaca5-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="eaca5-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-341">*Index.cshtml*</span></span>

<span data-ttu-id="eaca5-342">As páginas *pages/Customers/Create. cshtml* e *pages/Customers/Edit. cshtml* redirecionam para *pages/Customers/index. cshtml* após o êxito.</span><span class="sxs-lookup"><span data-stu-id="eaca5-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="eaca5-343">A cadeia de caracteres `./Index` é um nome de página relativo usado para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="eaca5-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="eaca5-344">Ele é usado para gerar URLs para a página *pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eaca5-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="eaca5-345">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="eaca5-346">O nome de página absoluto `/Index` é usado para gerar URLs para a página *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eaca5-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="eaca5-347">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="eaca5-348">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="eaca5-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="eaca5-349">Os exemplos de geração de URL anteriores oferecem opções avançadas e recursos funcionais por meio da codificação de uma URL.</span><span class="sxs-lookup"><span data-stu-id="eaca5-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="eaca5-350">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="eaca5-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="eaca5-351">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="eaca5-352">A tabela a seguir mostra qual página de índice é selecionada usando `RedirectToPage` parâmetros diferentes em *pages/Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="eaca5-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="eaca5-353">RedirectToPage(x)</span></span>| <span data-ttu-id="eaca5-354">Página</span><span class="sxs-lookup"><span data-stu-id="eaca5-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eaca5-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="eaca5-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="eaca5-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-356">*Pages/Index*</span></span> |
| <span data-ttu-id="eaca5-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="eaca5-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="eaca5-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="eaca5-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="eaca5-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="eaca5-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-360">*Pages/Index*</span></span> |
| <span data-ttu-id="eaca5-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="eaca5-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="eaca5-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="eaca5-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="eaca5-364">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="eaca5-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="eaca5-365">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="eaca5-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="eaca5-366">Quando nomes relativos são usados para vincular entre páginas em uma pasta:</span><span class="sxs-lookup"><span data-stu-id="eaca5-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="eaca5-367">Renomear uma pasta não interrompe os links relativos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="eaca5-368">Os links não são desfeitos porque não incluem o nome da pasta.</span><span class="sxs-lookup"><span data-stu-id="eaca5-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="eaca5-369">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="eaca5-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="eaca5-370">Para obter mais informações, consulte <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="eaca5-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="eaca5-371">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="eaca5-371">ViewData attribute</span></span>

<span data-ttu-id="eaca5-372">Os dados podem ser passados para uma página com <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="eaca5-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="eaca5-373">As propriedades com o `[ViewData]` atributo têm seus valores armazenados e carregados do <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="eaca5-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="eaca5-374">No exemplo a seguir, o `AboutModel` aplica o `[ViewData]` atributo à `Title` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="eaca5-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="eaca5-375">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="eaca5-376">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="eaca5-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="eaca5-377">TempData</span><span class="sxs-lookup"><span data-stu-id="eaca5-377">TempData</span></span>

<span data-ttu-id="eaca5-378">ASP.NET Core expõe o <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="eaca5-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="eaca5-379">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-379">This property stores data until it's read.</span></span> <span data-ttu-id="eaca5-380">Os métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="eaca5-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="eaca5-381">`TempData`é útil para redirecionamento, quando os dados são necessários para mais do que uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="eaca5-382">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="eaca5-383">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="eaca5-384">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="eaca5-385">Para obter mais informações, consulte [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="eaca5-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="eaca5-386">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="eaca5-386">Multiple handlers per page</span></span>

<span data-ttu-id="eaca5-387">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="eaca5-388">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="eaca5-389">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="eaca5-390">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="eaca5-391">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="eaca5-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="eaca5-392">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="eaca5-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="eaca5-393">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="eaca5-394">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="eaca5-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="eaca5-395">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="eaca5-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="eaca5-396">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="eaca5-397">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="eaca5-398">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="eaca5-399">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="eaca5-399">Custom routes</span></span>

<span data-ttu-id="eaca5-400">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="eaca5-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="eaca5-401">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-401">Specify a custom route to a page.</span></span> <span data-ttu-id="eaca5-402">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="eaca5-403">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-403">Append segments to a page's default route.</span></span> <span data-ttu-id="eaca5-404">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="eaca5-405">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="eaca5-406">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="eaca5-407">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="eaca5-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="eaca5-408">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="eaca5-409">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="eaca5-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="eaca5-410">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a `@page` diretiva.</span><span class="sxs-lookup"><span data-stu-id="eaca5-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="eaca5-411">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="eaca5-412">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="eaca5-413">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="eaca5-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="eaca5-414">Configuração e configurações avançadas</span><span class="sxs-lookup"><span data-stu-id="eaca5-414">Advanced configuration and settings</span></span>

<span data-ttu-id="eaca5-415">A configuração e as configurações nas seções a seguir não são exigidas pela maioria dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="eaca5-416">Para configurar opções avançadas, use o método de extensão <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> :</span><span class="sxs-lookup"><span data-stu-id="eaca5-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="eaca5-417">Use o <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para definir o diretório raiz para páginas ou adicione convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="eaca5-418">Para obter mais informações sobre convenções, consulte [ Razor páginas convenções de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="eaca5-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="eaca5-419">Para pré-compilar exibições, consulte [ Razor Exibir compilação](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="eaca5-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="eaca5-420">Especificar que Razor as páginas estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="eaca5-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="eaca5-421">Por padrão, Razor as páginas têm raiz no diretório */pages*</span><span class="sxs-lookup"><span data-stu-id="eaca5-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="eaca5-422">Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que suas Razor páginas estão na [raiz do conteúdo](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="eaca5-423">Especificar que as Razor páginas estejam em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="eaca5-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="eaca5-424">Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que as Razor páginas estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="eaca5-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="eaca5-425">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="eaca5-425">Additional resources</span></span>

* <span data-ttu-id="eaca5-426">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="eaca5-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* <span data-ttu-id="eaca5-427">[Autorizar atributo e Razor páginas](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="eaca5-427">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* [<span data-ttu-id="eaca5-428">Baixar ou exibir código de exemplo</span><span class="sxs-lookup"><span data-stu-id="eaca5-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaca5-429">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="eaca5-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="eaca5-430">As páginas são um novo aspecto do ASP.NET Core MVC que torna mais fácil e produtivo os cenários voltados para a página de codificação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-430"> Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="eaca5-431">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="eaca5-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="eaca5-432">Este documento fornece uma introdução às Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="eaca5-433">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="eaca5-434">Se você encontrar algumas das seções muito avançadas, consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="eaca5-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="eaca5-435">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="eaca5-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eaca5-436">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="eaca5-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaca5-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaca5-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaca5-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaca5-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaca5-439">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eaca5-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="eaca5-440">Criar um Razor projeto de páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaca5-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaca5-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaca5-442">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um Razor projeto de páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaca5-443">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eaca5-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eaca5-444">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="eaca5-445">Abra o arquivo *.csproj* gerado do Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="eaca5-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaca5-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaca5-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eaca5-447">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="eaca5-448">Pages</span><span class="sxs-lookup"><span data-stu-id="eaca5-448"> Pages</span></span>

Razor<span data-ttu-id="eaca5-449">As páginas estão habilitadas no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-449"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="eaca5-450">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="eaca5-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="eaca5-451">O código anterior é muito parecido com um [ Razor arquivo de exibição](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="eaca5-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="eaca5-452">O que o torna diferentes é a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="eaca5-453">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="eaca5-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="eaca5-454">`@page`deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="eaca5-455">`@page`afeta o comportamento de outras Razor construções.</span><span class="sxs-lookup"><span data-stu-id="eaca5-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="eaca5-456">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="eaca5-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="eaca5-457">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="eaca5-458">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="eaca5-459">Por convenção, o `PageModel` arquivo de classe tem o mesmo nome que o Razor arquivo de paginação com *. cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="eaca5-460">Por exemplo, a Razor página anterior é *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="eaca5-461">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="eaca5-462">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="eaca5-463">A tabela a seguir mostra um Razor caminho de página e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="eaca5-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="eaca5-464">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="eaca5-464">File name and path</span></span>               | <span data-ttu-id="eaca5-465">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="eaca5-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eaca5-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="eaca5-467">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="eaca5-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="eaca5-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="eaca5-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="eaca5-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="eaca5-471">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="eaca5-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="eaca5-472">Observações:</span><span class="sxs-lookup"><span data-stu-id="eaca5-472">Notes:</span></span>

* <span data-ttu-id="eaca5-473">O tempo de execução procura por Razor arquivos de páginas na pasta *páginas* por padrão.</span><span class="sxs-lookup"><span data-stu-id="eaca5-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="eaca5-474">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="eaca5-475">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="eaca5-475">Write a basic form</span></span>

Razor<span data-ttu-id="eaca5-476">As páginas são projetadas para tornar os padrões comuns usados com navegadores da Web fáceis de implementar ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-476"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="eaca5-477">[Associação de modelo](xref:mvc/models/model-binding), [auxiliares de marca](xref:mvc/views/tag-helpers/intro)e auxiliares HTML *funcionam apenas* com as propriedades definidas em uma Razor classe de página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="eaca5-478">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="eaca5-479">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="eaca5-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="eaca5-480">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="eaca5-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="eaca5-481">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="eaca5-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="eaca5-482">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="eaca5-483">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="eaca5-484">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="eaca5-485">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="eaca5-486">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="eaca5-487">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="eaca5-487">This separation allows:</span></span>

* <span data-ttu-id="eaca5-488">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eaca5-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="eaca5-489">[Teste de unidade](xref:test/razor-pages-tests) das páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="eaca5-490">A página tem um  *método de manipulador*`OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="eaca5-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="eaca5-491">Você pode adicionar métodos de manipulador para qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="eaca5-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="eaca5-492">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="eaca5-492">The most common handlers are:</span></span>

* <span data-ttu-id="eaca5-493">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="eaca5-494">Amostra de [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="eaca5-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="eaca5-495">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="eaca5-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="eaca5-496">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="eaca5-497">O código anterior é típico para Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="eaca5-498">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="eaca5-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="eaca5-499">O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="eaca5-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="eaca5-500">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation), [validação](xref:mvc/models/validation)e resultados de ação, é compartilhada.</span><span class="sxs-lookup"><span data-stu-id="eaca5-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="eaca5-501">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="eaca5-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="eaca5-502">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="eaca5-503">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-503">Check for validation errors.</span></span>

* <span data-ttu-id="eaca5-504">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="eaca5-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="eaca5-505">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="eaca5-506">A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais.</span><span class="sxs-lookup"><span data-stu-id="eaca5-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="eaca5-507">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="eaca5-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="eaca5-508">Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="eaca5-509">`RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="eaca5-510">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eaca5-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="eaca5-511">`RedirectToPage`é detalhado na seção [geração de URL para páginas](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="eaca5-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="eaca5-512">Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="eaca5-513">`Page` retorna uma instância de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="eaca5-514">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="eaca5-515">`PageResult`é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="eaca5-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="eaca5-516">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="eaca5-517">A propriedade `Customer` usa o atributo `[BindProperty]` para aceitar o model binding.</span><span class="sxs-lookup"><span data-stu-id="eaca5-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor<span data-ttu-id="eaca5-518">As páginas, por padrão, associam propriedades somente a não `GET` verbos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-518"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="eaca5-519">A associação de propriedades pode reduzir a quantidade de código que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="eaca5-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="eaca5-520">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="eaca5-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="eaca5-521">A home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="eaca5-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="eaca5-522">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="eaca5-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="eaca5-523">O arquivo *cshtml* contém a marcação a seguir para criar um link de edição para cada contato:</span><span class="sxs-lookup"><span data-stu-id="eaca5-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="eaca5-524">O `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o `asp-route-{value}` atributo para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="eaca5-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="eaca5-525">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="eaca5-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="eaca5-526">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="eaca5-527">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="eaca5-528">Os Auxiliares de Marcação são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="eaca5-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="eaca5-529">O arquivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="eaca5-530">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="eaca5-531">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="eaca5-532">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="eaca5-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="eaca5-533">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="eaca5-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="eaca5-534">O arquivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="eaca5-535">O arquivo *Index.cshtml* também contém a marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="eaca5-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="eaca5-536">Quando o botão de exclusão é renderizado em HTML, seu `formaction` inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="eaca5-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="eaca5-537">A ID de contato do cliente especificada pelo atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="eaca5-538">O `handler` especificado pelo atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="eaca5-539">Este é um exemplo de um botão de exclusão renderizado com uma ID de contato do cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="eaca5-540">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="eaca5-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="eaca5-541">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="eaca5-542">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="eaca5-543">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="eaca5-544">O código a seguir mostra o `OnPostDeleteAsync` manipulador:</span><span class="sxs-lookup"><span data-stu-id="eaca5-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="eaca5-545">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="eaca5-546">Aceita o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="eaca5-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="eaca5-547">Se a diretiva de página *index. cshtml* continha a restrição `"{id:int?}"` de roteamento, ela `id` virá de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="eaca5-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="eaca5-548">Os dados de rota para `id` são especificados no URI, como `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="eaca5-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="eaca5-549">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="eaca5-550">Se o contato do cliente for encontrado, eles serão removidos da lista de contatos do cliente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="eaca5-551">O banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="eaca5-551">The database is updated.</span></span>
* <span data-ttu-id="eaca5-552">Chama `RedirectToPage` para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eaca5-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="eaca5-553">Marque as propriedades da página conforme necessário</span><span class="sxs-lookup"><span data-stu-id="eaca5-553">Mark page properties as required</span></span>

<span data-ttu-id="eaca5-554">As propriedades em um `PageModel` podem ser marcadas com o atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :</span><span class="sxs-lookup"><span data-stu-id="eaca5-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="eaca5-555">Para obter mais informações, confira [Validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="eaca5-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="eaca5-556">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="eaca5-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="eaca5-557">As solicitações `HEAD` permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="eaca5-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="eaca5-558">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="eaca5-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="eaca5-559">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="eaca5-560">No ASP.NET Core 2,1 ou posterior, as Razor páginas voltarão para chamar o `OnGet` manipulador se nenhum `OnHead` manipulador for definido.</span><span class="sxs-lookup"><span data-stu-id="eaca5-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="eaca5-561">Esse comportamento é habilitado pela chamada para [SetCompatibilityVersion](xref:mvc/compatibility-version) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="eaca5-562">Os modelos padrão geram a chamada `SetCompatibilityVersion` no ASP.NET Core 2.1 e 2.2.</span><span class="sxs-lookup"><span data-stu-id="eaca5-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="eaca5-563">`SetCompatibilityVersion`define efetivamente a Razor opção Pages `AllowMappingHeadRequestsToGetHandler` como `true` .</span><span class="sxs-lookup"><span data-stu-id="eaca5-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="eaca5-564">Em vez de aceitar todos os comportamentos com `SetCompatibilityVersion`, você pode aceitar explicitamente participar de comportamentos *específicos*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="eaca5-565">O código a seguir aceita permitir que solicitações `HEAD` sejam mapeadas para o manipulador `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="eaca5-566">XSRF/CSRF e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="eaca5-567">Você não precisa escrever nenhum código para [validação antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="eaca5-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="eaca5-568">A geração e a validação de tokens antifalsificação são incluídas automaticamente nas Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="eaca5-569">Usando layouts, parciais, modelos e auxiliares de marca com Razor páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="eaca5-570">As páginas funcionam com todos os recursos do Razor mecanismo de exibição.</span><span class="sxs-lookup"><span data-stu-id="eaca5-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="eaca5-571">Layouts, parciais, modelos, auxiliares de marca, *_ViewStart. cshtml*, *_ViewImports. cshtml* funcionam da mesma maneira que fazem para Razor exibições convencionais.</span><span class="sxs-lookup"><span data-stu-id="eaca5-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="eaca5-572">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="eaca5-573">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="eaca5-574">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="eaca5-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="eaca5-575">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="eaca5-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="eaca5-576">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="eaca5-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="eaca5-577">Veja [página de layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="eaca5-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="eaca5-578">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="eaca5-579">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="eaca5-580">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="eaca5-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="eaca5-581">Um layout na pasta *páginas/compartilhadas* pode ser usado em qualquer Razor página na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="eaca5-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="eaca5-582">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="eaca5-583">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="eaca5-584">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="eaca5-584">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="eaca5-585">As páginas destinam-se a contar com a hierarquia de pastas, não com convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="eaca5-585"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="eaca5-586">Exibir pesquisa de uma Razor página inclui a pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="eaca5-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="eaca5-587">Os layouts, modelos e parciais que você está usando com controladores MVC e Razor exibições convencionais *funcionam apenas*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="eaca5-588">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="eaca5-589">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="eaca5-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="eaca5-590">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="eaca5-591">Quando a diretiva `@namespace` é usada explicitamente em uma página:</span><span class="sxs-lookup"><span data-stu-id="eaca5-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="eaca5-592">A diretiva define o namespace da página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="eaca5-593">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="eaca5-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="eaca5-594">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="eaca5-595">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="eaca5-596">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="eaca5-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="eaca5-597">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="eaca5-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="eaca5-598">O namespace gerado para a página *pages/Customers/Edit. cshtml* Razor é o mesmo que a `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="eaca5-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="eaca5-599">`@namespace`*também funciona com Razor exibições convencionais.*</span><span class="sxs-lookup"><span data-stu-id="eaca5-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="eaca5-600">O arquivo de exibição *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="eaca5-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="eaca5-601">O arquivo de exibição *Pages/Create.cshtml* atualizado:</span><span class="sxs-lookup"><span data-stu-id="eaca5-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="eaca5-602">O [ Razor projeto inicial de páginas](#rpvs17) contém as *páginas/_ValidationScriptsPartial. cshtml*, que conectam a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="eaca5-603">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="eaca5-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="eaca5-604">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="eaca5-604">URL generation for Pages</span></span>

<span data-ttu-id="eaca5-605">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="eaca5-606">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="eaca5-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="eaca5-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="eaca5-607">*/Pages*</span></span>

  * <span data-ttu-id="eaca5-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="eaca5-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="eaca5-609">*/Customers*</span></span>

    * <span data-ttu-id="eaca5-610">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="eaca5-611">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="eaca5-612">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eaca5-612">*Index.cshtml*</span></span>

<span data-ttu-id="eaca5-613">As páginas *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* redirecionam para o *Pages/Index.cshtml* após êxito.</span><span class="sxs-lookup"><span data-stu-id="eaca5-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="eaca5-614">A cadeia de caracteres `/Index` faz parte do URI para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="eaca5-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="eaca5-615">A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="eaca5-616">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="eaca5-617">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="eaca5-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="eaca5-618">Os exemplos anteriores de geração de URL oferecem opções avançadas e recursos funcionais para codificar uma URL.</span><span class="sxs-lookup"><span data-stu-id="eaca5-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="eaca5-619">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="eaca5-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="eaca5-620">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="eaca5-621">A tabela a seguir mostra qual página de Índice é selecionada com diferentes parâmetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eaca5-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="eaca5-622">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="eaca5-622">RedirectToPage(x)</span></span>| <span data-ttu-id="eaca5-623">Página</span><span class="sxs-lookup"><span data-stu-id="eaca5-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eaca5-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="eaca5-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="eaca5-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-625">*Pages/Index*</span></span> |
| <span data-ttu-id="eaca5-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="eaca5-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="eaca5-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="eaca5-628">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="eaca5-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="eaca5-629">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-629">*Pages/Index*</span></span> |
| <span data-ttu-id="eaca5-630">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="eaca5-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="eaca5-631">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eaca5-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="eaca5-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="eaca5-633">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="eaca5-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="eaca5-634">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="eaca5-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="eaca5-635">Se você usar nomes relativos para vincular entre páginas em uma pasta, você poderá renomear essa pasta.</span><span class="sxs-lookup"><span data-stu-id="eaca5-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="eaca5-636">Todos os links ainda funcionarão (porque eles não incluirão o nome da pasta).</span><span class="sxs-lookup"><span data-stu-id="eaca5-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="eaca5-637">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="eaca5-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="eaca5-638">Para obter mais informações, consulte <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="eaca5-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="eaca5-639">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="eaca5-639">ViewData attribute</span></span>

<span data-ttu-id="eaca5-640">Os dados podem ser passados para uma página com [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="eaca5-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="eaca5-641">As propriedades em controladores ou Razor modelos de página com o `[ViewData]` atributo têm seus valores armazenados e carregados do [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="eaca5-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="eaca5-642">No exemplo a seguir, o `AboutModel` contém uma `Title` propriedade marcada com `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="eaca5-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="eaca5-643">A propriedade `Title` está definida como o título da página Sobre:</span><span class="sxs-lookup"><span data-stu-id="eaca5-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="eaca5-644">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="eaca5-645">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="eaca5-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="eaca5-646">TempData</span><span class="sxs-lookup"><span data-stu-id="eaca5-646">TempData</span></span>

<span data-ttu-id="eaca5-647">O ASP.NET Core expõe a propriedade [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) em um [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="eaca5-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="eaca5-648">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="eaca5-648">This property stores data until it's read.</span></span> <span data-ttu-id="eaca5-649">Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="eaca5-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="eaca5-650">`TempData` é útil para redirecionamento nos casos em que os dados são necessários para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="eaca5-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="eaca5-651">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="eaca5-652">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="eaca5-653">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="eaca5-654">Para obter mais informações, confira [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="eaca5-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="eaca5-655">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="eaca5-655">Multiple handlers per page</span></span>

<span data-ttu-id="eaca5-656">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="eaca5-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="eaca5-657">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="eaca5-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="eaca5-658">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="eaca5-659">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="eaca5-660">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="eaca5-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="eaca5-661">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="eaca5-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="eaca5-662">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="eaca5-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="eaca5-663">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="eaca5-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="eaca5-664">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="eaca5-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="eaca5-665">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="eaca5-666">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="eaca5-667">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="eaca5-668">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="eaca5-668">Custom routes</span></span>

<span data-ttu-id="eaca5-669">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="eaca5-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="eaca5-670">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-670">Specify a custom route to a page.</span></span> <span data-ttu-id="eaca5-671">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="eaca5-672">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-672">Append segments to a page's default route.</span></span> <span data-ttu-id="eaca5-673">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="eaca5-674">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="eaca5-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="eaca5-675">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="eaca5-676">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="eaca5-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="eaca5-677">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="eaca5-678">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="eaca5-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="eaca5-679">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a `@page` diretiva.</span><span class="sxs-lookup"><span data-stu-id="eaca5-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="eaca5-680">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="eaca5-681">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eaca5-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="eaca5-682">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="eaca5-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="eaca5-683">Configuração e definições</span><span class="sxs-lookup"><span data-stu-id="eaca5-683">Configuration and settings</span></span>

<span data-ttu-id="eaca5-684">Para configurar opções avançadas, use o método de extensão `AddRazorPagesOptions` no construtor de MVC:</span><span class="sxs-lookup"><span data-stu-id="eaca5-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="eaca5-685">No momento, você pode usar o `RazorPagesOptions` para definir o diretório raiz para páginas ou adicionar as convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="eaca5-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="eaca5-686">Permitiremos mais extensibilidade dessa maneira no futuro.</span><span class="sxs-lookup"><span data-stu-id="eaca5-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="eaca5-687">Para pré-compilar exibições, consulte [ Razor Exibir compilação](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="eaca5-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="eaca5-688">[Baixar ou exibir código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="eaca5-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="eaca5-689">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="eaca5-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="eaca5-690">Especificar que Razor as páginas estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="eaca5-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="eaca5-691">Por padrão, Razor as páginas têm raiz no diretório */pages*</span><span class="sxs-lookup"><span data-stu-id="eaca5-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="eaca5-692">Adicione [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que suas Razor páginas estão na [raiz do conteúdo](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="eaca5-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="eaca5-693">Especificar que as Razor páginas estejam em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="eaca5-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="eaca5-694">Adicione [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que suas Razor páginas estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="eaca5-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="eaca5-695">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="eaca5-695">Additional resources</span></span>

* <span data-ttu-id="eaca5-696">[Autorizar atributo e Razor páginas](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="eaca5-696">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
