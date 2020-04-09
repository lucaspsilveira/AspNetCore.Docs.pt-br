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
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="13bd6-103">Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13bd6-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="13bd6-104">Por [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="13bd6-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="13bd6-105">Um Aplicativo de Página Única (SPA) é um tipo popular de aplicativo web devido à sua experiência inerente ao usuário rico.</span><span class="sxs-lookup"><span data-stu-id="13bd6-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="13bd6-106">Integrar estruturas de SPA do lado do cliente ou bibliotecas, como [Angular](https://angular.io/) ou [React,](https://facebook.github.io/react/)com frameworks do lado do servidor, como ASP.NET Core, pode ser difícil.</span><span class="sxs-lookup"><span data-stu-id="13bd6-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="13bd6-107">JavaScript Services foi desenvolvido para reduzir o atrito no processo de integração.</span><span class="sxs-lookup"><span data-stu-id="13bd6-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="13bd6-108">Ele permite uma operação perfeita entre as diferentes pilhas de tecnologia de cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="13bd6-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="13bd6-109">As características descritas neste artigo são obsoletas a partir de ASP.NET Núcleo 3.0.</span><span class="sxs-lookup"><span data-stu-id="13bd6-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="13bd6-110">Um mecanismo de integração de estruturas spa mais simples está disponível no pacote [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet.</span><span class="sxs-lookup"><span data-stu-id="13bd6-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="13bd6-111">Para obter mais informações, consulte [[Anúncio] Obsoleting Microsoft.AspNetCore.SpaServices e Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="13bd6-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="13bd6-112">O que é javaScript Services</span><span class="sxs-lookup"><span data-stu-id="13bd6-112">What is JavaScript Services</span></span>

<span data-ttu-id="13bd6-113">JavaScript Services é uma coleção de tecnologias do lado do cliente para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="13bd6-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="13bd6-114">Seu objetivo é posicionar ASP.NET Core como a plataforma preferida do lado do servidor dos desenvolvedores para a construção de SPAs.</span><span class="sxs-lookup"><span data-stu-id="13bd6-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="13bd6-115">JavaScript Services consiste em dois pacotes NuGet distintos:</span><span class="sxs-lookup"><span data-stu-id="13bd6-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="13bd6-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="13bd6-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="13bd6-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="13bd6-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="13bd6-118">Esses pacotes são úteis nos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="13bd6-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="13bd6-119">Executar JavaScript no servidor</span><span class="sxs-lookup"><span data-stu-id="13bd6-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="13bd6-120">Use uma estrutura ou biblioteca de SPA</span><span class="sxs-lookup"><span data-stu-id="13bd6-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="13bd6-121">Construa ativos do lado do cliente com o Webpack</span><span class="sxs-lookup"><span data-stu-id="13bd6-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="13bd6-122">Grande parte do foco neste artigo é colocada no uso do pacote SpaServices.</span><span class="sxs-lookup"><span data-stu-id="13bd6-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="13bd6-123">O que é SpaServices</span><span class="sxs-lookup"><span data-stu-id="13bd6-123">What is SpaServices</span></span>

<span data-ttu-id="13bd6-124">O SpaServices foi criado para posicionar o ASP.NET Core como a plataforma preferida do lado do servidor dos desenvolvedores para a construção de SPAs.</span><span class="sxs-lookup"><span data-stu-id="13bd6-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="13bd6-125">O SpaServices não é necessário para desenvolver SPAs com ASP.NET Core e não bloqueia desenvolvedores em uma estrutura de cliente específica.</span><span class="sxs-lookup"><span data-stu-id="13bd6-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="13bd6-126">SpaServices fornece infra-estrutura útil, tais como:</span><span class="sxs-lookup"><span data-stu-id="13bd6-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="13bd6-127">Prerenderização do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="13bd6-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="13bd6-128">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="13bd6-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="13bd6-129">Substituição do módulo quente</span><span class="sxs-lookup"><span data-stu-id="13bd6-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="13bd6-130">Ajudantes de roteamento</span><span class="sxs-lookup"><span data-stu-id="13bd6-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="13bd6-131">Coletivamente, esses componentes de infra-estrutura aprimoram tanto o fluxo de trabalho de desenvolvimento quanto a experiência de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="13bd6-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="13bd6-132">Os componentes podem ser adotados individualmente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="13bd6-133">Pré-requisitos para o uso do SpaServices</span><span class="sxs-lookup"><span data-stu-id="13bd6-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="13bd6-134">Para trabalhar com spaServices, instale o seguinte:</span><span class="sxs-lookup"><span data-stu-id="13bd6-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="13bd6-135">[Node.js](https://nodejs.org/) (versão 6 ou posterior) com npm</span><span class="sxs-lookup"><span data-stu-id="13bd6-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="13bd6-136">Para verificar se esses componentes estão instalados e podem ser encontrados, execute o seguinte a partir da linha de comando:</span><span class="sxs-lookup"><span data-stu-id="13bd6-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="13bd6-137">Se for implantado em um site do Azure, nenhuma ação é necessária,&mdash;o Node.js é instalado e disponível nos ambientes do servidor.</span><span class="sxs-lookup"><span data-stu-id="13bd6-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="13bd6-138">No Windows usando o Visual Studio 2017, o SDK é instalado selecionando a carga de trabalho **de desenvolvimento multiplataforma .NET Core.**</span><span class="sxs-lookup"><span data-stu-id="13bd6-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="13bd6-139">[Pacote Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet</span><span class="sxs-lookup"><span data-stu-id="13bd6-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="13bd6-140">Prerenderização do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="13bd6-140">Server-side prerendering</span></span>

<span data-ttu-id="13bd6-141">Um aplicativo universal (também conhecido como isomórfico) é um aplicativo JavaScript capaz de executar tanto no servidor quanto no cliente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="13bd6-142">Angular, React e outras estruturas populares fornecem uma plataforma universal para este estilo de desenvolvimento de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="13bd6-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="13bd6-143">A ideia é primeiro renderizar os componentes da estrutura no servidor via Node.js e, em seguida, delegar uma execução adicional ao cliente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="13bd6-144">ASP.NET [Principais Ajudantes de Marca fornecidos](xref:mvc/views/tag-helpers/intro) pelo SpaServices simplificam a implementação da pré-renderização do lado do servidor invocando as funções JavaScript no servidor.</span><span class="sxs-lookup"><span data-stu-id="13bd6-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="13bd6-145">Pré-requisitos de pré-renderização do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="13bd6-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="13bd6-146">Instale o pacote npm [de pré-renderização aspnet:](https://www.npmjs.com/package/aspnet-prerendering)</span><span class="sxs-lookup"><span data-stu-id="13bd6-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="13bd6-147">Configuração de pré-renderização do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="13bd6-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="13bd6-148">Os Tag Helpers são descobertos através do registro de namespace no arquivo *_ViewImports.cshtml* do projeto:</span><span class="sxs-lookup"><span data-stu-id="13bd6-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="13bd6-149">Esses Tag Helpers abstraem os meandros de se comunicar diretamente com APIs de baixo nível, aproveitando uma sintaxe semelhante a HTML dentro da exibição razor:</span><span class="sxs-lookup"><span data-stu-id="13bd6-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="13bd6-150">asp-prerender-module Tag Helper</span><span class="sxs-lookup"><span data-stu-id="13bd6-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="13bd6-151">O `asp-prerender-module` Tag Helper, usado no exemplo de código anterior, executa *clientApp/dist/main-server.js* no servidor via Node.js.</span><span class="sxs-lookup"><span data-stu-id="13bd6-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="13bd6-152">Para esclarecer, o arquivo *main-server.js* é um artefato da tarefa de transpilação TypeScript-to-JavaScript no processo de compilação do [Webpack.](https://webpack.github.io/)</span><span class="sxs-lookup"><span data-stu-id="13bd6-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="13bd6-153">O Webpack define um alias de ponto de entrada de; `main-server` e, a travessia do gráfico de dependência para este alias começa no arquivo *ClientApp/boot-server.ts:*</span><span class="sxs-lookup"><span data-stu-id="13bd6-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="13bd6-154">No exemplo Angular a seguir, o arquivo *ClientApp/boot-server.ts* utiliza a `createServerRenderer` função e `RenderResult` o `aspnet-prerendering` tipo do pacote npm para configurar a renderização do servidor via Node.js.</span><span class="sxs-lookup"><span data-stu-id="13bd6-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="13bd6-155">A marcação HTML destinada à renderização do lado do servidor é passada para uma chamada `Promise` de função de resolução, que é envolta em um objeto JavaScript fortemente digitado.</span><span class="sxs-lookup"><span data-stu-id="13bd6-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="13bd6-156">O `Promise` significado do objeto é que ele fornece assincronizasamente a marcação HTML para a página para injeção no elemento espaço reservado do DOM.</span><span class="sxs-lookup"><span data-stu-id="13bd6-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="13bd6-157">asp-prerender-data Tag Helper</span><span class="sxs-lookup"><span data-stu-id="13bd6-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="13bd6-158">Quando juntamente `asp-prerender-module` com o `asp-prerender-data` Tag Helper, o Tag Helper pode ser usado para passar informações contextuais da exibição Razor para o JavaScript do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="13bd6-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="13bd6-159">Por exemplo, a marcação a `main-server` seguir passa os dados do usuário para o módulo:</span><span class="sxs-lookup"><span data-stu-id="13bd6-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="13bd6-160">O `UserName` argumento recebido é serializado usando o serializador JSON incorporado e é armazenado no `params.data` objeto.</span><span class="sxs-lookup"><span data-stu-id="13bd6-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="13bd6-161">No exemplo Angular a seguir, os dados são usados para construir uma saudação personalizada dentro de um `h1` elemento:</span><span class="sxs-lookup"><span data-stu-id="13bd6-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="13bd6-162">Os nomes de propriedade passados em Tag Helpers são representados com notação **PascalCase.**</span><span class="sxs-lookup"><span data-stu-id="13bd6-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="13bd6-163">Contraste isso com JavaScript, onde os mesmos nomes de propriedade são representados com **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="13bd6-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="13bd6-164">A configuração de serialização JSON padrão é responsável por essa diferença.</span><span class="sxs-lookup"><span data-stu-id="13bd6-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="13bd6-165">Para expandir o exemplo de código anterior, os dados podem ser `globals` passados do `resolve` servidor para a exibição, hidratando a propriedade fornecida à função:</span><span class="sxs-lookup"><span data-stu-id="13bd6-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="13bd6-166">A `postList` matriz definida `globals` dentro do objeto é anexada ao objeto global `window` do navegador.</span><span class="sxs-lookup"><span data-stu-id="13bd6-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="13bd6-167">Essa variável içamento para o escopo global elimina a duplicação do esforço, particularmente no que diz respeito ao carregamento dos mesmos dados uma vez no servidor e novamente no cliente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![variável pós-lista global anexada ao objeto da janela](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="13bd6-169">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="13bd6-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="13bd6-170">[O Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduz um fluxo de trabalho de desenvolvimento simplificado pelo qual o Webpack constrói recursos sob demanda.</span><span class="sxs-lookup"><span data-stu-id="13bd6-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="13bd6-171">O middleware compila automaticamente e serve recursos do lado do cliente quando uma página é recarregada no navegador.</span><span class="sxs-lookup"><span data-stu-id="13bd6-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="13bd6-172">A abordagem alternativa é invocar manualmente o Webpack através do script de compilação npm do projeto quando uma dependência de terceiros ou o código personalizado for alterado.</span><span class="sxs-lookup"><span data-stu-id="13bd6-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="13bd6-173">Um script de compilação npm no arquivo *package.json* é mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="13bd6-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="13bd6-174">Pré-requisitos do Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="13bd6-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="13bd6-175">Instale o pacote [npm aspnet-webpack:](https://www.npmjs.com/package/aspnet-webpack)</span><span class="sxs-lookup"><span data-stu-id="13bd6-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="13bd6-176">Configuração do Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="13bd6-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="13bd6-177">O Webpack Dev Middleware está registrado no pipeline de solicitação HTTP `Configure` através do seguinte código no método do arquivo *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="13bd6-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="13bd6-178">O `UseWebpackDevMiddleware` método de extensão deve ser chamado `UseStaticFiles` antes de registrar a [hospedagem de arquivos estáticos](xref:fundamentals/static-files) através do método de extensão.</span><span class="sxs-lookup"><span data-stu-id="13bd6-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="13bd6-179">Por razões de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="13bd6-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="13bd6-180">A propriedade do `output.publicPath` arquivo *webpack.config.js* diz ao `dist` middleware para observar a pasta para obter alterações:</span><span class="sxs-lookup"><span data-stu-id="13bd6-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="13bd6-181">Substituição do módulo quente</span><span class="sxs-lookup"><span data-stu-id="13bd6-181">Hot Module Replacement</span></span>

<span data-ttu-id="13bd6-182">Pense no recurso Dev Middleware [(Hot Module Replacement, substituição](https://webpack.js.org/concepts/hot-module-replacement/) de módulo quente) do Webpack como uma evolução do [Webpack Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="13bd6-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="13bd6-183">O HMR introduz todos os mesmos benefícios, mas agiliza ainda mais o fluxo de trabalho de desenvolvimento atualizando automaticamente o conteúdo da página após a compilação das alterações.</span><span class="sxs-lookup"><span data-stu-id="13bd6-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="13bd6-184">Não confunda isso com uma atualização do navegador, o que interferiria no estado atual de memória e na sessão de depuração do SPA.</span><span class="sxs-lookup"><span data-stu-id="13bd6-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="13bd6-185">Há um link ao vivo entre o serviço Webpack Dev Middleware e o navegador, o que significa que as alterações são empurradas para o navegador.</span><span class="sxs-lookup"><span data-stu-id="13bd6-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="13bd6-186">Pré-requisitos de substituição do módulo quente</span><span class="sxs-lookup"><span data-stu-id="13bd6-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="13bd6-187">Instale o pacote [npm hot-middleware do webpack:](https://www.npmjs.com/package/webpack-hot-middleware)</span><span class="sxs-lookup"><span data-stu-id="13bd6-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="13bd6-188">Configuração de substituição do módulo quente</span><span class="sxs-lookup"><span data-stu-id="13bd6-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="13bd6-189">O componente HMR deve ser registrado no pipeline de `Configure` solicitação HTTP da MVC no método:</span><span class="sxs-lookup"><span data-stu-id="13bd6-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="13bd6-190">Como era verdade no [Webpack Dev Middleware](#webpack-dev-middleware), o método de `UseWebpackDevMiddleware` extensão deve ser chamado antes do método de `UseStaticFiles` extensão.</span><span class="sxs-lookup"><span data-stu-id="13bd6-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="13bd6-191">Por razões de segurança, registre o middleware somente quando o aplicativo for executado no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="13bd6-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="13bd6-192">O arquivo *webpack.config.js* `plugins` deve definir uma matriz, mesmo que fique vazia:</span><span class="sxs-lookup"><span data-stu-id="13bd6-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="13bd6-193">Depois de carregar o aplicativo no navegador, a guia Console das ferramentas do desenvolvedor fornece a confirmação da ativação do HMR:</span><span class="sxs-lookup"><span data-stu-id="13bd6-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Mensagem conectada de substituição de módulo quente](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="13bd6-195">Ajudantes de roteamento</span><span class="sxs-lookup"><span data-stu-id="13bd6-195">Routing helpers</span></span>

<span data-ttu-id="13bd6-196">Na maioria dos SPAs baseados no Core, ASP.NET roteamento do lado do cliente é muitas vezes desejado, além do roteamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="13bd6-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="13bd6-197">Os sistemas de roteamento SPA e MVC podem funcionar de forma independente sem interferência.</span><span class="sxs-lookup"><span data-stu-id="13bd6-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="13bd6-198">Há, no entanto, um caso de borda que coloca desafios: identificar 404 respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="13bd6-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="13bd6-199">Considere o cenário em que `/some/page` uma rota sem extensão é usada.</span><span class="sxs-lookup"><span data-stu-id="13bd6-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="13bd6-200">Suponha que a solicitação não corresponda a uma rota do lado do servidor, mas seu padrão corresponde a uma rota lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="13bd6-201">Agora considere uma solicitação `/images/user-512.png`de entrada, que geralmente espera encontrar um arquivo de imagem no servidor.</span><span class="sxs-lookup"><span data-stu-id="13bd6-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="13bd6-202">Se esse caminho de recurso solicitado não corresponder a nenhuma rota do lado do servidor ou&mdash;arquivo estático, é improvável que o aplicativo do lado do cliente lidaria com ele geralmente retornando um código de status HTTP 404 é desejado.</span><span class="sxs-lookup"><span data-stu-id="13bd6-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="13bd6-203">Pré-requisitos para ajudantes de roteamento</span><span class="sxs-lookup"><span data-stu-id="13bd6-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="13bd6-204">Instale o pacote npm de roteamento do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="13bd6-205">Usando angular como exemplo:</span><span class="sxs-lookup"><span data-stu-id="13bd6-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="13bd6-206">Configuração dos ajudantes de roteamento</span><span class="sxs-lookup"><span data-stu-id="13bd6-206">Routing helpers configuration</span></span>

<span data-ttu-id="13bd6-207">Um método `MapSpaFallbackRoute` de extensão `Configure` nomeado é usado no método:</span><span class="sxs-lookup"><span data-stu-id="13bd6-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="13bd6-208">As rotas são avaliadas na ordem em que estão configuradas.</span><span class="sxs-lookup"><span data-stu-id="13bd6-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="13bd6-209">Consequentemente, `default` a rota no exemplo de código anterior é usada primeiro para correspondência de padrões.</span><span class="sxs-lookup"><span data-stu-id="13bd6-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="13bd6-210">Criar um novo projeto</span><span class="sxs-lookup"><span data-stu-id="13bd6-210">Create a new project</span></span>

<span data-ttu-id="13bd6-211">Os JavaScript Services fornecem modelos de aplicativos pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="13bd6-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="13bd6-212">O SpaServices é usado nesses modelos em conjunto com diferentes frameworks e bibliotecas, como Angular, React e Redux.</span><span class="sxs-lookup"><span data-stu-id="13bd6-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="13bd6-213">Esses modelos podem ser instalados através do .NET Core CLI executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="13bd6-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="13bd6-214">Uma lista de modelos spa disponíveis é exibida:</span><span class="sxs-lookup"><span data-stu-id="13bd6-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="13bd6-215">Modelos</span><span class="sxs-lookup"><span data-stu-id="13bd6-215">Templates</span></span>                                 | <span data-ttu-id="13bd6-216">Nome curto</span><span class="sxs-lookup"><span data-stu-id="13bd6-216">Short Name</span></span> | <span data-ttu-id="13bd6-217">Linguagem</span><span class="sxs-lookup"><span data-stu-id="13bd6-217">Language</span></span> | <span data-ttu-id="13bd6-218">Marcas</span><span class="sxs-lookup"><span data-stu-id="13bd6-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="13bd6-219">Núcleo de ASP.NET MVC com Angular</span><span class="sxs-lookup"><span data-stu-id="13bd6-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="13bd6-220">angular</span><span class="sxs-lookup"><span data-stu-id="13bd6-220">angular</span></span>    | <span data-ttu-id="13bd6-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="13bd6-221">[C#]</span></span>     | <span data-ttu-id="13bd6-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="13bd6-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="13bd6-223">MVC ASP.NET Core com React.js</span><span class="sxs-lookup"><span data-stu-id="13bd6-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="13bd6-224">react</span><span class="sxs-lookup"><span data-stu-id="13bd6-224">react</span></span>      | <span data-ttu-id="13bd6-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="13bd6-225">[C#]</span></span>     | <span data-ttu-id="13bd6-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="13bd6-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="13bd6-227">MVC ASP.NET Core com React.js e Redux</span><span class="sxs-lookup"><span data-stu-id="13bd6-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="13bd6-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="13bd6-228">reactredux</span></span> | <span data-ttu-id="13bd6-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="13bd6-229">[C#]</span></span>     | <span data-ttu-id="13bd6-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="13bd6-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="13bd6-231">Para criar um novo projeto usando um dos modelos de SPA, inclua o **Nome Curto** do modelo no novo comando [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="13bd6-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="13bd6-232">O comando a seguir cria um aplicativo Angular com ASP.NET MVC Core configurado para o lado do servidor:</span><span class="sxs-lookup"><span data-stu-id="13bd6-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="13bd6-233">Defina o modo de configuração em tempo de execução</span><span class="sxs-lookup"><span data-stu-id="13bd6-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="13bd6-234">Existem dois modos de configuração de tempo de execução principais:</span><span class="sxs-lookup"><span data-stu-id="13bd6-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="13bd6-235">**Desenvolvimento**:</span><span class="sxs-lookup"><span data-stu-id="13bd6-235">**Development**:</span></span>
  * <span data-ttu-id="13bd6-236">Inclui mapas de origem para facilitar a depuração.</span><span class="sxs-lookup"><span data-stu-id="13bd6-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="13bd6-237">Não otimiza o código do lado do cliente para o desempenho.</span><span class="sxs-lookup"><span data-stu-id="13bd6-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="13bd6-238">**Produção**:</span><span class="sxs-lookup"><span data-stu-id="13bd6-238">**Production**:</span></span>
  * <span data-ttu-id="13bd6-239">Exclui mapas de origem.</span><span class="sxs-lookup"><span data-stu-id="13bd6-239">Excludes source maps.</span></span>
  * <span data-ttu-id="13bd6-240">Otimiza o código do lado do cliente através de agrupamento e minificação.</span><span class="sxs-lookup"><span data-stu-id="13bd6-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="13bd6-241">ASP.NET Core usa uma `ASPNETCORE_ENVIRONMENT` variável de ambiente nomeada para armazenar o modo de configuração.</span><span class="sxs-lookup"><span data-stu-id="13bd6-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="13bd6-242">Para obter mais informações, consulte [Definir o ambiente](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="13bd6-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="13bd6-243">Executar com .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="13bd6-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="13bd6-244">Restaurar os pacotes NuGet e npm necessários executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="13bd6-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="13bd6-245">Construir e executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="13bd6-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="13bd6-246">O aplicativo começa no localhost de acordo com o [modo de configuração em tempo de execução](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="13bd6-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="13bd6-247">Navegar para `http://localhost:5000` o navegador exibe a página de aterrissagem.</span><span class="sxs-lookup"><span data-stu-id="13bd6-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="13bd6-248">Execute com o Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="13bd6-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="13bd6-249">Abra o arquivo *.csproj* gerado pelo novo comando [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="13bd6-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="13bd6-250">Os pacotes NuGet e npm necessários são restaurados automaticamente após a abertura do projeto.</span><span class="sxs-lookup"><span data-stu-id="13bd6-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="13bd6-251">Este processo de restauração pode levar até alguns minutos, e o aplicativo está pronto para ser executado quando for concluído.</span><span class="sxs-lookup"><span data-stu-id="13bd6-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="13bd6-252">Clique no botão verde `Ctrl + F5`ou pressione , e o navegador abre para a página de aterrissagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="13bd6-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="13bd6-253">O aplicativo é executado no localhost de acordo com o [modo de configuração em tempo de execução](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="13bd6-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="13bd6-254">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="13bd6-254">Test the app</span></span>

<span data-ttu-id="13bd6-255">Os modelos do SpaServices são pré-configurados para executar testes do lado do cliente usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="13bd6-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="13bd6-256">Jasmine é uma estrutura de teste de unidade popular para JavaScript, enquanto Karma é um corredor de teste para esses testes.</span><span class="sxs-lookup"><span data-stu-id="13bd6-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="13bd6-257">O Karma está configurado para trabalhar com o [Webpack Dev Middleware de](#webpack-dev-middleware) tal forma que o desenvolvedor não é obrigado a parar e executar o teste toda vez que as alterações são feitas.</span><span class="sxs-lookup"><span data-stu-id="13bd6-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="13bd6-258">Seja o código que corre contra o caso de teste ou o caso de teste em si, o teste é executado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="13bd6-259">Usando o aplicativo Angular como exemplo, dois casos de `CounterComponent` teste jasmine já estão previstos para o arquivo *counter.component.spec.ts:*</span><span class="sxs-lookup"><span data-stu-id="13bd6-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="13bd6-260">Abra o prompt de comando no diretório *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="13bd6-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="13bd6-261">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="13bd6-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="13bd6-262">O script lança o corredor de teste Karma, que lê as configurações definidas no arquivo *karma.conf.js.*</span><span class="sxs-lookup"><span data-stu-id="13bd6-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="13bd6-263">Entre outras configurações, o *karma.conf.js* identifica os arquivos `files` de teste a serem executados através de sua matriz:</span><span class="sxs-lookup"><span data-stu-id="13bd6-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="13bd6-264">Publicar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="13bd6-264">Publish the app</span></span>

<span data-ttu-id="13bd6-265">Consulte [esta edição do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) para obter mais informações sobre a publicação no Azure.</span><span class="sxs-lookup"><span data-stu-id="13bd6-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="13bd6-266">A combinação dos ativos gerados do lado do cliente e dos artefatos ASP.NET Core publicados em um pacote pronto para implantação pode ser complicado.</span><span class="sxs-lookup"><span data-stu-id="13bd6-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="13bd6-267">Felizmente, o SpaServices orquestra todo esse processo de publicação `RunWebpack`com um alvo msbuild personalizado chamado :</span><span class="sxs-lookup"><span data-stu-id="13bd6-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="13bd6-268">O alvo do MSBuild tem as seguintes responsabilidades:</span><span class="sxs-lookup"><span data-stu-id="13bd6-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="13bd6-269">Restaure os pacotes npm.</span><span class="sxs-lookup"><span data-stu-id="13bd6-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="13bd6-270">Crie uma compilação de grau de produção dos ativos de terceiros e do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="13bd6-271">Crie uma compilação de grau de produção dos ativos personalizados do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="13bd6-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="13bd6-272">Copie os ativos gerados pelo Webpack para a pasta de publicação.</span><span class="sxs-lookup"><span data-stu-id="13bd6-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="13bd6-273">O destino MSBuild é invocado ao ser executado:</span><span class="sxs-lookup"><span data-stu-id="13bd6-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="13bd6-274">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="13bd6-274">Additional resources</span></span>

* [<span data-ttu-id="13bd6-275">Docs angulares</span><span class="sxs-lookup"><span data-stu-id="13bd6-275">Angular Docs</span></span>](https://angular.io/docs)
