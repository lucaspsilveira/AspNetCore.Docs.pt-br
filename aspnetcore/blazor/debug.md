---
title: Depurar ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84239221"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="8a1d0-103">Depurar ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="8a1d0-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="8a1d0-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="8a1d0-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="8a1d0-105">Os aplicativos Webassembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).</span><span class="sxs-lookup"><span data-stu-id="8a1d0-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="8a1d0-106">Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="8a1d0-107">Os cenários disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-107">Available scenarios include:</span></span>

* <span data-ttu-id="8a1d0-108">Definir e remover pontos de interrupção.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="8a1d0-109">Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="8a1d0-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="8a1d0-110">Etapa única (<kbd>F10</kbd>) por meio do código.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="8a1d0-111">Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="8a1d0-112">Na exibição *locais* , observe os valores de variáveis locais.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="8a1d0-113">Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="8a1d0-114">Por enquanto, você *não pode*:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-114">For now, you *can't*:</span></span>

* <span data-ttu-id="8a1d0-115">Interromper em exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="8a1d0-116">Pontos de interrupção de acesso durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="8a1d0-117">Continuaremos a melhorar a experiência de depuração em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8a1d0-118">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="8a1d0-118">Prerequisites</span></span>

<span data-ttu-id="8a1d0-119">A depuração requer um dos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="8a1d0-120">Microsoft Edge (versão 80 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="8a1d0-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="8a1d0-121">Google Chrome (versão 70 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="8a1d0-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="8a1d0-122">Habilitar depuração para Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a1d0-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="8a1d0-123">Para habilitar a depuração para um Blazor aplicativo Webassembly existente, atualize o *launchSettings.jsno* arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-123">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="8a1d0-124">Depois de atualizado, o *launchSettings.jsno* arquivo deve ser semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-124">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="8a1d0-125">A `inspectUri` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="8a1d0-126">Permite que o IDE detecte que o aplicativo é um Blazor aplicativo Webassembly.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="8a1d0-127">Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="8a1d0-128">Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="8a1d0-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a1d0-129">Visual Studio</span></span>

<span data-ttu-id="8a1d0-130">Para depurar um Blazor aplicativo Webassembly no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="8a1d0-131">Crie um novo Blazor aplicativo Webassembly hospedado ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="8a1d0-132">Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="8a1d0-133">Defina um ponto de interrupção no *Counter. Razor* no `IncrementCount` método.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-133">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="8a1d0-134">Navegue até a guia **contador** e selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-134">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="8a1d0-136">Confira o valor do `currentCount` campo na janela locais:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="8a1d0-138">Pressione <kbd>F5</kbd> para continuar a execução.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="8a1d0-139">Ao depurar seu Blazor aplicativo Webassembly, você também pode depurar o código do servidor:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="8a1d0-140">Defina um ponto de interrupção na página *FetchData. Razor* no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="8a1d0-140">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="8a1d0-141">Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="8a1d0-142">Navegue até a guia **buscar dados** para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-142">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="8a1d0-144">Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="8a1d0-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="8a1d0-146">Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="8a1d0-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a1d0-147">Visual Studio Code</span></span>

<span data-ttu-id="8a1d0-148">Para depurar um Blazor aplicativo Webassembly no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="8a1d0-149">Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="8a1d0-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="8a1d0-152">Depurar Blazor Webassembly autônomo</span><span class="sxs-lookup"><span data-stu-id="8a1d0-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="8a1d0-153">Abra o Blazor aplicativo Webassembly autônomo no vs Code.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="8a1d0-154">Se você receber a notificação a seguir, será necessária uma configuração adicional para habilitar a depuração:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="8a1d0-155">Confirme se você tem as extensões corretas instaladas.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="8a1d0-156">Confirme se a depuração de visualização do JavaScript está habilitada.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="8a1d0-157">Recarregue a janela.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-157">Reload the window.</span></span>

   ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="8a1d0-159">Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="8a1d0-160">Quando solicitado, selecione a opção \*\* Blazor Webassembly Debug\*\* para iniciar a depuração.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista de opções de depuração disponíveis](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="8a1d0-162">O aplicativo autônomo é iniciado e um navegador de depuração é aberto.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="8a1d0-163">Defina um ponto de interrupção no `IncrementCount` método no `Counter` componente e, em seguida, selecione o botão para atingir o ponto de interrupção:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="8a1d0-165">Depurar Blazor Webassembly hospedado</span><span class="sxs-lookup"><span data-stu-id="8a1d0-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="8a1d0-166">Abra o Blazor aplicativo Webassembly hospedado no vs Code.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="8a1d0-167">Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="8a1d0-168">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-168">Select **Yes**.</span></span>

   ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="8a1d0-170">Na janela de seleção, selecione o projeto de *servidor* dentro da solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="8a1d0-171">Um *launch.jsno* arquivo é gerado com a configuração de inicialização para iniciar o depurador.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-171">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="8a1d0-172">Anexar a uma sessão de depuração existente</span><span class="sxs-lookup"><span data-stu-id="8a1d0-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="8a1d0-173">Para anexar a um aplicativo em execução Blazor , crie um *launch.jsno* arquivo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-173">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="8a1d0-174">Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="8a1d0-175">Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="8a1d0-176">Opções de configuração de inicialização</span><span class="sxs-lookup"><span data-stu-id="8a1d0-176">Launch configuration options</span></span>

<span data-ttu-id="8a1d0-177">As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="8a1d0-178">Opção</span><span class="sxs-lookup"><span data-stu-id="8a1d0-178">Option</span></span>    | <span data-ttu-id="8a1d0-179">Descrição</span><span class="sxs-lookup"><span data-stu-id="8a1d0-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="8a1d0-180">Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor aplicativo Webassembly ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="8a1d0-181">A URL a ser aberta no navegador durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="8a1d0-182">O padrão é `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="8a1d0-183">O navegador a ser iniciado para a sessão de depuração.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="8a1d0-184">Definir como `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="8a1d0-185">O padrão é `chrome`.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="8a1d0-186">Usado para gerar logs do depurador JS.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="8a1d0-187">Defina como `true` para gerar logs.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="8a1d0-188">Deve ser definido como `true` se estiver iniciando e depurando um Blazor aplicativo Webassembly hospedado.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="8a1d0-189">Especifica o caminho absoluto do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="8a1d0-190">Deve ser definido se um aplicativo for servido de uma sub-roteiro.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="8a1d0-191">O número de milissegundos a aguardar a sessão de depuração ser anexada.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="8a1d0-192">O padrão é 30.000 milissegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="8a1d0-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="8a1d0-193">Uma referência ao executável para executar o servidor do aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="8a1d0-194">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="8a1d0-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="8a1d0-195">O diretório de trabalho para o qual iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-195">The working directory to launch the app under.</span></span> <span data-ttu-id="8a1d0-196">Deve ser definido se `hosted` for `true` .</span><span class="sxs-lookup"><span data-stu-id="8a1d0-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="8a1d0-197">As variáveis de ambiente a serem fornecidas ao processo iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="8a1d0-198">Aplicável somente se `hosted` for definido como `true` .</span><span class="sxs-lookup"><span data-stu-id="8a1d0-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="8a1d0-199">Exemplo de configurações de inicialização</span><span class="sxs-lookup"><span data-stu-id="8a1d0-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="8a1d0-200">Iniciar e depurar um Blazor aplicativo Webassembly autônomo</span><span class="sxs-lookup"><span data-stu-id="8a1d0-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="8a1d0-201">Anexar a um aplicativo em execução em uma URL especificada</span><span class="sxs-lookup"><span data-stu-id="8a1d0-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="8a1d0-202">Iniciar e depurar um Blazor aplicativo Webassembly hospedado</span><span class="sxs-lookup"><span data-stu-id="8a1d0-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="8a1d0-203">Depurar no navegador</span><span class="sxs-lookup"><span data-stu-id="8a1d0-203">Debug in the browser</span></span>

1. <span data-ttu-id="8a1d0-204">Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="8a1d0-205">Pressione <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="8a1d0-206">O navegador deve ser executado com a depuração remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="8a1d0-207">Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="8a1d0-208">A página de erro contém instruções para executar o navegador com a porta de depuração aberta para que o Blazor proxy de depuração possa se conectar ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="8a1d0-209">*Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="8a1d0-210">Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="8a1d0-211">Expanda cada assembly e localize os arquivos de origem *. cs* / *. Razor* disponíveis para depuração.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-211">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="8a1d0-212">Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="8a1d0-213">Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="8a1d0-214">fornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="8a1d0-215">Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="8a1d0-216">O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).</span><span class="sxs-lookup"><span data-stu-id="8a1d0-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="8a1d0-217">Mapas de origem do navegador</span><span class="sxs-lookup"><span data-stu-id="8a1d0-217">Browser source maps</span></span>

<span data-ttu-id="8a1d0-218">Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="8a1d0-219">No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="8a1d0-220">Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8a1d0-221">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="8a1d0-221">Troubleshoot</span></span>

<span data-ttu-id="8a1d0-222">Se você estiver executando erros, as dicas a seguir podem ajudar:</span><span class="sxs-lookup"><span data-stu-id="8a1d0-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="8a1d0-223">Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="8a1d0-224">No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="8a1d0-225">Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="8a1d0-226">Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="8a1d0-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
