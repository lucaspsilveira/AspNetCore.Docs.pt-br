---
title: Pacote e minify ativos estáticos em ASP.NET Core
author: scottaddie
description: Aprenda a otimizar recursos estáticos em uma ASP.NET aplicativo web Core aplicando técnicas de agrupamento e minificação.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658266"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Pacote e minify ativos estáticos em ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) e [David Pine](https://twitter.com/davidpine7)

Este artigo explica os benefícios da aplicação de agrupamento e minificação, incluindo como esses recursos podem ser usados com ASP.NET aplicativos web Core.

## <a name="what-is-bundling-and-minification"></a>O que é agrupamento e minificação

Agrupamento e minificação são duas otimizações de desempenho distintas que você pode aplicar em um aplicativo web. Usados em conjunto, o agrupamento e a minificação melhoram o desempenho, reduzindo o número de solicitações de servidores e reduzindo o tamanho dos ativos estáticos solicitados.

Agrupamento e minificação melhoram principalmente o tempo de carga de solicitação da primeira página. Uma vez solicitada uma página da Web, o navegador armazena os ativos estáticos (JavaScript, CSS e imagens). Consequentemente, o agrupamento e a minificação não melhoram o desempenho ao solicitar a mesma página, ou páginas, no mesmo site solicitando os mesmos ativos. Se o cabeçalho expirar não for definido corretamente nos ativos e se o agrupamento e a minificação não forem usados, a heurística de frescor do navegador marca os ativos obsoletos após alguns dias. Além disso, o navegador requer uma solicitação de validação para cada ativo. Neste caso, o agrupamento e a minificação proporcionam uma melhoria de desempenho mesmo após a solicitação da primeira página.

### <a name="bundling"></a>Agrupamento

O agrupamento combina vários arquivos em um único arquivo. O agrupamento reduz o número de solicitações de servidor necessárias para renderizar um ativo da Web, como uma página da Web. Você pode criar qualquer número de pacotes individuais especificamente para CSS, JavaScript, etc. Menos arquivos significa menos solicitações HTTP do navegador para o servidor ou do serviço que fornece seu aplicativo. Isso resulta em um melhor desempenho de carga da primeira página.

### <a name="minification"></a>Minificação

A minificação remove caracteres desnecessários do código sem alterar a funcionalidade. O resultado é uma redução significativa de tamanho nos ativos solicitados (como arquivos CSS, imagens e JavaScript). Os efeitos colaterais comuns da minificação incluem encurtar nomes variáveis para um caractere e remover comentários e espaço em branco desnecessário.

Considere a seguinte função JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

A minificação reduz a função ao seguinte:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Além de remover os comentários e o espaço em branco desnecessário, os seguintes parâmetros e nomes variáveis foram renomeados da seguinte forma:

Original | Renomeado
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impacto do agrupamento e minificação

A tabela a seguir descreve diferenças entre carregar ativos individualmente e usar agrupamento e minificação:

Ação | Com B/M | Sem B/M | Alterar
--- | :---: | :---: | :---:
Solicitações de arquivos  | 7   | 18     | 157%
KB transferido | 156 | 264.68 | 70%
Tempo de carga (ms) | 885 | 2360   | 167%

Os navegadores são bastante verbosos em relação aos cabeçalhos de solicitação HTTP. A métrica total de bytes enviados viu uma redução significativa ao agrupamento. O tempo de carga mostra uma melhora significativa, no entanto este exemplo correu localmente. Maiores ganhos de desempenho são realizados ao usar agrupamento e minificação com ativos transferidos por uma rede.

## <a name="choose-a-bundling-and-minification-strategy"></a>Escolha uma estratégia de agrupamento e minificação

Os modelos de projeto MVC e Razor Pages fornecem uma solução fora da caixa para agrupamento e minificação que consiste em um arquivo de configuração JSON. Ferramentas de terceiros, como o corredor de tarefas [Grunt,](xref:client-side/using-grunt) realizam as mesmas tarefas com um pouco mais de complexidade. Uma ferramenta de terceiros é um ótimo ajuste quando seu fluxo de&mdash;trabalho de desenvolvimento requer processamento além de agrupamento e minificação, como fiação e otimização de imagem. Usando o agrupamento e a minificação do tempo de projeto, os arquivos minified são criados antes da implantação do aplicativo. Agrupar e minificar antes da implantação fornece a vantagem da carga reduzida do servidor. No entanto, é importante reconhecer que o agrupamento e a minificação do tempo de projeto aumentam a complexidade da compilação e só funciona com arquivos estáticos.

## <a name="configure-bundling-and-minification"></a>Configurar agrupamento e minificação

::: moniker range="<= aspnetcore-2.0"

Em ASP.NET Core 2.0 ou anterior, os modelos de projeto MVC e Razor Pages fornecem um arquivo de configuração *bundleconfig.json* que define as opções para cada pacote:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Em ASP.NET Core 2.1 ou posterior, adicione um novo arquivo JSON, chamado *bundleconfig.json,* à raiz do projeto MVC ou Razor Pages. Inclua o seguinte JSON nesse arquivo como ponto de partida:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

O arquivo *bundleconfig.json* define as opções para cada pacote. No exemplo anterior, uma configuração de pacote único é definida para os arquivos JavaScript personalizado *(wwwroot/js/site.js)* e stylesheet *(wwwroot/css/site.css).*

As opções de configuração incluem:

* `outputFileName`: O nome do arquivo de pacote para saída. Pode conter um caminho relativo a partir do arquivo *bundleconfig.json.* **Necessário**
* `inputFiles`: Uma matriz de arquivos para agrupar. Estes são caminhos relativos para o arquivo de configuração. **opcional**, *um valor vazio resulta em um arquivo de saída vazio. [padrões de globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) são suportados.
* `minify`: As opções de minificação para o tipo de saída. **opcional,** *padrão `minify: { enabled: true }` -*
  * As opções de configuração estão disponíveis por tipo de arquivo de saída.
    * [MinisterisCS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Ministerífica JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Sinalizador indicando se deve adicionar arquivos gerados ao arquivo do projeto. **opcional**, *padrão - falso*
* `sourceMap`: Bandeira indicando se deve gerar um mapa de origem para o arquivo empacotado. **opcional**, *padrão - falso*
* `sourceMapRootPath`: O caminho raiz para armazenar o arquivo do mapa de origem gerado.

## <a name="build-time-execution-of-bundling-and-minification"></a>Execução em tempo de construção de agrupamento e minificação

O pacote [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet permite a execução de agrupamento e minificação no tempo de construção. O pacote injeta [alvos MSBuild](/visualstudio/msbuild/msbuild-targets) que são executados em tempo de construção e limpeza. O arquivo *bundleconfig.json* é analisado pelo processo de compilação para produzir os arquivos de saída com base na configuração definida.

> [!NOTE]
> O BuildBundlerMinifier pertence a um projeto orientado pela comunidade no GitHub para o qual a Microsoft não oferece suporte. Questões devem ser arquivadas [aqui.](https://github.com/madskristensen/BundlerMinifier/issues)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Adicione o pacote *BuildBundlerMinifier* ao seu projeto.

Compile o projeto. O seguinte aparece na janela Saída:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Limpe o projeto. O seguinte aparece na janela Saída:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Adicione o pacote *BuildBundlerMinifier* ao seu projeto:

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Se usar ASP.NET Core 1.x, restaure o pacote recém-adicionado:

```dotnetcli
dotnet restore
```

Compile o projeto:

```dotnetcli
dotnet build
```

A seguir aparece:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Limpe o projeto:

```dotnetcli
dotnet clean
```

A saída a seguir é exibida:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Execução ad hoc de agrupamento e minificação

É possível executar as tarefas de agrupamento e minificação em uma base ad hoc, sem construir o projeto. Adicione o pacote [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet ao seu projeto:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core pertence a um projeto orientado pela comunidade no GitHub para o qual a Microsoft não oferece suporte. Questões devem ser arquivadas [aqui.](https://github.com/madskristensen/BundlerMinifier/issues)

Este pacote estende o .NET Core CLI para incluir a ferramenta *dotnet-bundle.* O seguinte comando pode ser executado na janela PMC (Package Manager Console, console de gerenciamento de pacotes) ou em um shell de comando:

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> NuGet Package Manager adiciona dependências ao arquivo *.csproj como `<PackageReference />` nós. O `dotnet bundle` comando é registrado no .NET Core `<DotNetCliToolReference />` CLI somente quando um nó é usado. Modifique o arquivo *.csproj de acordo.

## <a name="add-files-to-workflow"></a>Adicionar arquivos ao fluxo de trabalho

Considere um exemplo no qual um arquivo *custom.css* adicional é adicionado semelhante ao seguinte:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Para minify *custom.css* e empacotá-lo com *site.css* em um arquivo *site.min.css,* adicione o caminho relativo ao *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternativamente, o seguinte padrão de globbing poderia ser usado:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Este padrão globbing corresponde a todos os arquivos CSS e exclui o padrão de arquivo minizado.

Construa o aplicativo. Abra *o site.min.css* e observe que o conteúdo do *custom.css* é anexado ao final do arquivo.

## <a name="environment-based-bundling-and-minification"></a>Agrupamento e minificação baseados em ambiente

Como uma prática recomendada, os arquivos empacotados e minizados do seu aplicativo devem ser usados em um ambiente de produção. Durante o desenvolvimento, os arquivos originais facilitam a depuração do aplicativo.

Especifique quais arquivos incluir em suas páginas usando o [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) em suas exibições. O Environment Tag Helper só renderiza seu conteúdo ao ser executado em [ambientes](xref:fundamentals/environments)específicos.

A `environment` tag a seguir renderiza os arquivos CSS `Development` não processados ao ser executado no ambiente:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

A `environment` tag a seguir renderiza os arquivos CSS empacotados `Development`e minizados ao ser executado em um ambiente diferente de . Por exemplo, `Production` a `Staging` execução ou aciona a renderização dessas folhas de estilos:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Consumir bundleconfig.json da Gulp

Há casos em que o fluxo de trabalho de agrupamento e minificação de um aplicativo requer processamento adicional. Exemplos incluem otimização de imagem, rebentação de cache e processamento de ativos de CDN. Para satisfazer esses requisitos, você pode converter o fluxo de trabalho de agrupamento e minificação para usar o Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Use a extensão Minifier bundler &

A extensão do [Minifier &](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) Visual Studio Bundler lida com a conversão para Gulp.

> [!NOTE]
> A extensão do Minifier do Bundler & pertence a um projeto orientado pela comunidade no GitHub para o qual a Microsoft não oferece suporte. Questões devem ser arquivadas [aqui.](https://github.com/madskristensen/BundlerMinifier/issues)

Clique com o botão direito do mouse no arquivo *bundleconfig.json* no Solution Explorer e selecione **Bundler & Minifier** > **Converter to Gulp...**:

![Converter para o item do menu de contexto gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Os arquivos *gulpfile.js* e *package.json* são adicionados ao projeto. Os pacotes [npm](https://www.npmjs.com/) de suporte listados na seção do `devDependencies` arquivo *package.json* estão instalados.

Execute o seguinte comando na janela PMC para instalar o Gulp CLI como uma dependência global:

```console
npm i -g gulp-cli
```

O arquivo *gulpfile.js* lê o arquivo *bundleconfig.json* para as entradas, saídas e configurações.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Converter manualmente

Se o Visual Studio e/ou a extensão Bundler & Minifier não estiver disponível, converta manualmente.

Adicione um *arquivo package.json,* com o seguinte `devDependencies`, à raiz do projeto:

> [!WARNING]
> O `gulp-uglify` módulo não suporta ECMAScript (ES) 2015 / ES6 e posterior. Instale [gulp-terser](https://www.npmjs.com/package/gulp-terser) em vez de `gulp-uglify` usar ES2015 / ES6 ou posterior.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Instale as dependências executando o seguinte comando no mesmo nível *que package.json*:

```console
npm i
```

Instale o Gulp CLI como uma dependência global:

```console
npm i -g gulp-cli
```

Copie o arquivo *gulpfile.js* abaixo para a raiz do projeto:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Executar tarefas de Gulp

Para ativar a tarefa de minificação gulp antes que o projeto seja construído no Visual Studio, adicione o seguinte [Alvo mSBuild](/visualstudio/msbuild/msbuild-targets) ao arquivo *.csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Neste exemplo, todas as `MyPreCompileTarget` tarefas definidas `Build` dentro do destino são executadas antes do destino predefinido. Saída semelhante à seguinte aparece na janela Saída do Visual Studio:

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
* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
