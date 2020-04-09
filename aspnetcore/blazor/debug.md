---
title: Debug ASP.NET Blazor WebAssembly Central
author: guardrex
description: Aprenda a depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: eaa67d63f6d15249885d78d3de197ae53e73f072
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80381866"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="3787c-103">Debug ASP.NET Blazor WebAssembly Central</span><span class="sxs-lookup"><span data-stu-id="3787c-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="3787c-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="3787c-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="3787c-105">Os aplicativos WebAssembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="3787c-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="3787c-106">Alternativamente, você pode depurar seu aplicativo usando Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3787c-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="3787c-107">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="3787c-107">Available scenarios include:</span></span>

* <span data-ttu-id="3787c-108">Defina e remova pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="3787c-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="3787c-109">Execute o aplicativo com suporte à depuração no Visual Studio e visual Studio Code ( suporte a<kbd>F5).</kbd></span><span class="sxs-lookup"><span data-stu-id="3787c-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="3787c-110">Passo único<kbd>(F10)</kbd>através do código.</span><span class="sxs-lookup"><span data-stu-id="3787c-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="3787c-111">Retomar a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3787c-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="3787c-112">No *visor local,* observe os valores das variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="3787c-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="3787c-113">Consulte a pilha de chamadas, incluindo cadeias de chamadas que vão de JavaScript para .NET e de .NET para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3787c-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="3787c-114">Por enquanto, você *não pode:*</span><span class="sxs-lookup"><span data-stu-id="3787c-114">For now, you *can't*:</span></span>

* <span data-ttu-id="3787c-115">Inspecione as matrizes.</span><span class="sxs-lookup"><span data-stu-id="3787c-115">Inspect arrays.</span></span>
* <span data-ttu-id="3787c-116">Hover para inspecionar membros.</span><span class="sxs-lookup"><span data-stu-id="3787c-116">Hover to inspect members.</span></span>
* <span data-ttu-id="3787c-117">Depurar para dentro ou para fora do código gerenciado.</span><span class="sxs-lookup"><span data-stu-id="3787c-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="3787c-118">Tenha suporte completo para inspecionar tipos de valor.</span><span class="sxs-lookup"><span data-stu-id="3787c-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="3787c-119">Quebre as exceções não tratadas.</span><span class="sxs-lookup"><span data-stu-id="3787c-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="3787c-120">Aperte pontos de interrupção durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3787c-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="3787c-121">Depurar um aplicativo com um trabalhador de serviço.</span><span class="sxs-lookup"><span data-stu-id="3787c-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="3787c-122">Continuaremos a melhorar a experiência de depuração nos próximos lançamentos.</span><span class="sxs-lookup"><span data-stu-id="3787c-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3787c-123">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="3787c-123">Prerequisites</span></span>

<span data-ttu-id="3787c-124">A depuração requer qualquer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="3787c-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="3787c-125">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="3787c-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="3787c-126">Google Chrome (versão 70 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="3787c-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="3787c-127">Habilite a depuração para visual studio e visual studio code</span><span class="sxs-lookup"><span data-stu-id="3787c-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="3787c-128">A depuração é habilitada automaticamente para novos projetos que são criados Blazor usando o ASP.NET modelo de projeto Core 3.2 Preview 3 ou posterior do WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3787c-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template.</span></span>

<span data-ttu-id="3787c-129">Para habilitar a depuração de um aplicativo WebAssembly existente, Blazor atualize o arquivo *launchSettings.json* no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de lançamento:</span><span class="sxs-lookup"><span data-stu-id="3787c-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="3787c-130">Uma vez atualizado, o arquivo *launchSettings.json* deve ser semelhante ao seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="3787c-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="3787c-131">A `inspectUri` propriedade:</span><span class="sxs-lookup"><span data-stu-id="3787c-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="3787c-132">Habilita o IDE para detectar Blazor que o aplicativo é um aplicativo WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3787c-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="3787c-133">Instrui a infra-estrutura de depuração Blazorde scripts para se conectar ao navegador através do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="3787c-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="3787c-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3787c-134">Visual Studio</span></span>

<span data-ttu-id="3787c-135">Para depurar um Blazor aplicativo WebAssembly no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3787c-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="3787c-136">Certifique-se de ter [instalado a versão de pré-visualização mais recente do Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="3787c-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="3787c-137">Crie um novo aplicativo Blazor webAssembly hospedado ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3787c-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="3787c-138">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="3787c-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="3787c-139">Defina um ponto de ruptura `IncrementCount` no *Counter.razor* no método.</span><span class="sxs-lookup"><span data-stu-id="3787c-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="3787c-140">Navegue até a guia **Contador** e selecione o botão para apertar o ponto de ruptura:</span><span class="sxs-lookup"><span data-stu-id="3787c-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="3787c-142">Confira o valor `currentCount` do campo na janela local:</span><span class="sxs-lookup"><span data-stu-id="3787c-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Ver moradores locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="3787c-144">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="3787c-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="3787c-145">Ao depurar Blazor seu aplicativo WebAssembly, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="3787c-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="3787c-146">Defina um ponto de ruptura na `OnInitializedAsync`página *FetchData.razor* em .</span><span class="sxs-lookup"><span data-stu-id="3787c-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="3787c-147">Defina um ponto `WeatherForecastController` de `Get` ruptura no método de ação.</span><span class="sxs-lookup"><span data-stu-id="3787c-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="3787c-148">Navegue até a guia **Buscar dados** para `FetchData` atingir o primeiro ponto de ruptura no componente pouco antes de emitir uma solicitação HTTP para o servidor:</span><span class="sxs-lookup"><span data-stu-id="3787c-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debug Buscar Dados](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="3787c-150">Pressione <kbd>F5</kbd> para continuar a execução e, `WeatherForecastController`em seguida, clique no ponto de partida no servidor no :</span><span class="sxs-lookup"><span data-stu-id="3787c-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="3787c-152">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela de previsão do tempo renderizada.</span><span class="sxs-lookup"><span data-stu-id="3787c-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="3787c-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3787c-153">Visual Studio Code</span></span>

<span data-ttu-id="3787c-154">Para depurar um Blazor aplicativo WebAssembly no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="3787c-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="3787c-155">Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` set para `true`.</span><span class="sxs-lookup"><span data-stu-id="3787c-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="3787c-158">Abra um Blazor aplicativo WebAssembly existente com a depuração ativada.</span><span class="sxs-lookup"><span data-stu-id="3787c-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="3787c-159">Se você receber a seguinte notificação de que a configuração adicional é necessária para ativar a depuração, confirme se você tem as extensões corretas instaladas e a depuração de visualização javaScript ativada e, em seguida, recarregue a janela:</span><span class="sxs-lookup"><span data-stu-id="3787c-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Configuração adicional requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="3787c-161">Uma notificação oferece adicionar os ativos necessários ao aplicativo para construção e depuração.</span><span class="sxs-lookup"><span data-stu-id="3787c-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="3787c-162">Selecione **Sim**:</span><span class="sxs-lookup"><span data-stu-id="3787c-162">Select **Yes**:</span></span>

     ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="3787c-164">Iniciar o aplicativo no depurador é um processo de duas etapas:</span><span class="sxs-lookup"><span data-stu-id="3787c-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="3787c-165">1\.</span><span class="sxs-lookup"><span data-stu-id="3787c-165">1\.</span></span> <span data-ttu-id="3787c-166">**Primeiro,** inicie o aplicativo usando a configuração de lançamento **doBlazor .NET Core Launch (Standalone).**</span><span class="sxs-lookup"><span data-stu-id="3787c-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="3787c-167">2\.</span><span class="sxs-lookup"><span data-stu-id="3787c-167">2\.</span></span> <span data-ttu-id="3787c-168">**Depois que o aplicativo tiver começado,** inicie o navegador usando o \*\* Blazor .NET Core Debug Web Assembly na\*\* configuração de lançamento do Chrome (requer o Chrome).</span><span class="sxs-lookup"><span data-stu-id="3787c-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="3787c-169">Para usar o Edge em `type` vez do Chrome, altere a configuração `pwa-chrome` `pwa-msedge`de lançamento em *.vscode/launch.json* de para .</span><span class="sxs-lookup"><span data-stu-id="3787c-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="3787c-170">Defina um ponto `IncrementCount` de `Counter` ruptura no método no componente e selecione o botão para apertar o ponto de partida:</span><span class="sxs-lookup"><span data-stu-id="3787c-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Contador de depuração em código VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="3787c-172">Depuração no navegador</span><span class="sxs-lookup"><span data-stu-id="3787c-172">Debug in the browser</span></span>

1. <span data-ttu-id="3787c-173">Execute uma compilação de depuração do aplicativo no ambiente Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3787c-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="3787c-174">Pressione <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="3787c-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="3787c-175">O navegador deve ser executado com depuração remota ativada.</span><span class="sxs-lookup"><span data-stu-id="3787c-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="3787c-176">Se a depuração remota estiver desativada, uma página de erro de guia do navegador não será **depurada.**</span><span class="sxs-lookup"><span data-stu-id="3787c-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="3787c-177">A página de erro contém instruções para executar o navegador Blazor com a porta de depuração aberta para que o proxy de depuração possa se conectar ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3787c-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="3787c-178">*Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.</span><span class="sxs-lookup"><span data-stu-id="3787c-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="3787c-179">Uma vez que o navegador esteja em execução com a depuração remota ativada, o atalho do teclado de depuração abre uma nova guia de depurador. Após um momento, a guia **Fontes** mostra uma lista dos conjuntos .NET no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3787c-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="3787c-180">Expanda cada montagem e encontre os arquivos de origem *.cs*/*.razor* disponíveis para depuração.</span><span class="sxs-lookup"><span data-stu-id="3787c-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="3787c-181">Defina pontos de interrupção, volte para a guia do aplicativo e os pontos de interrupção são atingidos quando o código é executado.</span><span class="sxs-lookup"><span data-stu-id="3787c-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="3787c-182">Após um ponto de quebra é atingido, passo único<kbd>(F10</kbd>) através do código ou currículo<kbd>(F8)</kbd>execução do código normalmente.</span><span class="sxs-lookup"><span data-stu-id="3787c-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="3787c-183">fornece um proxy de depuração que implementa o [Protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com . Informações específicas da NET.</span><span class="sxs-lookup"><span data-stu-id="3787c-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="3787c-184">Quando o atalho do teclado Blazor depurar é pressionado, aponte o Chrome DevTools para o proxy.</span><span class="sxs-lookup"><span data-stu-id="3787c-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="3787c-185">O proxy se conecta à janela do navegador que você está procurando depurar (daí a necessidade de ativar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="3787c-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="3787c-186">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="3787c-186">Browser source maps</span></span>

<span data-ttu-id="3787c-187">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta aos seus arquivos de origem originais e são comumente usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="3787c-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="3787c-188">No Blazor entanto, atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="3787c-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="3787c-189">Em Blazor vez disso, a interpretação il dentro do navegador, de modo que os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="3787c-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3787c-190">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="3787c-190">Troubleshoot</span></span>

<span data-ttu-id="3787c-191">Se você estiver com erros, a seguinte dica pode ajudar:</span><span class="sxs-lookup"><span data-stu-id="3787c-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="3787c-192">Na guia **Depurador,** abra as ferramentas do desenvolvedor no seu navegador.</span><span class="sxs-lookup"><span data-stu-id="3787c-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="3787c-193">No console, `localStorage.clear()` execute para remover quaisquer pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="3787c-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
