---
title: Parte 3, adicionar uma exibição a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Parte 3 da série de tutoriais no ASP.NET Core MVC.
ms.author: riande
ms.date: 8/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 3976ecba1a6bbf30c3147bf1cef78c5ced5bec62
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652829"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="81b49-103">Parte 3, adicionar uma exibição a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="81b49-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="81b49-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81b49-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81b49-105">Nesta seção, você modifica a `HelloWorldController` classe para usar os [Razor](xref:mvc/views/razor) arquivos de exibição para encapsular corretamente o processo de geração de respostas HTML para um cliente.</span><span class="sxs-lookup"><span data-stu-id="81b49-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="81b49-106">Você cria um arquivo de modelo de exibição usando Razor .</span><span class="sxs-lookup"><span data-stu-id="81b49-106">You create a view template file using Razor.</span></span> Razor<span data-ttu-id="81b49-107">os modelos de exibição baseados em são uma extensão de arquivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="81b49-107">-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="81b49-108">Eles fornecem uma maneira elegante de criar a saída HTML com o C#.</span><span class="sxs-lookup"><span data-stu-id="81b49-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="81b49-109">Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="81b49-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="81b49-110">Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="81b49-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="81b49-111">O código anterior chama o método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> do controlador.</span><span class="sxs-lookup"><span data-stu-id="81b49-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="81b49-112">Ele usa um modelo de exibição para gerar uma resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="81b49-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="81b49-113">Métodos do controlador (também conhecidos como *métodos de ação*), como o método `Index` acima, geralmente retornam um <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), não um tipo como `string`.</span><span class="sxs-lookup"><span data-stu-id="81b49-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="81b49-114">Adicionar uma exibição</span><span class="sxs-lookup"><span data-stu-id="81b49-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81b49-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81b49-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81b49-116">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="81b49-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="81b49-117">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="81b49-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="81b49-118">Na caixa de diálogo **Adicionar Novo Item – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="81b49-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="81b49-119">Na caixa de pesquisa no canto superior direito, insira *exibição*</span><span class="sxs-lookup"><span data-stu-id="81b49-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="81b49-120">Selecionar \*\* Razor exibição\*\*</span><span class="sxs-lookup"><span data-stu-id="81b49-120">Select **Razor View**</span></span>

  * <span data-ttu-id="81b49-121">Mantenha o valor da caixa **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="81b49-122">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="81b49-122">Select **Add**</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="81b49-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81b49-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="81b49-125">Adicione uma exibição `Index` ao `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="81b49-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="81b49-126">Adicione uma nova pasta chamada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="81b49-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="81b49-127">Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81b49-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="81b49-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="81b49-129">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="81b49-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="81b49-130">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="81b49-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="81b49-131">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="81b49-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="81b49-132">Selecione **ASP .NET Core** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="81b49-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="81b49-133">Selecione a **página exibição do MVC** no painel central.</span><span class="sxs-lookup"><span data-stu-id="81b49-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="81b49-134">Digite *index* na caixa **nome** .</span><span class="sxs-lookup"><span data-stu-id="81b49-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="81b49-135">Selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="81b49-135">Select **New**.</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="81b49-137">Substitua o conteúdo do arquivo de exibição *exibições/HelloWorld/index. cshtml* Razor pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="81b49-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="81b49-138">Navegue até `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="81b49-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="81b49-139">O método `Index` no `HelloWorldController` não fez muita coisa: ele executou a instrução `return View();`, que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="81b49-140">Como um nome de arquivo de modo de exibição não foi especificado, o MVC é padronizado para usar o arquivo de exibição padrão.</span><span class="sxs-lookup"><span data-stu-id="81b49-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="81b49-141">O arquivo de exibição padrão tem o mesmo nome que o método ( `Index` ), portanto, o modelo de exibição em */views/HelloWorld/index.cshtml* é usado.</span><span class="sxs-lookup"><span data-stu-id="81b49-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="81b49-142">A imagem abaixo mostra a cadeia de caracteres “Olá de nosso modelo de exibição!”</span><span class="sxs-lookup"><span data-stu-id="81b49-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="81b49-143">embutida em código na exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-143">hard-coded in the view.</span></span>

![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="81b49-145">Alterar exibições e páginas de layout</span><span class="sxs-lookup"><span data-stu-id="81b49-145">Change views and layout pages</span></span>

<span data-ttu-id="81b49-146">Selecione os links de menu (**MvcMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="81b49-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="81b49-147">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="81b49-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="81b49-148">O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="81b49-149">Abra o arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="81b49-150">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="81b49-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="81b49-151">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="81b49-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="81b49-152">`RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="81b49-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="81b49-153">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="81b49-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="81b49-154">Alterar o título, o rodapé e o link de menu no arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="81b49-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="81b49-155">Substitua o conteúdo do arquivo *views/Shared/_Layout. cshtml* pela marcação a seguir.</span><span class="sxs-lookup"><span data-stu-id="81b49-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="81b49-156">As alterações são realçadas:</span><span class="sxs-lookup"><span data-stu-id="81b49-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="81b49-157">A marcação anterior fez as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="81b49-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="81b49-158">3 ocorrências de `MvcMovie` para `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="81b49-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="81b49-159">O elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="81b49-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="81b49-160">Na marcação anterior, o valor do atributo e o  [atributo do Auxiliar de Marca de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)`asp-area=""` foram omitidos porque este aplicativo não está usando [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="81b49-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="81b49-161">**Observação**: o `Movies` controlador não foi implementado.</span><span class="sxs-lookup"><span data-stu-id="81b49-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="81b49-162">Neste ponto, o link `Movie App` não está funcionando.</span><span class="sxs-lookup"><span data-stu-id="81b49-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="81b49-163">Salve suas alterações e selecione o link **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="81b49-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="81b49-164">Observe como o título na guia do navegador agora exibe **Política de Privacidade – Aplicativo de filme**, em vez de **Política de Privacidade – Filme MVC**:</span><span class="sxs-lookup"><span data-stu-id="81b49-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="81b49-166">Selecione o link **Página Inicial** e observe que o texto do título e de âncora também exibem **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="81b49-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="81b49-167">Conseguimos fazer a alteração uma vez no modelo de layout e fazer com que todas as páginas no site refletissem o novo texto do link e o novo título.</span><span class="sxs-lookup"><span data-stu-id="81b49-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="81b49-168">Examine o arquivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="81b49-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="81b49-169">O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="81b49-170">A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.</span><span class="sxs-lookup"><span data-stu-id="81b49-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="81b49-171">Altere o título e o elemento `<h2>` do arquivo de exibição *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="81b49-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="81b49-172">O título e o elemento `<h2>` são ligeiramente diferentes para que possa ver qual parte do código altera a exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="81b49-173">`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”.</span><span class="sxs-lookup"><span data-stu-id="81b49-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="81b49-174">A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:</span><span class="sxs-lookup"><span data-stu-id="81b49-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="81b49-175">Salve as alterações e navegue para `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="81b49-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="81b49-176">Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados.</span><span class="sxs-lookup"><span data-stu-id="81b49-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="81b49-177">(Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido.</span><span class="sxs-lookup"><span data-stu-id="81b49-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="81b49-178">Pressione CTRL + F5 em seu navegador para forçar a resposta do servidor a ser carregado.) O título do navegador é criado com o `ViewData["Title"]` que definimos no modelo de exibição *index. cshtml* e o "-Movie app" adicional adicionado no arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="81b49-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="81b49-179">O conteúdo do modelo de exibição *Index.cshtml* é mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="81b49-180">Uma única resposta HTML é enviada ao navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="81b49-181">Os modelos de layout facilitam a realização de alterações que se aplicam a todas as páginas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="81b49-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="81b49-182">Para saber mais, consulte [layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="81b49-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="81b49-184">Apesar disso, nossos poucos “dados” (nesse caso, a mensagem “Olá de nosso modelo de exibição!”)</span><span class="sxs-lookup"><span data-stu-id="81b49-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="81b49-185">são embutidos em código.</span><span class="sxs-lookup"><span data-stu-id="81b49-185">message) is hard-coded, though.</span></span> <span data-ttu-id="81b49-186">O aplicativo MVC tem um “V” (exibição) e você tem um “C” (controlador), mas ainda nenhum “M” (modelo).</span><span class="sxs-lookup"><span data-stu-id="81b49-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="81b49-187">Passando dados do controlador para a exibição</span><span class="sxs-lookup"><span data-stu-id="81b49-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="81b49-188">As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="81b49-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="81b49-189">Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="81b49-190">O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="81b49-191">Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="81b49-192">Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta.</span><span class="sxs-lookup"><span data-stu-id="81b49-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="81b49-193">Uma melhor prática: modelos de exibição **não** devem executar a lógica de negócios nem interagir diretamente com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="81b49-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="81b49-194">Em vez disso, um modelo de exibição deve funcionar somente com os dados fornecidos pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="81b49-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="81b49-195">Manter essa “separação de preocupações” ajuda a manter o código limpo, testável e com capacidade de manutenção.</span><span class="sxs-lookup"><span data-stu-id="81b49-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="81b49-196">Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="81b49-197">Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="81b49-198">O modelo de exibição gera uma resposta dinâmica, o que significa que é necessário passar bits de dados apropriados do controlador para a exibição para gerar a resposta.</span><span class="sxs-lookup"><span data-stu-id="81b49-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="81b49-199">Faça isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um dicionário `ViewData` que pode ser acessado em seguida pelo modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="81b49-200">Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="81b49-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="81b49-201">O dicionário `ViewData` é um objeto dinâmico, o que significa que qualquer tipo pode ser usado. O objeto `ViewData` não tem nenhuma propriedade definida até que você insira algo nele.</span><span class="sxs-lookup"><span data-stu-id="81b49-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="81b49-202">O sistema de [model binding](xref:mvc/models/model-binding) MVC mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="81b49-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="81b49-203">O arquivo *HelloWorldController.cs* completo tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="81b49-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="81b49-204">O objeto de dicionário `ViewData` contém dados que serão passados para a exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="81b49-205">Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="81b49-206">Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="81b49-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="81b49-207">Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="81b49-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="81b49-208">Salve as alterações e navegue para a seguinte URL:</span><span class="sxs-lookup"><span data-stu-id="81b49-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="81b49-209">Os dados são obtidos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="81b49-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="81b49-210">O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="81b49-211">Em seguida, a exibição renderiza os dados como HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-211">The view then renders the data as HTML to the browser.</span></span>

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="81b49-213">No exemplo acima, o dicionário `ViewData` foi usado para passar dados do controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="81b49-214">Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="81b49-215">A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem do dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="81b49-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="81b49-216">Confira mais informações em [Quando usar ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="81b49-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="81b49-217">No próximo tutorial, será criado um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="81b49-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="81b49-218">[Anterior](adding-controller.md) 
>  [Avançar](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="81b49-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81b49-219">Nesta seção, você modifica a `HelloWorldController` classe para usar os [Razor](xref:mvc/views/razor) arquivos de exibição para encapsular corretamente o processo de geração de respostas HTML para um cliente.</span><span class="sxs-lookup"><span data-stu-id="81b49-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="81b49-220">Você cria um arquivo de modelo de exibição usando Razor .</span><span class="sxs-lookup"><span data-stu-id="81b49-220">You create a view template file using Razor.</span></span> Razor<span data-ttu-id="81b49-221">os modelos de exibição baseados em são uma extensão de arquivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="81b49-221">-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="81b49-222">Eles fornecem uma maneira elegante de criar a saída HTML com o C#.</span><span class="sxs-lookup"><span data-stu-id="81b49-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="81b49-223">Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="81b49-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="81b49-224">Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="81b49-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="81b49-225">O código anterior chama o método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> do controlador.</span><span class="sxs-lookup"><span data-stu-id="81b49-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="81b49-226">Ele usa um modelo de exibição para gerar uma resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="81b49-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="81b49-227">Métodos do controlador (também conhecidos como *métodos de ação*), como o método `Index` acima, geralmente retornam um <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), não um tipo como `string`.</span><span class="sxs-lookup"><span data-stu-id="81b49-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="81b49-228">Adicionar uma exibição</span><span class="sxs-lookup"><span data-stu-id="81b49-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81b49-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81b49-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81b49-230">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="81b49-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="81b49-231">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="81b49-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="81b49-232">Na caixa de diálogo **Adicionar Novo Item – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="81b49-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="81b49-233">Na caixa de pesquisa no canto superior direito, insira *exibição*</span><span class="sxs-lookup"><span data-stu-id="81b49-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="81b49-234">Selecionar \*\* Razor exibição\*\*</span><span class="sxs-lookup"><span data-stu-id="81b49-234">Select **Razor View**</span></span>

  * <span data-ttu-id="81b49-235">Mantenha o valor da caixa **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="81b49-236">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="81b49-236">Select **Add**</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="81b49-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81b49-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="81b49-239">Adicione uma exibição `Index` ao `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="81b49-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="81b49-240">Adicione uma nova pasta chamada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="81b49-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="81b49-241">Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81b49-242">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="81b49-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="81b49-243">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="81b49-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="81b49-244">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="81b49-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="81b49-245">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="81b49-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="81b49-246">Selecione **Web** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="81b49-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="81b49-247">Selecione **Arquivo HTML vazio** no painel central.</span><span class="sxs-lookup"><span data-stu-id="81b49-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="81b49-248">Digite *Index.cshtml* na caixa **Nome**.</span><span class="sxs-lookup"><span data-stu-id="81b49-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="81b49-249">Selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="81b49-249">Select **New**.</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="81b49-251">Substitua o conteúdo do arquivo de exibição *exibições/HelloWorld/index. cshtml* Razor pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="81b49-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="81b49-252">Navegue até `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="81b49-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="81b49-253">O método `Index` no `HelloWorldController` não fez muita coisa: ele executou a instrução `return View();`, que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="81b49-254">Como um nome de arquivo de modo de exibição não foi especificado, o MVC é padronizado para usar o arquivo de exibição padrão.</span><span class="sxs-lookup"><span data-stu-id="81b49-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="81b49-255">O arquivo de exibição padrão tem o mesmo nome que o método (`Index`), portanto, é usado */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="81b49-256">A imagem abaixo mostra a cadeia de caracteres “Olá de nosso modelo de exibição!”</span><span class="sxs-lookup"><span data-stu-id="81b49-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="81b49-257">embutida em código na exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-257">hard-coded in the view.</span></span>

![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="81b49-259">Alterar exibições e páginas de layout</span><span class="sxs-lookup"><span data-stu-id="81b49-259">Change views and layout pages</span></span>

<span data-ttu-id="81b49-260">Selecione os links de menu (**MvcMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="81b49-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="81b49-261">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="81b49-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="81b49-262">O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="81b49-263">Abra o arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="81b49-264">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="81b49-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="81b49-265">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="81b49-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="81b49-266">`RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="81b49-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="81b49-267">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="81b49-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="81b49-268">Alterar o título, o rodapé e o link de menu no arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="81b49-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="81b49-269">Nos elementos de título e de rodapé, altere `MvcMovie` para `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="81b49-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="81b49-270">Altere o elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="81b49-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="81b49-271">A seguinte marcação mostra as alterações realçadas:</span><span class="sxs-lookup"><span data-stu-id="81b49-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="81b49-272">Na marcação anterior, o `asp-area` [atributo do Auxiliar de Marca de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) foi omitido porque este aplicativo não está usando [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="81b49-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="81b49-273">**Observação**: o `Movies` controlador não foi implementado.</span><span class="sxs-lookup"><span data-stu-id="81b49-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="81b49-274">Neste ponto, o link `Movie App` não está funcionando.</span><span class="sxs-lookup"><span data-stu-id="81b49-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="81b49-275">Salve suas alterações e selecione o link **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="81b49-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="81b49-276">Observe como o título na guia do navegador agora exibe **Política de Privacidade – Aplicativo de filme**, em vez de **Política de Privacidade – Filme MVC**:</span><span class="sxs-lookup"><span data-stu-id="81b49-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="81b49-278">Selecione o link **Página Inicial** e observe que o texto do título e de âncora também exibem **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="81b49-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="81b49-279">Conseguimos fazer a alteração uma vez no modelo de layout e fazer com que todas as páginas no site refletissem o novo texto do link e o novo título.</span><span class="sxs-lookup"><span data-stu-id="81b49-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="81b49-280">Examine o arquivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="81b49-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="81b49-281">O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="81b49-282">A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.</span><span class="sxs-lookup"><span data-stu-id="81b49-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="81b49-283">Altere o título e o elemento `<h2>` do arquivo de exibição *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="81b49-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="81b49-284">O título e o elemento `<h2>` são ligeiramente diferentes para que possa ver qual parte do código altera a exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="81b49-285">`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”.</span><span class="sxs-lookup"><span data-stu-id="81b49-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="81b49-286">A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:</span><span class="sxs-lookup"><span data-stu-id="81b49-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="81b49-287">Salve as alterações e navegue para `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="81b49-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="81b49-288">Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados.</span><span class="sxs-lookup"><span data-stu-id="81b49-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="81b49-289">(Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido.</span><span class="sxs-lookup"><span data-stu-id="81b49-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="81b49-290">Pressione CTRL + F5 em seu navegador para forçar a resposta do servidor a ser carregado.) O título do navegador é criado com o `ViewData["Title"]` que definimos no modelo de exibição *index. cshtml* e o "-Movie app" adicional adicionado no arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="81b49-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="81b49-291">Observe também como o conteúdo no modelo de exibição *Index.cshtml* foi mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml* e uma única resposta HTML foi enviada para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="81b49-292">Os modelos de layout facilitam realmente a realização de alterações que se aplicam a todas as páginas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="81b49-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="81b49-293">Para saber mais, consulte [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="81b49-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="81b49-295">Apesar disso, nossos poucos “dados” (nesse caso, a mensagem “Olá de nosso modelo de exibição!”)</span><span class="sxs-lookup"><span data-stu-id="81b49-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="81b49-296">são embutidos em código.</span><span class="sxs-lookup"><span data-stu-id="81b49-296">message) is hard-coded, though.</span></span> <span data-ttu-id="81b49-297">O aplicativo MVC tem um “V” (exibição) e você tem um “C” (controlador), mas ainda nenhum “M” (modelo).</span><span class="sxs-lookup"><span data-stu-id="81b49-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="81b49-298">Passando dados do controlador para a exibição</span><span class="sxs-lookup"><span data-stu-id="81b49-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="81b49-299">As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="81b49-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="81b49-300">Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="81b49-301">O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="81b49-302">Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="81b49-303">Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta.</span><span class="sxs-lookup"><span data-stu-id="81b49-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="81b49-304">Uma melhor prática: modelos de exibição **não** devem executar a lógica de negócios nem interagir diretamente com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="81b49-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="81b49-305">Em vez disso, um modelo de exibição deve funcionar somente com os dados fornecidos pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="81b49-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="81b49-306">Manter essa “separação de preocupações” ajuda a manter o código limpo, testável e com capacidade de manutenção.</span><span class="sxs-lookup"><span data-stu-id="81b49-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="81b49-307">Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="81b49-308">Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="81b49-309">O modelo de exibição gera uma resposta dinâmica, o que significa que é necessário passar bits de dados apropriados do controlador para a exibição para gerar a resposta.</span><span class="sxs-lookup"><span data-stu-id="81b49-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="81b49-310">Faça isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um dicionário `ViewData` que pode ser acessado em seguida pelo modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="81b49-311">Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="81b49-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="81b49-312">O dicionário `ViewData` é um objeto dinâmico, o que significa que qualquer tipo pode ser usado. O objeto `ViewData` não tem nenhuma propriedade definida até que você insira algo nele.</span><span class="sxs-lookup"><span data-stu-id="81b49-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="81b49-313">O sistema de [model binding](xref:mvc/models/model-binding) MVC mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="81b49-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="81b49-314">O arquivo *HelloWorldController.cs* completo tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="81b49-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="81b49-315">O objeto de dicionário `ViewData` contém dados que serão passados para a exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="81b49-316">Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81b49-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="81b49-317">Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="81b49-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="81b49-318">Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="81b49-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="81b49-319">Salve as alterações e navegue para a seguinte URL:</span><span class="sxs-lookup"><span data-stu-id="81b49-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="81b49-320">Os dados são obtidos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="81b49-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="81b49-321">O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="81b49-322">Em seguida, a exibição renderiza os dados como HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="81b49-322">The view then renders the data as HTML to the browser.</span></span>

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="81b49-324">No exemplo acima, o dicionário `ViewData` foi usado para passar dados do controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="81b49-325">Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="81b49-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="81b49-326">A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem do dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="81b49-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="81b49-327">Confira mais informações em [Quando usar ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="81b49-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="81b49-328">No próximo tutorial, será criado um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="81b49-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="81b49-329">[Anterior](adding-controller.md) 
>  [Avançar](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="81b49-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
