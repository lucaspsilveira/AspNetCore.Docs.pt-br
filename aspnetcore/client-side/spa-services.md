---
title: Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core
author: scottaddie
description: Conheça os benefícios de usar o JavaScript Services para criar um APLICATIVO de Página Única (SPA) apoiado pelo ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663775"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)

Um Aplicativo de Página Única (SPA) é um tipo popular de aplicativo web devido à sua experiência inerente ao usuário rico. Integrar estruturas de SPA do lado do cliente ou bibliotecas, como [Angular](https://angular.io/) ou [React,](https://facebook.github.io/react/)com frameworks do lado do servidor, como ASP.NET Core, pode ser difícil. JavaScript Services foi desenvolvido para reduzir o atrito no processo de integração. Ele permite uma operação perfeita entre as diferentes pilhas de tecnologia de cliente e servidor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> As características descritas neste artigo são obsoletas a partir de ASP.NET Núcleo 3.0. Um mecanismo de integração de estruturas spa mais simples está disponível no pacote [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet. Para obter mais informações, consulte [[Anúncio] Obsoleting Microsoft.AspNetCore.SpaServices e Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>O que é javaScript Services

JavaScript Services é uma coleção de tecnologias do lado do cliente para ASP.NET Core. Seu objetivo é posicionar ASP.NET Core como a plataforma preferida do lado do servidor dos desenvolvedores para a construção de SPAs.

JavaScript Services consiste em dois pacotes NuGet distintos:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Esses pacotes são úteis nos seguintes cenários:

* Executar JavaScript no servidor
* Use uma estrutura ou biblioteca de SPA
* Construa ativos do lado do cliente com o Webpack

Grande parte do foco neste artigo é colocada no uso do pacote SpaServices.

## <a name="what-is-spaservices"></a>O que é SpaServices

O SpaServices foi criado para posicionar o ASP.NET Core como a plataforma preferida do lado do servidor dos desenvolvedores para a construção de SPAs. O SpaServices não é necessário para desenvolver SPAs com ASP.NET Core e não bloqueia desenvolvedores em uma estrutura de cliente específica.

SpaServices fornece infra-estrutura útil, tais como:

* [Prerenderização do lado do servidor](#server-side-prerendering)
* [Webpack Dev Middleware](#webpack-dev-middleware)
* [Substituição do módulo quente](#hot-module-replacement)
* [Ajudantes de roteamento](#routing-helpers)

Coletivamente, esses componentes de infra-estrutura aprimoram tanto o fluxo de trabalho de desenvolvimento quanto a experiência de tempo de execução. Os componentes podem ser adotados individualmente.

## <a name="prerequisites-for-using-spaservices"></a>Pré-requisitos para o uso do SpaServices

Para trabalhar com spaServices, instale o seguinte:

* [Node.js](https://nodejs.org/) (versão 6 ou posterior) com npm

  * Para verificar se esses componentes estão instalados e podem ser encontrados, execute o seguinte a partir da linha de comando:

    ```console
    node -v && npm -v
    ```

  * Se for implantado em um site do Azure, nenhuma ação é necessária,&mdash;o Node.js é instalado e disponível nos ambientes do servidor.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * No Windows usando o Visual Studio 2017, o SDK é instalado selecionando a carga de trabalho **de desenvolvimento multiplataforma .NET Core.**

* [Pacote Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet

## <a name="server-side-prerendering"></a>Prerenderização do lado do servidor

Um aplicativo universal (também conhecido como isomórfico) é um aplicativo JavaScript capaz de executar tanto no servidor quanto no cliente. Angular, React e outras estruturas populares fornecem uma plataforma universal para este estilo de desenvolvimento de aplicativos. A ideia é primeiro renderizar os componentes da estrutura no servidor via Node.js e, em seguida, delegar uma execução adicional ao cliente.

ASP.NET [Principais Ajudantes de Marca fornecidos](xref:mvc/views/tag-helpers/intro) pelo SpaServices simplificam a implementação da pré-renderização do lado do servidor invocando as funções JavaScript no servidor.

### <a name="server-side-prerendering-prerequisites"></a>Pré-requisitos de pré-renderização do lado do servidor

Instale o pacote npm [de pré-renderização aspnet:](https://www.npmjs.com/package/aspnet-prerendering)

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Configuração de pré-renderização do lado do servidor

Os Tag Helpers são descobertos através do registro de namespace no arquivo *_ViewImports.cshtml* do projeto:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Esses Tag Helpers abstraem os meandros de se comunicar diretamente com APIs de baixo nível, aproveitando uma sintaxe semelhante a HTML dentro da exibição razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-prerender-module Tag Helper

O `asp-prerender-module` Tag Helper, usado no exemplo de código anterior, executa *clientApp/dist/main-server.js* no servidor via Node.js. Para esclarecer, o arquivo *main-server.js* é um artefato da tarefa de transpilação TypeScript-to-JavaScript no processo de compilação do [Webpack.](https://webpack.github.io/) O Webpack define um alias de ponto de entrada de; `main-server` e, a travessia do gráfico de dependência para este alias começa no arquivo *ClientApp/boot-server.ts:*

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

No exemplo Angular a seguir, o arquivo *ClientApp/boot-server.ts* utiliza a `createServerRenderer` função e `RenderResult` o `aspnet-prerendering` tipo do pacote npm para configurar a renderização do servidor via Node.js. A marcação HTML destinada à renderização do lado do servidor é passada para uma chamada `Promise` de função de resolução, que é envolta em um objeto JavaScript fortemente digitado. O `Promise` significado do objeto é que ele fornece assincronizasamente a marcação HTML para a página para injeção no elemento espaço reservado do DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-prerender-data Tag Helper

Quando juntamente `asp-prerender-module` com o `asp-prerender-data` Tag Helper, o Tag Helper pode ser usado para passar informações contextuais da exibição Razor para o JavaScript do lado do servidor. Por exemplo, a marcação a `main-server` seguir passa os dados do usuário para o módulo:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

O `UserName` argumento recebido é serializado usando o serializador JSON incorporado e é armazenado no `params.data` objeto. No exemplo Angular a seguir, os dados são usados para construir uma saudação personalizada dentro de um `h1` elemento:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Os nomes de propriedade passados em Tag Helpers são representados com notação **PascalCase.** Contraste isso com JavaScript, onde os mesmos nomes de propriedade são representados com **camelCase**. A configuração de serialização JSON padrão é responsável por essa diferença.

Para expandir o exemplo de código anterior, os dados podem ser `globals` passados do `resolve` servidor para a exibição, hidratando a propriedade fornecida à função:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

A `postList` matriz definida `globals` dentro do objeto é anexada ao objeto global `window` do navegador. Essa variável içamento para o escopo global elimina a duplicação do esforço, particularmente no que diz respeito ao carregamento dos mesmos dados uma vez no servidor e novamente no cliente.

![variável pós-lista global anexada ao objeto da janela](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack Dev Middleware

[O Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduz um fluxo de trabalho de desenvolvimento simplificado pelo qual o Webpack constrói recursos sob demanda. O middleware compila automaticamente e serve recursos do lado do cliente quando uma página é recarregada no navegador. A abordagem alternativa é invocar manualmente o Webpack através do script de compilação npm do projeto quando uma dependência de terceiros ou o código personalizado for alterado. Um script de compilação npm no arquivo *package.json* é mostrado no seguinte exemplo:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Pré-requisitos do Webpack Dev Middleware

Instale o pacote [npm aspnet-webpack:](https://www.npmjs.com/package/aspnet-webpack)

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Configuração do Webpack Dev Middleware

O Webpack Dev Middleware está registrado no pipeline de solicitação HTTP `Configure` através do seguinte código no método do arquivo *Startup.cs:*

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

O `UseWebpackDevMiddleware` método de extensão deve ser chamado `UseStaticFiles` antes de registrar a [hospedagem de arquivos estáticos](xref:fundamentals/static-files) através do método de extensão. Por razões de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.

A propriedade do `output.publicPath` arquivo *webpack.config.js* diz ao `dist` middleware para observar a pasta para obter alterações:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Substituição do módulo quente

Pense no recurso Dev Middleware [(Hot Module Replacement, substituição](https://webpack.js.org/concepts/hot-module-replacement/) de módulo quente) do Webpack como uma evolução do [Webpack Dev Middleware](#webpack-dev-middleware). O HMR introduz todos os mesmos benefícios, mas agiliza ainda mais o fluxo de trabalho de desenvolvimento atualizando automaticamente o conteúdo da página após a compilação das alterações. Não confunda isso com uma atualização do navegador, o que interferiria no estado atual de memória e na sessão de depuração do SPA. Há um link ao vivo entre o serviço Webpack Dev Middleware e o navegador, o que significa que as alterações são empurradas para o navegador.

### <a name="hot-module-replacement-prerequisites"></a>Pré-requisitos de substituição do módulo quente

Instale o pacote [npm hot-middleware do webpack:](https://www.npmjs.com/package/webpack-hot-middleware)

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Configuração de substituição do módulo quente

O componente HMR deve ser registrado no pipeline de `Configure` solicitação HTTP da MVC no método:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Como era verdade no [Webpack Dev Middleware](#webpack-dev-middleware), o método de `UseWebpackDevMiddleware` extensão deve ser chamado antes do método de `UseStaticFiles` extensão. Por razões de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.

O arquivo *webpack.config.js* `plugins` deve definir uma matriz, mesmo que fique vazia:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Depois de carregar o aplicativo no navegador, a guia Console das ferramentas do desenvolvedor fornece a confirmação da ativação do HMR:

![Mensagem conectada de substituição de módulo quente](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Ajudantes de roteamento

Na maioria dos SPAs baseados no Core, ASP.NET roteamento do lado do cliente é muitas vezes desejado, além do roteamento do lado do servidor. Os sistemas de roteamento SPA e MVC podem funcionar de forma independente sem interferência. Há, no entanto, um caso de borda que coloca desafios: identificar 404 respostas HTTP.

Considere o cenário em que `/some/page` uma rota sem extensão é usada. Suponha que a solicitação não corresponda a uma rota do lado do servidor, mas seu padrão corresponde a uma rota lado do cliente. Agora considere uma solicitação `/images/user-512.png`de entrada, que geralmente espera encontrar um arquivo de imagem no servidor. Se esse caminho de recurso solicitado não corresponder a nenhuma rota do lado do servidor ou&mdash;arquivo estático, é improvável que o aplicativo do lado do cliente lidaria com ele geralmente retornando um código de status HTTP 404 é desejado.

### <a name="routing-helpers-prerequisites"></a>Pré-requisitos para ajudantes de roteamento

Instale o pacote npm de roteamento do lado do cliente. Usando angular como exemplo:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Configuração dos ajudantes de roteamento

Um método `MapSpaFallbackRoute` de extensão `Configure` nomeado é usado no método:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

As rotas são avaliadas na ordem em que estão configuradas. Consequentemente, `default` a rota no exemplo de código anterior é usada primeiro para correspondência de padrões.

## <a name="create-a-new-project"></a>Criar um novo projeto

Os JavaScript Services fornecem modelos de aplicativos pré-configurados. O SpaServices é usado nesses modelos em conjunto com diferentes frameworks e bibliotecas, como Angular, React e Redux.

Esses modelos podem ser instalados através do .NET Core CLI executando o seguinte comando:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Uma lista de modelos spa disponíveis é exibida:

| Modelos                                 | Nome curto | Linguagem | Marcas        |
| ------------------------------------------| :--------: | :------: | :---------: |
| Núcleo de ASP.NET MVC com Angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core com React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core com React.js e Redux  | reactredux | [C#]     | Web/MVC/SPA |

Para criar um novo projeto usando um dos modelos de SPA, inclua o **Nome Curto** do modelo no novo comando [dotnet.](/dotnet/core/tools/dotnet-new) O comando a seguir cria um aplicativo Angular com ASP.NET MVC Core configurado para o lado do servidor:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Defina o modo de configuração em tempo de execução

Existem dois modos de configuração de tempo de execução principais:

* **Desenvolvimento**:
  * Inclui mapas de origem para facilitar a depuração.
  * Não otimiza o código do lado do cliente para o desempenho.
* **Produção**:
  * Exclui mapas de origem.
  * Otimiza o código do lado do cliente através de agrupamento e minificação.

ASP.NET Core usa uma `ASPNETCORE_ENVIRONMENT` variável de ambiente nomeada para armazenar o modo de configuração. Para obter mais informações, consulte [Definir o ambiente](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Executar com .NET Core CLI

Restaurar os pacotes NuGet e npm necessários executando o seguinte comando na raiz do projeto:

```dotnetcli
dotnet restore && npm i
```

Construir e executar o aplicativo:

```dotnetcli
dotnet run
```

O aplicativo começa no localhost de acordo com o [modo de configuração em tempo de execução](#set-the-runtime-configuration-mode). Navegar para `http://localhost:5000` o navegador exibe a página de aterrissagem.

### <a name="run-with-visual-studio-2017"></a>Execute com o Visual Studio 2017

Abra o arquivo *.csproj* gerado pelo novo comando [dotnet.](/dotnet/core/tools/dotnet-new) Os pacotes NuGet e npm necessários são restaurados automaticamente após a abertura do projeto. Este processo de restauração pode levar até alguns minutos, e o aplicativo está pronto para ser executado quando for concluído. Clique no botão verde `Ctrl + F5`ou pressione , e o navegador abre para a página de aterrissagem do aplicativo. O aplicativo é executado no localhost de acordo com o [modo de configuração em tempo de execução](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Testar o aplicativo

Os modelos do SpaServices são pré-configurados para executar testes do lado do cliente usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/). Jasmine é uma estrutura de teste de unidade popular para JavaScript, enquanto Karma é um corredor de teste para esses testes. O Karma está configurado para trabalhar com o [Webpack Dev Middleware de](#webpack-dev-middleware) tal forma que o desenvolvedor não é obrigado a parar e executar o teste toda vez que as alterações são feitas. Seja o código que corre contra o caso de teste ou o caso de teste em si, o teste é executado automaticamente.

Usando o aplicativo Angular como exemplo, dois casos de `CounterComponent` teste jasmine já estão previstos para o arquivo *counter.component.spec.ts:*

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Abra o prompt de comando no diretório *ClientApp.* Execute o comando a seguir:

```console
npm test
```

O script lança o corredor de teste Karma, que lê as configurações definidas no arquivo *karma.conf.js.* Entre outras configurações, o *karma.conf.js* identifica os arquivos `files` de teste a serem executados através de sua matriz:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publicar o aplicativo

Consulte [esta edição do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) para obter mais informações sobre a publicação no Azure.

A combinação dos ativos gerados do lado do cliente e dos artefatos ASP.NET Core publicados em um pacote pronto para implantação pode ser complicado. Felizmente, o SpaServices orquestra todo esse processo de publicação `RunWebpack`com um alvo msbuild personalizado chamado :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

O alvo do MSBuild tem as seguintes responsabilidades:

1. Restaure os pacotes npm.
1. Crie uma compilação de grau de produção dos ativos de terceiros e do lado do cliente.
1. Crie uma compilação de grau de produção dos ativos personalizados do lado do cliente.
1. Copie os ativos gerados pelo Webpack para a pasta de publicação.

O destino MSBuild é invocado ao ser executado:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Recursos adicionais

* [Docs angulares](https://angular.io/docs)
