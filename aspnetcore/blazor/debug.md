---
<span data-ttu-id="6f0fb-101">Título: ' Debug ASP.NET Core Blazor Webassembly ' autor: guardrex Descrição: ' saiba como depurar Blazor aplicativos '.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="6f0fb-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/29/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/29/2020 no-loc:</span></span>
- <span data-ttu-id="6f0fb-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f0fb-103">'Blazor'</span></span>
- <span data-ttu-id="6f0fb-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f0fb-104">'Identity'</span></span>
- <span data-ttu-id="6f0fb-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f0fb-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f0fb-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f0fb-106">'Razor'</span></span>
- <span data-ttu-id="6f0fb-107">SignalRUID: mais incrivelmente/depurar</span><span class="sxs-lookup"><span data-stu-id="6f0fb-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="6f0fb-108">Depurar ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="6f0fb-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="6f0fb-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="6f0fb-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="6f0fb-110">Os aplicativos Webassembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="6f0fb-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="6f0fb-111">Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="6f0fb-112">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-112">Available scenarios include:</span></span>

* <span data-ttu-id="6f0fb-113">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="6f0fb-114">Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="6f0fb-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="6f0fb-115">Etapa única (<kbd>F10</kbd>) por meio do código.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="6f0fb-116">Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="6f0fb-117">Na exibição *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="6f0fb-118">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="6f0fb-119">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-119">For now, you *can't*:</span></span>

* <span data-ttu-id="6f0fb-120">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="6f0fb-121">Pontos de interrupção de acesso durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="6f0fb-122">Continuaremos a melhorar a experiência de depuração em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6f0fb-123">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6f0fb-123">Prerequisites</span></span>

<span data-ttu-id="6f0fb-124">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="6f0fb-125">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="6f0fb-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="6f0fb-126">Google Chrome (versão 70 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="6f0fb-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="6f0fb-127">Habilitar depuração para Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f0fb-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="6f0fb-128">Para habilitar a depuração para um Blazor aplicativo Webassembly existente, atualize o arquivo *launchSettings. JSON* no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="6f0fb-129">Depois de atualizado, o arquivo *launchSettings. JSON* deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="6f0fb-130">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="6f0fb-131">Permite que o IDE detecte que o aplicativo é um Blazor aplicativo Webassembly.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="6f0fb-132">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="6f0fb-133">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="6f0fb-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f0fb-134">Visual Studio</span></span>

<span data-ttu-id="6f0fb-135">Para depurar um Blazor aplicativo Webassembly no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="6f0fb-136">Crie um novo Blazor aplicativo Webassembly hospedado ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="6f0fb-137">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="6f0fb-138">Defina um ponto de interrupção no *Counter. Razor* no `IncrementCount` método.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="6f0fb-139">Navegue até a guia **contador** e selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="6f0fb-141">Confira o valor do `currentCount` campo na janela locais:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="6f0fb-143">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="6f0fb-144">Ao depurar seu Blazor aplicativo Webassembly, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="6f0fb-145">Defina um ponto de interrupção na página *FetchData. Razor* no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="6f0fb-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="6f0fb-146">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="6f0fb-147">Navegue até a guia **buscar dados** para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="6f0fb-149">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="6f0fb-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="6f0fb-151">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="6f0fb-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f0fb-152">Visual Studio Code</span></span>

<span data-ttu-id="6f0fb-153">Para depurar um Blazor aplicativo Webassembly no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="6f0fb-154">Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="6f0fb-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="6f0fb-157">Abra um Blazor aplicativo Webassembly existente com a depuração habilitada.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-157">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="6f0fb-158">Se você receber a notificação a seguir que a configuração adicional é necessária para habilitar a depuração, confirme se você tem as extensões corretas instaladas e a depuração de visualização do JavaScript habilitada e recarregue a janela:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-158">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="6f0fb-160">Uma notificação oferece para adicionar os ativos necessários ao aplicativo para criação e depuração.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-160">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="6f0fb-161">Selecione **Sim**:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-161">Select **Yes**:</span></span>

     ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="6f0fb-163">Iniciar o aplicativo no depurador é um processo de duas etapas:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-163">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="6f0fb-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-164">1\.</span></span> <span data-ttu-id="6f0fb-165">**Primeiro**, inicie o aplicativo usando a configuração de inicialização do **.NET Core ( Blazor autônomo)** .</span><span class="sxs-lookup"><span data-stu-id="6f0fb-165">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="6f0fb-166">2 \.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-166">2\.</span></span> <span data-ttu-id="6f0fb-167">**Depois que o aplicativo for iniciado**, inicie o navegador usando o \*\* Blazor assembly da Web de depuração do .NET Core na\*\* configuração de inicialização do Chrome (requer o Chrome).</span><span class="sxs-lookup"><span data-stu-id="6f0fb-167">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="6f0fb-168">Para usar o Edge em vez do Chrome, altere o `type` da configuração de inicialização em *. vscode/Launch. JSON* de `pwa-chrome` para `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="6f0fb-168">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="6f0fb-169">Defina um ponto de interrupção no `IncrementCount` método no `Counter` componente e, em seguida, selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="6f0fb-171">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="6f0fb-171">Debug in the browser</span></span>

1. <span data-ttu-id="6f0fb-172">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-172">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="6f0fb-173">Pressione <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-173">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="6f0fb-174">O navegador deve ser executado com a depuração remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-174">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="6f0fb-175">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-175">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="6f0fb-176">A página de erro contém instruções para executar o navegador com a porta de depuração aberta para que o Blazor proxy de depuração possa se conectar ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-176">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="6f0fb-177">*Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-177">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="6f0fb-178">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-178">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="6f0fb-179">Expanda cada assembly e localize os arquivos de origem *. cs* / *. Razor* disponíveis para depuração.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-179">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="6f0fb-180">Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-180">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="6f0fb-181">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-181">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="6f0fb-182">fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-182"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="6f0fb-183">Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-183">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="6f0fb-184">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="6f0fb-184">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="6f0fb-185">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="6f0fb-185">Browser source maps</span></span>

<span data-ttu-id="6f0fb-186">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-186">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="6f0fb-187">No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-187">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="6f0fb-188">Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-188">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6f0fb-189">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="6f0fb-189">Troubleshoot</span></span>

<span data-ttu-id="6f0fb-190">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="6f0fb-190">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="6f0fb-191">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-191">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="6f0fb-192">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-192">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="6f0fb-193">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-193">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="6f0fb-194">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="6f0fb-194">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
