---
title: Áreas no ASP.NET Core
author: rick-anderson
description: Saiba por que as áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace (para roteamento) e uma estrutura de pasta (para exibições) separados.
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625926"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="2297e-103">Áreas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2297e-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="2297e-104">Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2297e-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2297e-105">As áreas são um recurso ASP.NET usado para organizar funcionalidades relacionadas em um grupo separadamente:</span><span class="sxs-lookup"><span data-stu-id="2297e-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="2297e-106">Espaço de nome para roteamento.</span><span class="sxs-lookup"><span data-stu-id="2297e-106">Namespace for routing.</span></span>
* <span data-ttu-id="2297e-107">Estrutura de pastas para visualizações e páginas de barbear.</span><span class="sxs-lookup"><span data-stu-id="2297e-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="2297e-108">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action` ou a uma Razor Page, `page`.</span><span class="sxs-lookup"><span data-stu-id="2297e-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="2297e-109">As áreas proporcionam uma maneira de particionar um aplicativo Web do ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de Razor Pages, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="2297e-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="2297e-110">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="2297e-111">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="2297e-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="2297e-112">O runtime do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="2297e-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="2297e-113">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="2297e-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="2297e-114">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="2297e-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="2297e-115">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, Razor Pages e modelos.</span><span class="sxs-lookup"><span data-stu-id="2297e-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="2297e-116">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="2297e-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="2297e-117">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="2297e-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="2297e-118">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="2297e-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="2297e-119">[Exibir ou baixar o código de amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) [(como baixar).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2297e-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2297e-120">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="2297e-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="2297e-121">Se você estiver usando o Razor Pages, confira [Áreas com Razor Pages](#areas-with-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="2297e-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="2297e-122">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="2297e-122">Areas for controllers with views</span></span>

<span data-ttu-id="2297e-123">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2297e-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="2297e-124">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="2297e-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="2297e-125">Controladores com [`[Area]`](#attribute) o atributo para associar o controlador à área:</span><span class="sxs-lookup"><span data-stu-id="2297e-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="2297e-126">A [rota de área adicionada à inicialização](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="2297e-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="2297e-127">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="2297e-127">Area folder structure</span></span>

<span data-ttu-id="2297e-128">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços*.</span><span class="sxs-lookup"><span data-stu-id="2297e-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="2297e-129">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="2297e-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="2297e-130">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="2297e-130">Project name</span></span>
  * <span data-ttu-id="2297e-131">Áreas</span><span class="sxs-lookup"><span data-stu-id="2297e-131">Areas</span></span>
    * <span data-ttu-id="2297e-132">Produtos</span><span class="sxs-lookup"><span data-stu-id="2297e-132">Products</span></span>
      * <span data-ttu-id="2297e-133">Controladores</span><span class="sxs-lookup"><span data-stu-id="2297e-133">Controllers</span></span>
        * <span data-ttu-id="2297e-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2297e-134">HomeController.cs</span></span>
        * <span data-ttu-id="2297e-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="2297e-135">ManageController.cs</span></span>
      * <span data-ttu-id="2297e-136">Exibições</span><span class="sxs-lookup"><span data-stu-id="2297e-136">Views</span></span>
        * <span data-ttu-id="2297e-137">Home</span><span class="sxs-lookup"><span data-stu-id="2297e-137">Home</span></span>
          * <span data-ttu-id="2297e-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-138">Index.cshtml</span></span>
        * <span data-ttu-id="2297e-139">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="2297e-139">Manage</span></span>
          * <span data-ttu-id="2297e-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-140">Index.cshtml</span></span>
          * <span data-ttu-id="2297e-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-141">About.cshtml</span></span>
    * <span data-ttu-id="2297e-142">Serviços</span><span class="sxs-lookup"><span data-stu-id="2297e-142">Services</span></span>
      * <span data-ttu-id="2297e-143">Controladores</span><span class="sxs-lookup"><span data-stu-id="2297e-143">Controllers</span></span>
        * <span data-ttu-id="2297e-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2297e-144">HomeController.cs</span></span>
      * <span data-ttu-id="2297e-145">Exibições</span><span class="sxs-lookup"><span data-stu-id="2297e-145">Views</span></span>
        * <span data-ttu-id="2297e-146">Home</span><span class="sxs-lookup"><span data-stu-id="2297e-146">Home</span></span>
          * <span data-ttu-id="2297e-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-147">Index.cshtml</span></span>

<span data-ttu-id="2297e-148">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="2297e-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="2297e-149">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="2297e-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="2297e-150">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="2297e-150">Associate the controller with an Area</span></span>

<span data-ttu-id="2297e-151">Os controladores de área são designados com o atributo [ &lbrack;Área:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)</span><span class="sxs-lookup"><span data-stu-id="2297e-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="2297e-152">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="2297e-152">Add Area route</span></span>

<span data-ttu-id="2297e-153">As rotas de área normalmente usam [roteamento convencional](xref:mvc/controllers/routing#cr) em vez [de roteamento de atributos](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="2297e-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="2297e-154">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="2297e-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2297e-155">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="2297e-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2297e-156">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="2297e-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="2297e-157">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="2297e-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="2297e-158">Usando `{area:...}` `MapControllerRoute`com:</span><span class="sxs-lookup"><span data-stu-id="2297e-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="2297e-159">É o mecanismo menos complicado para adicionar roteamento para áreas.</span><span class="sxs-lookup"><span data-stu-id="2297e-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="2297e-160">Combina todos os `[Area("Area name")]` controladores com o atributo.</span><span class="sxs-lookup"><span data-stu-id="2297e-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="2297e-161">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="2297e-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="2297e-162">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="2297e-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="2297e-163">Geração de links com áreas do MVC</span><span class="sxs-lookup"><span data-stu-id="2297e-163">Link generation with MVC areas</span></span>

<span data-ttu-id="2297e-164">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="2297e-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2297e-165">O download da amostra inclui uma [exibição parcial](xref:mvc/views/partial) que contém:</span><span class="sxs-lookup"><span data-stu-id="2297e-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="2297e-166">Os links anteriores.</span><span class="sxs-lookup"><span data-stu-id="2297e-166">The preceding links.</span></span>
* <span data-ttu-id="2297e-167">Links semelhantes aos `area` anteriores, exceto não são especificados.</span><span class="sxs-lookup"><span data-stu-id="2297e-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="2297e-168">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="2297e-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2297e-169">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="2297e-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="2297e-170">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do [ambiente](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="2297e-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="2297e-171">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="2297e-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2297e-172">Em muitos casos para o aplicativo de amostra, o uso dos valores ambientais gera links incorretos com a marcação que não especifica a área.</span><span class="sxs-lookup"><span data-stu-id="2297e-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="2297e-173">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="2297e-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="2297e-174">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="2297e-175">Para compartilhar um layout comum para todo o aplicativo, mantenha o *_ViewStart.cshtml* na [pasta raiz](#arf)do aplicativo .</span><span class="sxs-lookup"><span data-stu-id="2297e-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="2297e-176">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2297e-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="2297e-177">Pasta raiz do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2297e-177">Application root folder</span></span>

<span data-ttu-id="2297e-178">A pasta raiz do aplicativo é a pasta que contém *Startup.cs* no aplicativo web criado com os modelos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2297e-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="2297e-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="2297e-180">*/Views/_ViewImports.cshtml*, para MVC e */Pages/_ViewImports.cshtml* for Razor Pages, não é importado para visualizações em áreas.</span><span class="sxs-lookup"><span data-stu-id="2297e-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="2297e-181">Use uma das seguintes abordagens para fornecer importações de visualização a todas as visualizações:</span><span class="sxs-lookup"><span data-stu-id="2297e-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="2297e-182">Adicione *_ViewImports.cshtml* à [pasta raiz](#arf)do aplicativo .</span><span class="sxs-lookup"><span data-stu-id="2297e-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="2297e-183">Um *_ViewImports.cshtml* na pasta raiz do aplicativo se aplicará a todas as visualizações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="2297e-184">Copie o arquivo *_ViewImports.cshtml* para a pasta de exibição apropriada em áreas.</span><span class="sxs-lookup"><span data-stu-id="2297e-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="2297e-185">O arquivo *_ViewImports.cshtml* normalmente contém `@using`importações `@inject` e instruções [do Tag Helpers.](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="2297e-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="2297e-186">Para obter mais informações, consulte [Importar Diretivas Compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2297e-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="2297e-187">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="2297e-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="2297e-188">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="2297e-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="2297e-189">Áreas com Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2297e-189">Areas with Razor Pages</span></span>

<span data-ttu-id="2297e-190">Áreas com Páginas `Areas/<area name>/Pages` de Navalha requerem uma pasta na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="2297e-191">A seguinte estrutura de pasta é usada com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="2297e-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="2297e-192">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="2297e-192">Project name</span></span>
  * <span data-ttu-id="2297e-193">Áreas</span><span class="sxs-lookup"><span data-stu-id="2297e-193">Areas</span></span>
    * <span data-ttu-id="2297e-194">Produtos</span><span class="sxs-lookup"><span data-stu-id="2297e-194">Products</span></span>
      * <span data-ttu-id="2297e-195">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="2297e-195">Pages</span></span>
        * <span data-ttu-id="2297e-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="2297e-196">_ViewImports</span></span>
        * <span data-ttu-id="2297e-197">Sobre o</span><span class="sxs-lookup"><span data-stu-id="2297e-197">About</span></span>
        * <span data-ttu-id="2297e-198">Índice</span><span class="sxs-lookup"><span data-stu-id="2297e-198">Index</span></span>
    * <span data-ttu-id="2297e-199">Serviços</span><span class="sxs-lookup"><span data-stu-id="2297e-199">Services</span></span>
      * <span data-ttu-id="2297e-200">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="2297e-200">Pages</span></span>
        * <span data-ttu-id="2297e-201">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="2297e-201">Manage</span></span>
          * <span data-ttu-id="2297e-202">Sobre o</span><span class="sxs-lookup"><span data-stu-id="2297e-202">About</span></span>
          * <span data-ttu-id="2297e-203">Índice</span><span class="sxs-lookup"><span data-stu-id="2297e-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="2297e-204">Geração de links com áreas e Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2297e-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="2297e-205">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="2297e-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2297e-206">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="2297e-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2297e-207">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="2297e-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2297e-208">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.</span><span class="sxs-lookup"><span data-stu-id="2297e-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="2297e-209">Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="2297e-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2297e-210">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="2297e-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2297e-211">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="2297e-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="2297e-212">Por exemplo, considere os links gerados a partir do código a seguir:</span><span class="sxs-lookup"><span data-stu-id="2297e-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="2297e-213">Para o código anterior:</span><span class="sxs-lookup"><span data-stu-id="2297e-213">For the preceding code:</span></span>

* <span data-ttu-id="2297e-214">O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`.</span><span class="sxs-lookup"><span data-stu-id="2297e-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="2297e-215">Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="2297e-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="2297e-216">O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.</span><span class="sxs-lookup"><span data-stu-id="2297e-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="2297e-217">O código vem do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="2297e-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="2297e-218">Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="2297e-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="2297e-219">Um arquivo *_ViewImports.cshtml* pode ser adicionado a cada pasta *Páginas* da área para importar o namespace e os Auxiliares de Marcação para cada Razor Page na pasta.</span><span class="sxs-lookup"><span data-stu-id="2297e-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="2297e-220">Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2297e-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2297e-221">A marcação a seguir mostra a Página do Razor */Services/Manage/About*:</span><span class="sxs-lookup"><span data-stu-id="2297e-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="2297e-222">Na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="2297e-222">In the preceding markup:</span></span>

* <span data-ttu-id="2297e-223">O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="2297e-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="2297e-224">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="2297e-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="2297e-225">No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2297e-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2297e-226">A marcação a seguir mostra a Página do Razor */Products/About*:</span><span class="sxs-lookup"><span data-stu-id="2297e-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="2297e-227">No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2297e-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="2297e-228">Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2297e-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="2297e-229">Layout compartilhado para Áreas do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2297e-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="2297e-230">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="2297e-231">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="2297e-231">Publishing Areas</span></span>

<span data-ttu-id="2297e-232">Todos os arquivos \*.cshtml e os arquivos do diretório *wwwroot* são publicados em uma saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` são incluídos no arquivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="2297e-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2297e-233">Áreas são um recurso do ASP.NET usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições).</span><span class="sxs-lookup"><span data-stu-id="2297e-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="2297e-234">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action` ou a uma Razor Page, `page`.</span><span class="sxs-lookup"><span data-stu-id="2297e-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="2297e-235">As áreas proporcionam uma maneira de particionar um aplicativo Web do ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de Razor Pages, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="2297e-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="2297e-236">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="2297e-237">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="2297e-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="2297e-238">O runtime do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="2297e-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="2297e-239">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="2297e-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="2297e-240">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="2297e-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="2297e-241">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, Razor Pages e modelos.</span><span class="sxs-lookup"><span data-stu-id="2297e-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="2297e-242">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="2297e-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="2297e-243">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="2297e-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="2297e-244">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="2297e-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="2297e-245">[Exibir ou baixar o código de amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) [(como baixar).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2297e-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2297e-246">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="2297e-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="2297e-247">Se você estiver usando o Razor Pages, confira [Áreas com Razor Pages](#areas-with-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="2297e-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="2297e-248">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="2297e-248">Areas for controllers with views</span></span>

<span data-ttu-id="2297e-249">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2297e-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="2297e-250">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="2297e-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="2297e-251">Controladores com [`[Area]`](#attribute) o atributo para associar o controlador à área:</span><span class="sxs-lookup"><span data-stu-id="2297e-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="2297e-252">A [rota de área adicionada à inicialização](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="2297e-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="2297e-253">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="2297e-253">Area folder structure</span></span>

<span data-ttu-id="2297e-254">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços*.</span><span class="sxs-lookup"><span data-stu-id="2297e-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="2297e-255">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="2297e-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="2297e-256">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="2297e-256">Project name</span></span>
  * <span data-ttu-id="2297e-257">Áreas</span><span class="sxs-lookup"><span data-stu-id="2297e-257">Areas</span></span>
    * <span data-ttu-id="2297e-258">Produtos</span><span class="sxs-lookup"><span data-stu-id="2297e-258">Products</span></span>
      * <span data-ttu-id="2297e-259">Controladores</span><span class="sxs-lookup"><span data-stu-id="2297e-259">Controllers</span></span>
        * <span data-ttu-id="2297e-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2297e-260">HomeController.cs</span></span>
        * <span data-ttu-id="2297e-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="2297e-261">ManageController.cs</span></span>
      * <span data-ttu-id="2297e-262">Exibições</span><span class="sxs-lookup"><span data-stu-id="2297e-262">Views</span></span>
        * <span data-ttu-id="2297e-263">Home</span><span class="sxs-lookup"><span data-stu-id="2297e-263">Home</span></span>
          * <span data-ttu-id="2297e-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-264">Index.cshtml</span></span>
        * <span data-ttu-id="2297e-265">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="2297e-265">Manage</span></span>
          * <span data-ttu-id="2297e-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-266">Index.cshtml</span></span>
          * <span data-ttu-id="2297e-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-267">About.cshtml</span></span>
    * <span data-ttu-id="2297e-268">Serviços</span><span class="sxs-lookup"><span data-stu-id="2297e-268">Services</span></span>
      * <span data-ttu-id="2297e-269">Controladores</span><span class="sxs-lookup"><span data-stu-id="2297e-269">Controllers</span></span>
        * <span data-ttu-id="2297e-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="2297e-270">HomeController.cs</span></span>
      * <span data-ttu-id="2297e-271">Exibições</span><span class="sxs-lookup"><span data-stu-id="2297e-271">Views</span></span>
        * <span data-ttu-id="2297e-272">Home</span><span class="sxs-lookup"><span data-stu-id="2297e-272">Home</span></span>
          * <span data-ttu-id="2297e-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-273">Index.cshtml</span></span>

<span data-ttu-id="2297e-274">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="2297e-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="2297e-275">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="2297e-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="2297e-276">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="2297e-276">Associate the controller with an Area</span></span>

<span data-ttu-id="2297e-277">Os controladores de área são designados com o atributo [ &lbrack;Área:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)</span><span class="sxs-lookup"><span data-stu-id="2297e-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="2297e-278">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="2297e-278">Add Area route</span></span>

<span data-ttu-id="2297e-279">Rotas de área normalmente usam o roteamento convencional, em vez de roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="2297e-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="2297e-280">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="2297e-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2297e-281">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="2297e-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2297e-282">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="2297e-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="2297e-283">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="2297e-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="2297e-284">Usar `{area:...}` é o mecanismo menos complicado para a adição de roteamento para áreas.</span><span class="sxs-lookup"><span data-stu-id="2297e-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="2297e-285">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="2297e-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="2297e-286">Ao usar `MapAreaRoute` com o ASP.NET Core 2.2, veja [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="2297e-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="2297e-287">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="2297e-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="2297e-288">Geração de links com áreas do MVC</span><span class="sxs-lookup"><span data-stu-id="2297e-288">Link generation with MVC areas</span></span>

<span data-ttu-id="2297e-289">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="2297e-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2297e-290">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="2297e-291">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="2297e-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2297e-292">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="2297e-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2297e-293">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="2297e-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="2297e-294">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="2297e-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2297e-295">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="2297e-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2297e-296">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="2297e-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="2297e-297">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="2297e-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="2297e-298">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="2297e-299">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="2297e-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="2297e-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="2297e-301">Em seu local padrão, */Views/_ViewImports.cshtml* não se aplica às áreas.</span><span class="sxs-lookup"><span data-stu-id="2297e-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="2297e-302">Para usar [o Comum Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using` `@inject` ou em sua área, certifique-se de que um arquivo *_ViewImports.cshtml* adequado [se aplique às suas exibições de área](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2297e-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="2297e-303">Se você quiser o mesmo comportamento em todas as suas exibições, mova */Views/_ViewImports.cshtml* para a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="2297e-304">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="2297e-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="2297e-305">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="2297e-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="2297e-306">Áreas com Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2297e-306">Areas with Razor Pages</span></span>

<span data-ttu-id="2297e-307">Áreas com Páginas `Areas/<area name>/Pages` de Navalha requerem uma pasta na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="2297e-308">A seguinte estrutura de pasta é usada com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="2297e-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="2297e-309">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="2297e-309">Project name</span></span>
  * <span data-ttu-id="2297e-310">Áreas</span><span class="sxs-lookup"><span data-stu-id="2297e-310">Areas</span></span>
    * <span data-ttu-id="2297e-311">Produtos</span><span class="sxs-lookup"><span data-stu-id="2297e-311">Products</span></span>
      * <span data-ttu-id="2297e-312">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="2297e-312">Pages</span></span>
        * <span data-ttu-id="2297e-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="2297e-313">_ViewImports</span></span>
        * <span data-ttu-id="2297e-314">Sobre o</span><span class="sxs-lookup"><span data-stu-id="2297e-314">About</span></span>
        * <span data-ttu-id="2297e-315">Índice</span><span class="sxs-lookup"><span data-stu-id="2297e-315">Index</span></span>
    * <span data-ttu-id="2297e-316">Serviços</span><span class="sxs-lookup"><span data-stu-id="2297e-316">Services</span></span>
      * <span data-ttu-id="2297e-317">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="2297e-317">Pages</span></span>
        * <span data-ttu-id="2297e-318">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="2297e-318">Manage</span></span>
          * <span data-ttu-id="2297e-319">Sobre o</span><span class="sxs-lookup"><span data-stu-id="2297e-319">About</span></span>
          * <span data-ttu-id="2297e-320">Índice</span><span class="sxs-lookup"><span data-stu-id="2297e-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="2297e-321">Geração de links com áreas e Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2297e-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="2297e-322">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="2297e-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="2297e-323">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="2297e-324">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="2297e-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="2297e-325">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="2297e-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="2297e-326">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.</span><span class="sxs-lookup"><span data-stu-id="2297e-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="2297e-327">Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="2297e-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="2297e-328">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="2297e-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2297e-329">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="2297e-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="2297e-330">Por exemplo, considere os links gerados a partir do código a seguir:</span><span class="sxs-lookup"><span data-stu-id="2297e-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="2297e-331">Para o código anterior:</span><span class="sxs-lookup"><span data-stu-id="2297e-331">For the preceding code:</span></span>

* <span data-ttu-id="2297e-332">O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`.</span><span class="sxs-lookup"><span data-stu-id="2297e-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="2297e-333">Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="2297e-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="2297e-334">O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.</span><span class="sxs-lookup"><span data-stu-id="2297e-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="2297e-335">O código vem do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="2297e-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="2297e-336">Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="2297e-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="2297e-337">Um arquivo *_ViewImports.cshtml* pode ser adicionado a cada pasta *Páginas* da área para importar o namespace e os Auxiliares de Marcação para cada Razor Page na pasta.</span><span class="sxs-lookup"><span data-stu-id="2297e-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="2297e-338">Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2297e-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2297e-339">A marcação a seguir mostra a Página do Razor */Services/Manage/About*:</span><span class="sxs-lookup"><span data-stu-id="2297e-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="2297e-340">Na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="2297e-340">In the preceding markup:</span></span>

* <span data-ttu-id="2297e-341">O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="2297e-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="2297e-342">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="2297e-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="2297e-343">No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2297e-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="2297e-344">A marcação a seguir mostra a Página do Razor */Products/About*:</span><span class="sxs-lookup"><span data-stu-id="2297e-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="2297e-345">No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2297e-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="2297e-346">Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="2297e-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="2297e-347">Layout compartilhado para Áreas do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2297e-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="2297e-348">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2297e-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="2297e-349">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="2297e-349">Publishing Areas</span></span>

<span data-ttu-id="2297e-350">Todos os arquivos \*.cshtml e os arquivos do diretório *wwwroot* são publicados em uma saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` são incluídos no arquivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="2297e-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
