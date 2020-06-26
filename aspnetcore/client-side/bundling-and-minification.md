---
title: Empacotar e reduzirr ativos estáticos no ASP.NET Core
author: scottaddie
description: Saiba como otimizar recursos estáticos em um aplicativo Web ASP.NET Core aplicando técnicas de agrupamento e minificação.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: de7c155189008e1f78bfb1eba062fcc86f9e4839
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401903"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="52dd6-103">Empacotar e reduzirr ativos estáticos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52dd6-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="52dd6-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [David Pinheiro](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="52dd6-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="52dd6-105">Este artigo explica os benefícios da aplicação de agrupamento e minificação, incluindo como esses recursos podem ser usados com ASP.NET Core aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="52dd6-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="52dd6-106">O que é agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="52dd6-106">What is bundling and minification</span></span>

<span data-ttu-id="52dd6-107">O agrupamento e o minificação são duas otimizações de desempenho distintas que você pode aplicar em um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="52dd6-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="52dd6-108">Usados juntos, o agrupamento e o minificação melhoram o desempenho, reduzindo o número de solicitações do servidor e reduzindo o tamanho dos ativos estáticos solicitados.</span><span class="sxs-lookup"><span data-stu-id="52dd6-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="52dd6-109">O agrupamento e o minificação melhoram principalmente o tempo de carregamento da solicitação da primeira página.</span><span class="sxs-lookup"><span data-stu-id="52dd6-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="52dd6-110">Depois que uma página da Web for solicitada, o navegador armazenará em cache os ativos estáticos (JavaScript, CSS e imagens).</span><span class="sxs-lookup"><span data-stu-id="52dd6-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="52dd6-111">Consequentemente, o agrupamento e o minificação não melhoram o desempenho ao solicitar a mesma página, ou páginas, no mesmo site que solicita os mesmos ativos.</span><span class="sxs-lookup"><span data-stu-id="52dd6-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="52dd6-112">Se o cabeçalho Expires não estiver definido corretamente nos ativos e se o agrupamento e o minificação não forem usados, a heurística de atualização do navegador marcará os ativos como obsoletos após alguns dias.</span><span class="sxs-lookup"><span data-stu-id="52dd6-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="52dd6-113">Além disso, o navegador requer uma solicitação de validação para cada ativo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="52dd6-114">Nesse caso, o agrupamento e o minificação fornecem uma melhoria de desempenho mesmo após a primeira solicitação de página.</span><span class="sxs-lookup"><span data-stu-id="52dd6-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="52dd6-115">Reagrupamento</span><span class="sxs-lookup"><span data-stu-id="52dd6-115">Bundling</span></span>

<span data-ttu-id="52dd6-116">O agrupamento combina vários arquivos em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="52dd6-117">O agrupamento reduz o número de solicitações de servidor necessárias para processar um ativo da Web, como uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="52dd6-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="52dd6-118">Você pode criar qualquer número de grupos individuais especificamente para CSS, JavaScript, etc. Menos arquivos significa menos solicitações HTTP do navegador para o servidor ou do serviço que fornece seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="52dd6-119">Isso resulta em um melhor desempenho de carregamento de página.</span><span class="sxs-lookup"><span data-stu-id="52dd6-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="52dd6-120">Minificação</span><span class="sxs-lookup"><span data-stu-id="52dd6-120">Minification</span></span>

<span data-ttu-id="52dd6-121">Minificação remove caracteres desnecessários do código sem alterar a funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="52dd6-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="52dd6-122">O resultado é uma redução significativa de tamanho em ativos solicitados (como CSS, imagens e arquivos JavaScript).</span><span class="sxs-lookup"><span data-stu-id="52dd6-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="52dd6-123">Os efeitos colaterais comuns de minificação incluem a redução de nomes de variáveis para um caractere e a remoção de comentários e espaço em branco desnecessário.</span><span class="sxs-lookup"><span data-stu-id="52dd6-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="52dd6-124">Considere a seguinte função JavaScript:</span><span class="sxs-lookup"><span data-stu-id="52dd6-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="52dd6-125">Minificação reduz a função para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="52dd6-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="52dd6-126">Além de remover os comentários e o espaço em branco desnecessário, os seguintes parâmetros e nomes de variáveis foram renomeados da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="52dd6-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="52dd6-127">Original</span><span class="sxs-lookup"><span data-stu-id="52dd6-127">Original</span></span> | <span data-ttu-id="52dd6-128">Renomeado</span><span class="sxs-lookup"><span data-stu-id="52dd6-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="52dd6-129">Impacto do agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="52dd6-129">Impact of bundling and minification</span></span>

<span data-ttu-id="52dd6-130">A tabela a seguir descreve as diferenças entre os ativos de carregamento individualmente e o uso de agrupamento e minificação:</span><span class="sxs-lookup"><span data-stu-id="52dd6-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="52dd6-131">Ação</span><span class="sxs-lookup"><span data-stu-id="52dd6-131">Action</span></span> | <span data-ttu-id="52dd6-132">Com B/M</span><span class="sxs-lookup"><span data-stu-id="52dd6-132">With B/M</span></span> | <span data-ttu-id="52dd6-133">Sem B/M</span><span class="sxs-lookup"><span data-stu-id="52dd6-133">Without B/M</span></span> | <span data-ttu-id="52dd6-134">Alteração</span><span class="sxs-lookup"><span data-stu-id="52dd6-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="52dd6-135">Solicitações de arquivo</span><span class="sxs-lookup"><span data-stu-id="52dd6-135">File Requests</span></span>  | <span data-ttu-id="52dd6-136">7</span><span class="sxs-lookup"><span data-stu-id="52dd6-136">7</span></span>   | <span data-ttu-id="52dd6-137">18</span><span class="sxs-lookup"><span data-stu-id="52dd6-137">18</span></span>     | <span data-ttu-id="52dd6-138">157%</span><span class="sxs-lookup"><span data-stu-id="52dd6-138">157%</span></span>
<span data-ttu-id="52dd6-139">KB transferidos</span><span class="sxs-lookup"><span data-stu-id="52dd6-139">KB Transferred</span></span> | <span data-ttu-id="52dd6-140">156</span><span class="sxs-lookup"><span data-stu-id="52dd6-140">156</span></span> | <span data-ttu-id="52dd6-141">264,68</span><span class="sxs-lookup"><span data-stu-id="52dd6-141">264.68</span></span> | <span data-ttu-id="52dd6-142">70%</span><span class="sxs-lookup"><span data-stu-id="52dd6-142">70%</span></span>
<span data-ttu-id="52dd6-143">Tempo de carregamento (MS)</span><span class="sxs-lookup"><span data-stu-id="52dd6-143">Load Time (ms)</span></span> | <span data-ttu-id="52dd6-144">885</span><span class="sxs-lookup"><span data-stu-id="52dd6-144">885</span></span> | <span data-ttu-id="52dd6-145">2360</span><span class="sxs-lookup"><span data-stu-id="52dd6-145">2360</span></span>   | <span data-ttu-id="52dd6-146">167%</span><span class="sxs-lookup"><span data-stu-id="52dd6-146">167%</span></span>

<span data-ttu-id="52dd6-147">Os navegadores são bastante detalhados em relação aos cabeçalhos de solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="52dd6-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="52dd6-148">A métrica total de bytes enviados observou uma redução significativa durante o agrupamento.</span><span class="sxs-lookup"><span data-stu-id="52dd6-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="52dd6-149">O tempo de carregamento mostra uma melhoria significativa, no entanto, este exemplo foi executado localmente.</span><span class="sxs-lookup"><span data-stu-id="52dd6-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="52dd6-150">São obtidos maiores ganhos de desempenho ao usar agrupamento e minificação com ativos transferidos por uma rede.</span><span class="sxs-lookup"><span data-stu-id="52dd6-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="52dd6-151">Escolher uma estratégia de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="52dd6-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="52dd6-152">Os modelos de Razor projeto MVC e pages fornecem uma solução para Agrupamento e minificação que consistem em um arquivo de configuração JSON.</span><span class="sxs-lookup"><span data-stu-id="52dd6-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="52dd6-153">Ferramentas de terceiros, como o executor de tarefas do [Grunt](xref:client-side/using-grunt) , realizam as mesmas tarefas com um pouco mais de complexidade.</span><span class="sxs-lookup"><span data-stu-id="52dd6-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="52dd6-154">Uma ferramenta de terceiros é uma ótima opção quando seu fluxo de trabalho de desenvolvimento requer processamento além do agrupamento e minificação &mdash; , como a otimização de imagem e de retratação.</span><span class="sxs-lookup"><span data-stu-id="52dd6-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="52dd6-155">Usando agrupamento e minificação de tempo de design, os arquivos reduzidos são criados antes da implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="52dd6-156">O agrupamento e o minificar antes da implantação proporcionam a vantagem da carga reduzida do servidor.</span><span class="sxs-lookup"><span data-stu-id="52dd6-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="52dd6-157">No entanto, é importante reconhecer que o agrupamento de tempo de design e minificação aumenta a complexidade da compilação e só funciona com arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="52dd6-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="52dd6-158">Configurar agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="52dd6-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="52dd6-159">No ASP.NET Core 2,0 ou anterior, os modelos de Razor projeto MVC e pages fornecem um *bundleconfig.jsno* arquivo de configuração que define as opções para cada pacote:</span><span class="sxs-lookup"><span data-stu-id="52dd6-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="52dd6-160">No ASP.NET Core 2,1 ou posterior, adicione um novo arquivo JSON, chamado *bundleconfig.jsno*, à raiz do projeto MVC ou de Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="52dd6-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="52dd6-161">Inclua o seguinte JSON nesse arquivo como um ponto de partida:</span><span class="sxs-lookup"><span data-stu-id="52dd6-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="52dd6-162">O *bundleconfig.jsno* arquivo define as opções para cada pacote.</span><span class="sxs-lookup"><span data-stu-id="52dd6-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="52dd6-163">No exemplo anterior, uma única configuração de pacote é definida para os arquivos JavaScript (*wwwroot/js/site.js*) e folha de estilo (*wwwroot/CSS/site. css*) personalizados.</span><span class="sxs-lookup"><span data-stu-id="52dd6-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="52dd6-164">As opções de configuração incluem:</span><span class="sxs-lookup"><span data-stu-id="52dd6-164">Configuration options include:</span></span>

* <span data-ttu-id="52dd6-165">`outputFileName`: O nome do arquivo de pacote para saída.</span><span class="sxs-lookup"><span data-stu-id="52dd6-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="52dd6-166">Pode conter um caminho relativo do *bundleconfig.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="52dd6-167">**Necessário**</span><span class="sxs-lookup"><span data-stu-id="52dd6-167">**required**</span></span>
* <span data-ttu-id="52dd6-168">`inputFiles`: Uma matriz de arquivos para agrupar.</span><span class="sxs-lookup"><span data-stu-id="52dd6-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="52dd6-169">Esses são caminhos relativos ao arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="52dd6-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="52dd6-170">**opcional**, \* um valor vazio resulta em um arquivo de saída vazio.</span><span class="sxs-lookup"><span data-stu-id="52dd6-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="52dd6-171">Há suporte para padrões de [mascaramento](https://www.tldp.org/LDP/abs/html/globbingref.html) .</span><span class="sxs-lookup"><span data-stu-id="52dd6-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="52dd6-172">`minify`: As opções de minificação para o tipo de saída.</span><span class="sxs-lookup"><span data-stu-id="52dd6-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="52dd6-173">**opcional**, \*padrão- `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="52dd6-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="52dd6-174">As opções de configuração estão disponíveis por tipo de arquivo de saída.</span><span class="sxs-lookup"><span data-stu-id="52dd6-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="52dd6-175">Minificador CSS</span><span class="sxs-lookup"><span data-stu-id="52dd6-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="52dd6-176">Minificador JavaScript</span><span class="sxs-lookup"><span data-stu-id="52dd6-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="52dd6-177">Minificador HTML</span><span class="sxs-lookup"><span data-stu-id="52dd6-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="52dd6-178">`includeInProject`: Sinalizador que indica se os arquivos gerados devem ser adicionados ao arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="52dd6-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="52dd6-179">**opcional**, *padrão-falso*</span><span class="sxs-lookup"><span data-stu-id="52dd6-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="52dd6-180">`sourceMap`: Sinalizador que indica se um mapa de origem deve ser gerado para o arquivo agrupado.</span><span class="sxs-lookup"><span data-stu-id="52dd6-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="52dd6-181">**opcional**, *padrão-falso*</span><span class="sxs-lookup"><span data-stu-id="52dd6-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="52dd6-182">`sourceMapRootPath`: O caminho raiz para armazenar o arquivo de mapa de origem gerado.</span><span class="sxs-lookup"><span data-stu-id="52dd6-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="52dd6-183">Adicionar arquivos ao fluxo de trabalho</span><span class="sxs-lookup"><span data-stu-id="52dd6-183">Add files to workflow</span></span>

<span data-ttu-id="52dd6-184">Considere um exemplo no qual um arquivo *. CSS personalizado* adicional é adicionado semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="52dd6-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="52dd6-185">Para reduzir *Custom. css* e agrupá-lo com *site. css* em um arquivo *site. min. css* , adicione o caminho relativo para *bundleconfig.jsem*:</span><span class="sxs-lookup"><span data-stu-id="52dd6-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="52dd6-186">Como alternativa, o seguinte padrão de mascaramento poderia ser usado:</span><span class="sxs-lookup"><span data-stu-id="52dd6-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="52dd6-187">Esse padrão de mascaramento corresponde a todos os arquivos CSS e exclui o padrão de arquivo reduzidos.</span><span class="sxs-lookup"><span data-stu-id="52dd6-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="52dd6-188">Construa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-188">Build the application.</span></span> <span data-ttu-id="52dd6-189">Abra *site. min. css* e observe que o conteúdo de *Custom. css* é acrescentado ao final do arquivo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="52dd6-190">Agrupamento baseado em ambiente e minificação</span><span class="sxs-lookup"><span data-stu-id="52dd6-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="52dd6-191">Como prática recomendada, os arquivos agrupados e reduzidos do seu aplicativo devem ser usados em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="52dd6-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="52dd6-192">Durante o desenvolvimento, os arquivos originais tornam a depuração mais fácil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52dd6-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="52dd6-193">Especifique quais arquivos incluir em suas páginas usando o auxiliar de [marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) em suas exibições.</span><span class="sxs-lookup"><span data-stu-id="52dd6-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="52dd6-194">O auxiliar de marca de ambiente só renderiza seu conteúdo quando executado em [ambientes](xref:fundamentals/environments)específicos.</span><span class="sxs-lookup"><span data-stu-id="52dd6-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="52dd6-195">A marca a seguir `environment` renderiza os arquivos CSS não processados durante a execução no `Development` ambiente:</span><span class="sxs-lookup"><span data-stu-id="52dd6-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="52dd6-196">A marca a seguir `environment` renderiza os arquivos CSS agrupados e reduzidos quando executados em um ambiente diferente de `Development` .</span><span class="sxs-lookup"><span data-stu-id="52dd6-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="52dd6-197">Por exemplo, em execução `Production` ou `Staging` dispara a renderização dessas folhas de estilo:</span><span class="sxs-lookup"><span data-stu-id="52dd6-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="52dd6-198">Consumir bundleconfig.jsde Gulp</span><span class="sxs-lookup"><span data-stu-id="52dd6-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="52dd6-199">Há casos em que o fluxo de trabalho de agrupamento e minificação de um aplicativo requer processamento adicional.</span><span class="sxs-lookup"><span data-stu-id="52dd6-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="52dd6-200">Os exemplos incluem otimização de imagem, de cache e processamento de ativos CDN.</span><span class="sxs-lookup"><span data-stu-id="52dd6-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="52dd6-201">Para atender a esses requisitos, você pode converter o agrupamento e o fluxo de trabalho minificação para usar o Gulp.</span><span class="sxs-lookup"><span data-stu-id="52dd6-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="52dd6-202">Converter manualmente o agrupamento e o fluxo de trabalho minificação para usar Gulp</span><span class="sxs-lookup"><span data-stu-id="52dd6-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="52dd6-203">Adicione um *package.jsno* arquivo, com o seguinte `devDependencies` , à raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="52dd6-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="52dd6-204">O `gulp-uglify` módulo não dá suporte a ECMAScript (es) 2015/ES6 e posterior.</span><span class="sxs-lookup"><span data-stu-id="52dd6-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="52dd6-205">Instale o [Gulp-terser](https://www.npmjs.com/package/gulp-terser) em vez de `gulp-uglify` usar o ES2015/ES6 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="52dd6-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="52dd6-206">Instale as dependências executando o seguinte comando no mesmo nível que *package.jsem*:</span><span class="sxs-lookup"><span data-stu-id="52dd6-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="52dd6-207">Instale a CLI do Gulp como uma dependência global:</span><span class="sxs-lookup"><span data-stu-id="52dd6-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="52dd6-208">Copie o arquivo de *gulpfile.js* abaixo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="52dd6-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="52dd6-209">Executar tarefas do Gulp</span><span class="sxs-lookup"><span data-stu-id="52dd6-209">Run Gulp tasks</span></span>

<span data-ttu-id="52dd6-210">Para disparar a tarefa Gulp minificação antes que o projeto seja compilado no Visual Studio, adicione o seguinte [destino do MSBuild](/visualstudio/msbuild/msbuild-targets) ao arquivo \*. csproj:</span><span class="sxs-lookup"><span data-stu-id="52dd6-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="52dd6-211">Neste exemplo, todas as tarefas definidas no `MyPreCompileTarget` destino são executadas antes do destino predefinido `Build` .</span><span class="sxs-lookup"><span data-stu-id="52dd6-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="52dd6-212">Uma saída semelhante à seguinte aparece na janela de saída do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="52dd6-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="52dd6-213">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="52dd6-213">Additional resources</span></span>

* [<span data-ttu-id="52dd6-214">Usar o Grunt</span><span class="sxs-lookup"><span data-stu-id="52dd6-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="52dd6-215">Usar vários ambientes</span><span class="sxs-lookup"><span data-stu-id="52dd6-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="52dd6-216">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="52dd6-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
