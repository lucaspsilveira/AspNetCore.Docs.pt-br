---
title: Pacote e minify ativos estáticos em ASP.NET Core
author: scottaddie
description: Aprenda a otimizar recursos estáticos em uma ASP.NET aplicativo web Core aplicando técnicas de agrupamento e minificação.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
uid: client-side/bundling-and-minification
ms.openlocfilehash: 670ac6a96c3affd2b2ac699836f536aea7d85ff3
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488683"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="b016e-103">Pacote e minify ativos estáticos em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b016e-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="b016e-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="b016e-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="b016e-105">Este artigo explica os benefícios da aplicação de agrupamento e minificação, incluindo como esses recursos podem ser usados com ASP.NET aplicativos web Core.</span><span class="sxs-lookup"><span data-stu-id="b016e-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="b016e-106">O que é agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="b016e-106">What is bundling and minification</span></span>

<span data-ttu-id="b016e-107">Agrupamento e minificação são duas otimizações de desempenho distintas que você pode aplicar em um aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="b016e-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="b016e-108">Usados em conjunto, o agrupamento e a minificação melhoram o desempenho, reduzindo o número de solicitações de servidores e reduzindo o tamanho dos ativos estáticos solicitados.</span><span class="sxs-lookup"><span data-stu-id="b016e-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="b016e-109">Agrupamento e minificação melhoram principalmente o tempo de carga de solicitação da primeira página.</span><span class="sxs-lookup"><span data-stu-id="b016e-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="b016e-110">Uma vez solicitada uma página da Web, o navegador armazena os ativos estáticos (JavaScript, CSS e imagens).</span><span class="sxs-lookup"><span data-stu-id="b016e-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="b016e-111">Consequentemente, o agrupamento e a minificação não melhoram o desempenho ao solicitar a mesma página, ou páginas, no mesmo site solicitando os mesmos ativos.</span><span class="sxs-lookup"><span data-stu-id="b016e-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="b016e-112">Se o cabeçalho expirar não for definido corretamente nos ativos e se o agrupamento e a minificação não forem usados, a heurística de frescor do navegador marca os ativos obsoletos após alguns dias.</span><span class="sxs-lookup"><span data-stu-id="b016e-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="b016e-113">Além disso, o navegador requer uma solicitação de validação para cada ativo.</span><span class="sxs-lookup"><span data-stu-id="b016e-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="b016e-114">Neste caso, o agrupamento e a minificação proporcionam uma melhoria de desempenho mesmo após a solicitação da primeira página.</span><span class="sxs-lookup"><span data-stu-id="b016e-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="b016e-115">Agrupamento</span><span class="sxs-lookup"><span data-stu-id="b016e-115">Bundling</span></span>

<span data-ttu-id="b016e-116">O agrupamento combina vários arquivos em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="b016e-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="b016e-117">O agrupamento reduz o número de solicitações de servidor necessárias para renderizar um ativo da Web, como uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="b016e-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="b016e-118">Você pode criar qualquer número de pacotes individuais especificamente para CSS, JavaScript, etc. Menos arquivos significa menos solicitações HTTP do navegador para o servidor ou do serviço que fornece seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b016e-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="b016e-119">Isso resulta em um melhor desempenho de carga da primeira página.</span><span class="sxs-lookup"><span data-stu-id="b016e-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="b016e-120">Minificação</span><span class="sxs-lookup"><span data-stu-id="b016e-120">Minification</span></span>

<span data-ttu-id="b016e-121">A minificação remove caracteres desnecessários do código sem alterar a funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="b016e-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="b016e-122">O resultado é uma redução significativa de tamanho nos ativos solicitados (como arquivos CSS, imagens e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b016e-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="b016e-123">Os efeitos colaterais comuns da minificação incluem encurtar nomes variáveis para um caractere e remover comentários e espaço em branco desnecessário.</span><span class="sxs-lookup"><span data-stu-id="b016e-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="b016e-124">Considere a seguinte função JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b016e-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="b016e-125">A minificação reduz a função ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="b016e-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="b016e-126">Além de remover os comentários e o espaço em branco desnecessário, os seguintes parâmetros e nomes variáveis foram renomeados da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="b016e-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="b016e-127">Original</span><span class="sxs-lookup"><span data-stu-id="b016e-127">Original</span></span> | <span data-ttu-id="b016e-128">Renomeado</span><span class="sxs-lookup"><span data-stu-id="b016e-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="b016e-129">Impacto do agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="b016e-129">Impact of bundling and minification</span></span>

<span data-ttu-id="b016e-130">A tabela a seguir descreve diferenças entre carregar ativos individualmente e usar agrupamento e minificação:</span><span class="sxs-lookup"><span data-stu-id="b016e-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="b016e-131">Ação</span><span class="sxs-lookup"><span data-stu-id="b016e-131">Action</span></span> | <span data-ttu-id="b016e-132">Com B/M</span><span class="sxs-lookup"><span data-stu-id="b016e-132">With B/M</span></span> | <span data-ttu-id="b016e-133">Sem B/M</span><span class="sxs-lookup"><span data-stu-id="b016e-133">Without B/M</span></span> | <span data-ttu-id="b016e-134">Alterar</span><span class="sxs-lookup"><span data-stu-id="b016e-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="b016e-135">Solicitações de arquivos</span><span class="sxs-lookup"><span data-stu-id="b016e-135">File Requests</span></span>  | <span data-ttu-id="b016e-136">7</span><span class="sxs-lookup"><span data-stu-id="b016e-136">7</span></span>   | <span data-ttu-id="b016e-137">18</span><span class="sxs-lookup"><span data-stu-id="b016e-137">18</span></span>     | <span data-ttu-id="b016e-138">157%</span><span class="sxs-lookup"><span data-stu-id="b016e-138">157%</span></span>
<span data-ttu-id="b016e-139">KB transferido</span><span class="sxs-lookup"><span data-stu-id="b016e-139">KB Transferred</span></span> | <span data-ttu-id="b016e-140">156</span><span class="sxs-lookup"><span data-stu-id="b016e-140">156</span></span> | <span data-ttu-id="b016e-141">264.68</span><span class="sxs-lookup"><span data-stu-id="b016e-141">264.68</span></span> | <span data-ttu-id="b016e-142">70%</span><span class="sxs-lookup"><span data-stu-id="b016e-142">70%</span></span>
<span data-ttu-id="b016e-143">Tempo de carga (ms)</span><span class="sxs-lookup"><span data-stu-id="b016e-143">Load Time (ms)</span></span> | <span data-ttu-id="b016e-144">885</span><span class="sxs-lookup"><span data-stu-id="b016e-144">885</span></span> | <span data-ttu-id="b016e-145">2360</span><span class="sxs-lookup"><span data-stu-id="b016e-145">2360</span></span>   | <span data-ttu-id="b016e-146">167%</span><span class="sxs-lookup"><span data-stu-id="b016e-146">167%</span></span>

<span data-ttu-id="b016e-147">Os navegadores são bastante verbosos em relação aos cabeçalhos de solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b016e-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="b016e-148">A métrica total de bytes enviados viu uma redução significativa ao agrupamento.</span><span class="sxs-lookup"><span data-stu-id="b016e-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="b016e-149">O tempo de carga mostra uma melhora significativa, no entanto este exemplo correu localmente.</span><span class="sxs-lookup"><span data-stu-id="b016e-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="b016e-150">Maiores ganhos de desempenho são realizados ao usar agrupamento e minificação com ativos transferidos por uma rede.</span><span class="sxs-lookup"><span data-stu-id="b016e-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="b016e-151">Escolha uma estratégia de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="b016e-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="b016e-152">Os modelos de projeto MVC e Razor Pages fornecem uma solução para agrupamento e minificação que consiste em um arquivo de configuração JSON.</span><span class="sxs-lookup"><span data-stu-id="b016e-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="b016e-153">Ferramentas de terceiros, como o corredor de tarefas [Grunt,](xref:client-side/using-grunt) realizam as mesmas tarefas com um pouco mais de complexidade.</span><span class="sxs-lookup"><span data-stu-id="b016e-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="b016e-154">Uma ferramenta de terceiros é um ótimo ajuste quando seu fluxo de&mdash;trabalho de desenvolvimento requer processamento além de agrupamento e minificação, como fiação e otimização de imagem.</span><span class="sxs-lookup"><span data-stu-id="b016e-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="b016e-155">Usando o agrupamento e a minificação do tempo de projeto, os arquivos minified são criados antes da implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b016e-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="b016e-156">Agrupar e minificar antes da implantação fornece a vantagem da carga reduzida do servidor.</span><span class="sxs-lookup"><span data-stu-id="b016e-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="b016e-157">No entanto, é importante reconhecer que o agrupamento e a minificação do tempo de projeto aumentam a complexidade da compilação e só funciona com arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="b016e-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="b016e-158">Configurar agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="b016e-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="b016e-159">Em ASP.NET Core 2.0 ou anterior, os modelos de projeto MVC e Razor Pages fornecem um arquivo de configuração *bundleconfig.json* que define as opções para cada pacote:</span><span class="sxs-lookup"><span data-stu-id="b016e-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b016e-160">Em ASP.NET Core 2.1 ou posterior, adicione um novo arquivo JSON, chamado *bundleconfig.json,* à raiz do projeto MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="b016e-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="b016e-161">Inclua o seguinte JSON nesse arquivo como ponto de partida:</span><span class="sxs-lookup"><span data-stu-id="b016e-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="b016e-162">O arquivo *bundleconfig.json* define as opções para cada pacote.</span><span class="sxs-lookup"><span data-stu-id="b016e-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="b016e-163">No exemplo anterior, uma configuração de pacote único é definida para os arquivos JavaScript personalizado *(wwwroot/js/site.js)* e stylesheet *(wwwroot/css/site.css).*</span><span class="sxs-lookup"><span data-stu-id="b016e-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="b016e-164">As opções de configuração incluem:</span><span class="sxs-lookup"><span data-stu-id="b016e-164">Configuration options include:</span></span>

* <span data-ttu-id="b016e-165">`outputFileName`: O nome do arquivo de pacote para saída.</span><span class="sxs-lookup"><span data-stu-id="b016e-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="b016e-166">Pode conter um caminho relativo a partir do arquivo *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="b016e-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="b016e-167">**Necessário**</span><span class="sxs-lookup"><span data-stu-id="b016e-167">**required**</span></span>
* <span data-ttu-id="b016e-168">`inputFiles`: Uma matriz de arquivos para agrupar.</span><span class="sxs-lookup"><span data-stu-id="b016e-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="b016e-169">Estes são caminhos relativos para o arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="b016e-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="b016e-170">**opcional**, \*um valor vazio resulta em um arquivo de saída vazio.</span><span class="sxs-lookup"><span data-stu-id="b016e-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="b016e-171">[padrões de globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) são suportados.</span><span class="sxs-lookup"><span data-stu-id="b016e-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="b016e-172">`minify`: As opções de minificação para o tipo de saída.</span><span class="sxs-lookup"><span data-stu-id="b016e-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="b016e-173">**opcional,** *padrão `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="b016e-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="b016e-174">As opções de configuração estão disponíveis por tipo de arquivo de saída.</span><span class="sxs-lookup"><span data-stu-id="b016e-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="b016e-175">MinisterisCS</span><span class="sxs-lookup"><span data-stu-id="b016e-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="b016e-176">Ministerífica JavaScript</span><span class="sxs-lookup"><span data-stu-id="b016e-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="b016e-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="b016e-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="b016e-178">`includeInProject`: Sinalizador indicando se deve adicionar arquivos gerados ao arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="b016e-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="b016e-179">**opcional**, *padrão - falso*</span><span class="sxs-lookup"><span data-stu-id="b016e-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="b016e-180">`sourceMap`: Bandeira indicando se deve gerar um mapa de origem para o arquivo empacotado.</span><span class="sxs-lookup"><span data-stu-id="b016e-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="b016e-181">**opcional**, *padrão - falso*</span><span class="sxs-lookup"><span data-stu-id="b016e-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="b016e-182">`sourceMapRootPath`: O caminho raiz para armazenar o arquivo do mapa de origem gerado.</span><span class="sxs-lookup"><span data-stu-id="b016e-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="b016e-183">Adicionar arquivos ao fluxo de trabalho</span><span class="sxs-lookup"><span data-stu-id="b016e-183">Add files to workflow</span></span>

<span data-ttu-id="b016e-184">Considere um exemplo no qual um arquivo *custom.css* adicional é adicionado semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="b016e-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="b016e-185">Para minify *custom.css* e empacotá-lo com *site.css* em um arquivo *site.min.css,* adicione o caminho relativo ao *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="b016e-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="b016e-186">Alternativamente, o seguinte padrão de globbing poderia ser usado:</span><span class="sxs-lookup"><span data-stu-id="b016e-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="b016e-187">Este padrão globbing corresponde a todos os arquivos CSS e exclui o padrão de arquivo minizado.</span><span class="sxs-lookup"><span data-stu-id="b016e-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="b016e-188">Construa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b016e-188">Build the application.</span></span> <span data-ttu-id="b016e-189">Abra *o site.min.css* e observe que o conteúdo do *custom.css* é anexado ao final do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b016e-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="b016e-190">Agrupamento e minificação baseados em ambiente</span><span class="sxs-lookup"><span data-stu-id="b016e-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="b016e-191">Como uma prática recomendada, os arquivos empacotados e minizados do seu aplicativo devem ser usados em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="b016e-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="b016e-192">Durante o desenvolvimento, os arquivos originais facilitam a depuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b016e-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="b016e-193">Especifique quais arquivos incluir em suas páginas usando o [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) em suas exibições.</span><span class="sxs-lookup"><span data-stu-id="b016e-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="b016e-194">O Environment Tag Helper só renderiza seu conteúdo ao ser executado em [ambientes](xref:fundamentals/environments)específicos.</span><span class="sxs-lookup"><span data-stu-id="b016e-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b016e-195">A `environment` tag a seguir renderiza os arquivos CSS `Development` não processados ao ser executado no ambiente:</span><span class="sxs-lookup"><span data-stu-id="b016e-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="b016e-196">A `environment` tag a seguir renderiza os arquivos CSS empacotados `Development`e minizados ao ser executado em um ambiente diferente de .</span><span class="sxs-lookup"><span data-stu-id="b016e-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="b016e-197">Por exemplo, `Production` a `Staging` execução ou aciona a renderização dessas folhas de estilos:</span><span class="sxs-lookup"><span data-stu-id="b016e-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="b016e-198">Consumir bundleconfig.json da Gulp</span><span class="sxs-lookup"><span data-stu-id="b016e-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="b016e-199">Há casos em que o fluxo de trabalho de agrupamento e minificação de um aplicativo requer processamento adicional.</span><span class="sxs-lookup"><span data-stu-id="b016e-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="b016e-200">Exemplos incluem otimização de imagem, rebentação de cache e processamento de ativos de CDN.</span><span class="sxs-lookup"><span data-stu-id="b016e-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="b016e-201">Para satisfazer esses requisitos, você pode converter o fluxo de trabalho de agrupamento e minificação para usar o Gulp.</span><span class="sxs-lookup"><span data-stu-id="b016e-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="b016e-202">Converta manualmente o fluxo de trabalho de agrupamento e minificação para usar o Gulp</span><span class="sxs-lookup"><span data-stu-id="b016e-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="b016e-203">Adicione um *arquivo package.json,* com o seguinte `devDependencies`, à raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="b016e-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="b016e-204">O `gulp-uglify` módulo não suporta ECMAScript (ES) 2015 / ES6 e posterior.</span><span class="sxs-lookup"><span data-stu-id="b016e-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="b016e-205">Instale [gulp-terser](https://www.npmjs.com/package/gulp-terser) em vez de `gulp-uglify` usar ES2015 / ES6 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="b016e-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="b016e-206">Instale as dependências executando o seguinte comando no mesmo nível *que package.json*:</span><span class="sxs-lookup"><span data-stu-id="b016e-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="b016e-207">Instale o Gulp CLI como uma dependência global:</span><span class="sxs-lookup"><span data-stu-id="b016e-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="b016e-208">Copie o arquivo *gulpfile.js* abaixo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="b016e-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="b016e-209">Executar tarefas de Gulp</span><span class="sxs-lookup"><span data-stu-id="b016e-209">Run Gulp tasks</span></span>

<span data-ttu-id="b016e-210">Para ativar a tarefa de minificação gulp antes que o projeto seja construído no Visual Studio, adicione o seguinte [Alvo mSBuild](/visualstudio/msbuild/msbuild-targets) ao arquivo \*.csproj:</span><span class="sxs-lookup"><span data-stu-id="b016e-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="b016e-211">Neste exemplo, todas as `MyPreCompileTarget` tarefas definidas `Build` dentro do destino são executadas antes do destino predefinido.</span><span class="sxs-lookup"><span data-stu-id="b016e-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="b016e-212">Saída semelhante à seguinte aparece na janela Saída do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b016e-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="b016e-213">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b016e-213">Additional resources</span></span>

* [<span data-ttu-id="b016e-214">Usar o Grunt</span><span class="sxs-lookup"><span data-stu-id="b016e-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="b016e-215">Usar vários ambientes</span><span class="sxs-lookup"><span data-stu-id="b016e-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="b016e-216">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="b016e-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
