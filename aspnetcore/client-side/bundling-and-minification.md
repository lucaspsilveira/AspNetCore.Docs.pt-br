---
title: Empacotar e reduzirr ativos estáticos no ASP.NET Core
author: scottaddie
description: Saiba como otimizar recursos estáticos em um aplicativo Web ASP.NET Core aplicando técnicas de agrupamento e minificação.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: 4523ba299d5c5e50a442f84acadf06bf57c69c5d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770947"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Empacotar e reduzirr ativos estáticos no ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) e [David Pinheiro](https://twitter.com/davidpine7)

Este artigo explica os benefícios da aplicação de agrupamento e minificação, incluindo como esses recursos podem ser usados com ASP.NET Core aplicativos Web.

## <a name="what-is-bundling-and-minification"></a>O que é agrupamento e minificação

O agrupamento e o minificação são duas otimizações de desempenho distintas que você pode aplicar em um aplicativo Web. Usados juntos, o agrupamento e o minificação melhoram o desempenho, reduzindo o número de solicitações do servidor e reduzindo o tamanho dos ativos estáticos solicitados.

O agrupamento e o minificação melhoram principalmente o tempo de carregamento da solicitação da primeira página. Depois que uma página da Web for solicitada, o navegador armazenará em cache os ativos estáticos (JavaScript, CSS e imagens). Consequentemente, o agrupamento e o minificação não melhoram o desempenho ao solicitar a mesma página, ou páginas, no mesmo site que solicita os mesmos ativos. Se o cabeçalho Expires não estiver definido corretamente nos ativos e se o agrupamento e o minificação não forem usados, a heurística de atualização do navegador marcará os ativos como obsoletos após alguns dias. Além disso, o navegador requer uma solicitação de validação para cada ativo. Nesse caso, o agrupamento e o minificação fornecem uma melhoria de desempenho mesmo após a primeira solicitação de página.

### <a name="bundling"></a>Reagrupamento

O agrupamento combina vários arquivos em um único arquivo. O agrupamento reduz o número de solicitações de servidor necessárias para processar um ativo da Web, como uma página da Web. Você pode criar qualquer número de grupos individuais especificamente para CSS, JavaScript, etc. Menos arquivos significa menos solicitações HTTP do navegador para o servidor ou do serviço que fornece seu aplicativo. Isso resulta em um melhor desempenho de carregamento de página.

### <a name="minification"></a>Minificação

Minificação remove caracteres desnecessários do código sem alterar a funcionalidade. O resultado é uma redução significativa de tamanho em ativos solicitados (como CSS, imagens e arquivos JavaScript). Os efeitos colaterais comuns de minificação incluem a redução de nomes de variáveis para um caractere e a remoção de comentários e espaço em branco desnecessário.

Considere a seguinte função JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minificação reduz a função para o seguinte:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Além de remover os comentários e o espaço em branco desnecessário, os seguintes parâmetros e nomes de variáveis foram renomeados da seguinte maneira:

Original | Renomeado
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impacto do agrupamento e minificação

A tabela a seguir descreve as diferenças entre os ativos de carregamento individualmente e o uso de agrupamento e minificação:

Ação | Com B/M | Sem B/M | Alterar
--- | :---: | :---: | :---:
Solicitações de arquivo  | 7   | 18     | 157%
KB transferidos | 156 | 264,68 | 70%
Tempo de carregamento (MS) | 885 | 2360   | 167%

Os navegadores são bastante detalhados em relação aos cabeçalhos de solicitação HTTP. A métrica total de bytes enviados observou uma redução significativa durante o agrupamento. O tempo de carregamento mostra uma melhoria significativa, no entanto, este exemplo foi executado localmente. São obtidos maiores ganhos de desempenho ao usar agrupamento e minificação com ativos transferidos por uma rede.

## <a name="choose-a-bundling-and-minification-strategy"></a>Escolher uma estratégia de agrupamento e minificação

Os modelos de projeto do MVC e do Razor Pages fornecem uma solução para Agrupamento e minificação que consistem em um arquivo de configuração JSON. Ferramentas de terceiros, como o executor de tarefas do [Grunt](xref:client-side/using-grunt) , realizam as mesmas tarefas com um pouco mais de complexidade. Uma ferramenta de terceiros é uma ótima opção quando seu fluxo de trabalho de desenvolvimento requer processamento além do&mdash;agrupamento e minificação, como a otimização de imagem e de retratação. Usando agrupamento e minificação de tempo de design, os arquivos reduzidos são criados antes da implantação do aplicativo. O agrupamento e o minificar antes da implantação proporcionam a vantagem da carga reduzida do servidor. No entanto, é importante reconhecer que o agrupamento de tempo de design e minificação aumenta a complexidade da compilação e só funciona com arquivos estáticos.

## <a name="configure-bundling-and-minification"></a>Configurar agrupamento e minificação

::: moniker range="<= aspnetcore-2.0"

No ASP.NET Core 2,0 ou anterior, os modelos de projeto MVC e Razor Pages fornecem um arquivo de configuração *bundleconfig. JSON* que define as opções para cada pacote:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

No ASP.NET Core 2,1 ou posterior, adicione um novo arquivo JSON, chamado *bundleconfig. JSON*, à raiz do projeto MVC ou Razor Pages. Inclua o seguinte JSON nesse arquivo como um ponto de partida:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

O arquivo *bundleconfig. JSON* define as opções para cada pacote. No exemplo anterior, uma única configuração de pacote é definida para os arquivos JavaScript personalizado (*wwwroot/js/site. js*) e folha de estilos (*wwwroot/CSS/site. css*).

As opções de configuração incluem:

* `outputFileName`: O nome do arquivo de pacote para saída. Pode conter um caminho relativo do arquivo *bundleconfig. JSON* . **obrigatório**
* `inputFiles`: Uma matriz de arquivos para agrupar. Esses são caminhos relativos ao arquivo de configuração. **opcional**, * um valor vazio resulta em um arquivo de saída vazio. Há suporte para padrões de [mascaramento](https://www.tldp.org/LDP/abs/html/globbingref.html) .
* `minify`: As opções de minificação para o tipo de saída. **opcional**, *padrão- `minify: { enabled: true }` *
  * As opções de configuração estão disponíveis por tipo de arquivo de saída.
    * [Minificador CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minificador JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minificador HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Sinalizador que indica se os arquivos gerados devem ser adicionados ao arquivo de projeto. **opcional**, *padrão-falso*
* `sourceMap`: Sinalizador que indica se um mapa de origem deve ser gerado para o arquivo agrupado. **opcional**, *padrão-falso*
* `sourceMapRootPath`: O caminho raiz para armazenar o arquivo de mapa de origem gerado.

## <a name="add-files-to-workflow"></a>Adicionar arquivos ao fluxo de trabalho

Considere um exemplo no qual um arquivo *. CSS personalizado* adicional é adicionado semelhante ao seguinte:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Para reduzir *Custom. css* e agrupá-lo com *site. css* em um arquivo *site. min. css* , adicione o caminho relativo a *bundleconfig. JSON*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Como alternativa, o seguinte padrão de mascaramento poderia ser usado:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Esse padrão de mascaramento corresponde a todos os arquivos CSS e exclui o padrão de arquivo reduzidos.

Construa o aplicativo. Abra *site. min. css* e observe que o conteúdo de *Custom. css* é acrescentado ao final do arquivo.

## <a name="environment-based-bundling-and-minification"></a>Agrupamento baseado em ambiente e minificação

Como prática recomendada, os arquivos agrupados e reduzidos do seu aplicativo devem ser usados em um ambiente de produção. Durante o desenvolvimento, os arquivos originais tornam a depuração mais fácil do aplicativo.

Especifique quais arquivos incluir em suas páginas usando o auxiliar de [marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) em suas exibições. O auxiliar de marca de ambiente só renderiza seu conteúdo quando executado em [ambientes](xref:fundamentals/environments)específicos.

A marca `environment` a seguir renderiza os arquivos CSS não processados durante a `Development` execução no ambiente:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

A marca `environment` a seguir renderiza os arquivos CSS agrupados e reduzidos quando executados em um ambiente diferente de `Development`. Por exemplo, em execução `Production` ou `Staging` dispara a renderização dessas folhas de estilo:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Consumir bundleconfig. JSON de Gulp

Há casos em que o fluxo de trabalho de agrupamento e minificação de um aplicativo requer processamento adicional. Os exemplos incluem otimização de imagem, de cache e processamento de ativos CDN. Para atender a esses requisitos, você pode converter o agrupamento e o fluxo de trabalho minificação para usar o Gulp.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Converter manualmente o agrupamento e o fluxo de trabalho minificação para usar Gulp

Adicione um arquivo *Package. JSON* , com o seguinte `devDependencies`, à raiz do projeto:

> [!WARNING]
> O `gulp-uglify` módulo não dá suporte a ECMASCRIPT (ES) 2015/ES6 e posterior. Instale o [Gulp-terser](https://www.npmjs.com/package/gulp-terser) em `gulp-uglify` vez de usar o ES2015/ES6 ou posterior.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Instale as dependências executando o seguinte comando no mesmo nível que *Package. JSON*:

```console
npm i
```

Instale a CLI do Gulp como uma dependência global:

```console
npm i -g gulp-cli
```

Copie o arquivo *gulpfile. js* abaixo para a raiz do projeto:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Executar tarefas do Gulp

Para disparar a tarefa Gulp minificação antes que o projeto seja compilado no Visual Studio, adicione o seguinte [destino do MSBuild](/visualstudio/msbuild/msbuild-targets) ao arquivo *. csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Neste exemplo, todas as tarefas definidas no `MyPreCompileTarget` destino são executadas antes do destino `Build` predefinido. Uma saída semelhante à seguinte aparece na janela de saída do Visual Studio:

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

## <a name="additional-resources"></a>Recursos adicionais

* [Usar o Grunt](xref:client-side/using-grunt)
* [Usar vários ambientes](xref:fundamentals/environments)
* [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro)
