---
<span data-ttu-id="18fe1-101">Título: ' Debug ASP.NET Core Blazor Webassembly ' autor: guardrex Descrição: ' saiba como depurar Blazor aplicativos '.</span><span class="sxs-lookup"><span data-stu-id="18fe1-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="18fe1-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/31/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="18fe1-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:</span></span>
- <span data-ttu-id="18fe1-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18fe1-103">'Blazor'</span></span>
- <span data-ttu-id="18fe1-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18fe1-104">'Identity'</span></span>
- <span data-ttu-id="18fe1-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18fe1-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="18fe1-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18fe1-106">'Razor'</span></span>
- <span data-ttu-id="18fe1-107">SignalRUID: mais incrivelmente/depurar</span><span class="sxs-lookup"><span data-stu-id="18fe1-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="18fe1-108">Depurar ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="18fe1-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="18fe1-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="18fe1-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="18fe1-110">Os aplicativos Webassembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="18fe1-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="18fe1-111">Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="18fe1-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="18fe1-112">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="18fe1-112">Available scenarios include:</span></span>

* <span data-ttu-id="18fe1-113">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="18fe1-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="18fe1-114">Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="18fe1-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="18fe1-115">Etapa única (<kbd>F10</kbd>) por meio do código.</span><span class="sxs-lookup"><span data-stu-id="18fe1-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="18fe1-116">Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="18fe1-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="18fe1-117">Na exibição *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="18fe1-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="18fe1-118">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="18fe1-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="18fe1-119">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="18fe1-119">For now, you *can't*:</span></span>

* <span data-ttu-id="18fe1-120">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="18fe1-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="18fe1-121">Pontos de interrupção de acesso durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18fe1-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="18fe1-122">Continuaremos a melhorar a experiência de depuração em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="18fe1-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18fe1-123">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="18fe1-123">Prerequisites</span></span>

<span data-ttu-id="18fe1-124">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="18fe1-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="18fe1-125">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="18fe1-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="18fe1-126">Google Chrome (versão 70 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="18fe1-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="18fe1-127">Habilitar depuração para Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18fe1-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="18fe1-128">Para habilitar a depuração para um Blazor aplicativo Webassembly existente, atualize o arquivo *launchSettings. JSON* no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="18fe1-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="18fe1-129">Depois de atualizado, o arquivo *launchSettings. JSON* deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="18fe1-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="18fe1-130">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="18fe1-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="18fe1-131">Permite que o IDE detecte que o aplicativo é um Blazor aplicativo Webassembly.</span><span class="sxs-lookup"><span data-stu-id="18fe1-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="18fe1-132">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="18fe1-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="18fe1-133">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="18fe1-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="18fe1-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18fe1-134">Visual Studio</span></span>

<span data-ttu-id="18fe1-135">Para depurar um Blazor aplicativo Webassembly no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="18fe1-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="18fe1-136">Crie um novo Blazor aplicativo Webassembly hospedado ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18fe1-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="18fe1-137">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="18fe1-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="18fe1-138">Defina um ponto de interrupção no *Counter. Razor* no `IncrementCount` método.</span><span class="sxs-lookup"><span data-stu-id="18fe1-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="18fe1-139">Navegue até a guia **contador** e selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="18fe1-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="18fe1-141">Confira o valor do `currentCount` campo na janela locais:</span><span class="sxs-lookup"><span data-stu-id="18fe1-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="18fe1-143">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="18fe1-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="18fe1-144">Ao depurar seu Blazor aplicativo Webassembly, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="18fe1-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="18fe1-145">Defina um ponto de interrupção na página *FetchData. Razor* no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="18fe1-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="18fe1-146">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="18fe1-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="18fe1-147">Navegue até a guia **buscar dados** para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor:</span><span class="sxs-lookup"><span data-stu-id="18fe1-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="18fe1-149">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="18fe1-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="18fe1-151">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.</span><span class="sxs-lookup"><span data-stu-id="18fe1-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="18fe1-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18fe1-152">Visual Studio Code</span></span>

<span data-ttu-id="18fe1-153">Para depurar um Blazor aplicativo Webassembly no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="18fe1-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="18fe1-154">Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="18fe1-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="18fe1-157">Depurar Blazor Webassembly autônomo</span><span class="sxs-lookup"><span data-stu-id="18fe1-157">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="18fe1-158">Abra o Blazor aplicativo Webassembly autônomo no vs Code.</span><span class="sxs-lookup"><span data-stu-id="18fe1-158">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="18fe1-159">Se você receber a notificação a seguir, será necessária uma configuração adicional para habilitar a depuração:</span><span class="sxs-lookup"><span data-stu-id="18fe1-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="18fe1-160">Confirme se você tem as extensões corretas instaladas.</span><span class="sxs-lookup"><span data-stu-id="18fe1-160">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="18fe1-161">Confirme se a depuração de visualização do JavaScript está habilitada.</span><span class="sxs-lookup"><span data-stu-id="18fe1-161">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="18fe1-162">Recarregue a janela.</span><span class="sxs-lookup"><span data-stu-id="18fe1-162">Reload the window.</span></span>

   ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="18fe1-164">Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.</span><span class="sxs-lookup"><span data-stu-id="18fe1-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="18fe1-165">Quando solicitado, selecione a opção \*\* Blazor Webassembly Debug\*\* para iniciar a depuração.</span><span class="sxs-lookup"><span data-stu-id="18fe1-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista de opções de depuração disponíveis](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="18fe1-167">O aplicativo autônomo é iniciado e um navegador de depuração é aberto.</span><span class="sxs-lookup"><span data-stu-id="18fe1-167">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="18fe1-168">Defina um ponto de interrupção no `IncrementCount` método no `Counter` componente e, em seguida, selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="18fe1-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="18fe1-170">Depurar Blazor Webassembly hospedado</span><span class="sxs-lookup"><span data-stu-id="18fe1-170">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="18fe1-171">Abra o Blazor aplicativo Webassembly hospedado no vs Code.</span><span class="sxs-lookup"><span data-stu-id="18fe1-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="18fe1-172">Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida.</span><span class="sxs-lookup"><span data-stu-id="18fe1-172">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="18fe1-173">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="18fe1-173">Select **Yes**.</span></span>

   ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="18fe1-175">Na janela de seleção, selecione o projeto de *servidor* dentro da solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="18fe1-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="18fe1-176">Um arquivo *Launch. JSON* é gerado com a configuração de inicialização para iniciar o depurador.</span><span class="sxs-lookup"><span data-stu-id="18fe1-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="18fe1-177">Anexar a uma sessão de depuração existente</span><span class="sxs-lookup"><span data-stu-id="18fe1-177">Attach to an existing debugging session</span></span>

<span data-ttu-id="18fe1-178">Para anexar a um aplicativo em execução Blazor , crie um arquivo *Launch. JSON* com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="18fe1-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="18fe1-179">Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos.</span><span class="sxs-lookup"><span data-stu-id="18fe1-179">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="18fe1-180">Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="18fe1-180">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="18fe1-181">Opções de configuração de inicialização</span><span class="sxs-lookup"><span data-stu-id="18fe1-181">Launch configuration options</span></span>

<span data-ttu-id="18fe1-182">As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração.</span><span class="sxs-lookup"><span data-stu-id="18fe1-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="18fe1-183">Opção</span><span class="sxs-lookup"><span data-stu-id="18fe1-183">Option</span></span>    | <span data-ttu-id="18fe1-184">Descrição</span><span class="sxs-lookup"><span data-stu-id="18fe1-184">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="18fe1-185">Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor aplicativo Webassembly ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução.</span><span class="sxs-lookup"><span data-stu-id="18fe1-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="18fe1-186">A URL a ser aberta no navegador durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="18fe1-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="18fe1-187">O padrão é `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="18fe1-187">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="18fe1-188">O navegador a ser iniciado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="18fe1-188">The browser to launch for the debugging session.</span></span> <span data-ttu-id="18fe1-189">Definir como `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="18fe1-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="18fe1-190">O padrão é `chrome`.</span><span class="sxs-lookup"><span data-stu-id="18fe1-190">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="18fe1-191">Usado para gerar logs do depurador JS.</span><span class="sxs-lookup"><span data-stu-id="18fe1-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="18fe1-192">Defina como `true` para gerar logs.</span><span class="sxs-lookup"><span data-stu-id="18fe1-192">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="18fe1-193">Deve ser definido como `true` se estiver iniciando e depurando um Blazor aplicativo Webassembly hospedado.</span><span class="sxs-lookup"><span data-stu-id="18fe1-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="18fe1-194">Especifica o caminho absoluto do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="18fe1-194">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="18fe1-195">Deve ser definido se um aplicativo for servido de uma sub-roteiro.</span><span class="sxs-lookup"><span data-stu-id="18fe1-195">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="18fe1-196">O número de milissegundos a aguardar a sessão de depuração ser anexada.</span><span class="sxs-lookup"><span data-stu-id="18fe1-196">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="18fe1-197">O padrão é 30.000 milissegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="18fe1-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="18fe1-198">Uma referência ao executável para executar o servidor do aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="18fe1-198">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="18fe1-199">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="18fe1-199">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="18fe1-200">O diretório de trabalho para o qual iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18fe1-200">The working directory to launch the app under.</span></span> <span data-ttu-id="18fe1-201">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="18fe1-201">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="18fe1-202">As variáveis de ambiente a serem fornecidas ao processo iniciado.</span><span class="sxs-lookup"><span data-stu-id="18fe1-202">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="18fe1-203">Aplicável somente se `hosted` for definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="18fe1-203">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="18fe1-204">Exemplo de configurações de inicialização</span><span class="sxs-lookup"><span data-stu-id="18fe1-204">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="18fe1-205">Iniciar e depurar um Blazor aplicativo Webassembly autônomo</span><span class="sxs-lookup"><span data-stu-id="18fe1-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="18fe1-206">Anexar a um aplicativo em execução em uma URL especificada</span><span class="sxs-lookup"><span data-stu-id="18fe1-206">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="18fe1-207">Iniciar e depurar um Blazor aplicativo Webassembly hospedado</span><span class="sxs-lookup"><span data-stu-id="18fe1-207">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="18fe1-208">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="18fe1-208">Debug in the browser</span></span>

1. <span data-ttu-id="18fe1-209">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="18fe1-209">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="18fe1-210">Pressione <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="18fe1-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="18fe1-211">O navegador deve ser executado com a depuração remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="18fe1-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="18fe1-212">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada.</span><span class="sxs-lookup"><span data-stu-id="18fe1-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="18fe1-213">A página de erro contém instruções para executar o navegador com a porta de depuração aberta para que o Blazor proxy de depuração possa se conectar ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18fe1-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="18fe1-214">*Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.</span><span class="sxs-lookup"><span data-stu-id="18fe1-214">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="18fe1-215">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18fe1-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="18fe1-216">Expanda cada assembly e localize os arquivos de origem *. cs* / *. Razor* disponíveis para depuração.</span><span class="sxs-lookup"><span data-stu-id="18fe1-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="18fe1-217">Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado.</span><span class="sxs-lookup"><span data-stu-id="18fe1-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="18fe1-218">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="18fe1-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="18fe1-219">fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="18fe1-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="18fe1-220">Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="18fe1-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="18fe1-221">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="18fe1-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="18fe1-222">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="18fe1-222">Browser source maps</span></span>

<span data-ttu-id="18fe1-223">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="18fe1-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="18fe1-224">No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="18fe1-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="18fe1-225">Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="18fe1-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="18fe1-226">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="18fe1-226">Troubleshoot</span></span>

<span data-ttu-id="18fe1-227">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="18fe1-227">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="18fe1-228">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="18fe1-228">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="18fe1-229">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="18fe1-229">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="18fe1-230">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="18fe1-230">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="18fe1-231">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="18fe1-231">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
