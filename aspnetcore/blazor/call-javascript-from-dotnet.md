---
title: Chamar funções JavaScript de métodos .NET no ASP.NET CoreBlazor
author: guardrex
description: Saiba como invocar funções JavaScript a partir de métodos .NET em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5c22220871fdba7fea43c38fa4bc826c07135ffc
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060027"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="5de28-103">Chamar funções JavaScript de métodos .NET no ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="5de28-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="5de28-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5de28-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5de28-105">Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5de28-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="5de28-106">Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).</span><span class="sxs-lookup"><span data-stu-id="5de28-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="5de28-107">Este artigo aborda a invocação de funções JavaScript do .NET.</span><span class="sxs-lookup"><span data-stu-id="5de28-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="5de28-108">Para obter informações sobre como chamar métodos .NET do JavaScript, consulte <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="5de28-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="5de28-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5de28-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5de28-110">Para chamar o JavaScript do .NET, use a <xref:Microsoft.JSInterop.IJSRuntime> abstração.</span><span class="sxs-lookup"><span data-stu-id="5de28-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="5de28-111">Para emitir chamadas de interoperabilidade JS, insira a <xref:Microsoft.JSInterop.IJSRuntime> abstração em seu componente.</span><span class="sxs-lookup"><span data-stu-id="5de28-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="5de28-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON.</span><span class="sxs-lookup"><span data-stu-id="5de28-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="5de28-113">O identificador de função é relativo ao escopo global ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="5de28-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="5de28-114">Se você quiser chamar `window.someScope.someFunction` , o identificador será `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="5de28-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="5de28-115">Não é necessário registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="5de28-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="5de28-116">O tipo de retorno `T` também deve ser serializável em JSON.</span><span class="sxs-lookup"><span data-stu-id="5de28-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="5de28-117">`T`deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.</span><span class="sxs-lookup"><span data-stu-id="5de28-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="5de28-118">Para Blazor Server aplicativos com pré-processamento habilitado, não é possível chamar o JavaScript durante o pré-processamento inicial.</span><span class="sxs-lookup"><span data-stu-id="5de28-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="5de28-119">As chamadas de interoperabilidade do JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="5de28-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="5de28-120">Para obter mais informações, consulte a seção [detectar quando um Blazor Server aplicativo está sendo renderizado](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="5de28-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="5de28-121">O exemplo a seguir se baseia em [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) um decodificador baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5de28-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="5de28-122">O exemplo demonstra como invocar uma função JavaScript de um método C# que descarrega um requisito do código do desenvolvedor para uma API JavaScript existente.</span><span class="sxs-lookup"><span data-stu-id="5de28-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="5de28-123">A função JavaScript aceita uma matriz de bytes de um método C#, decodifica a matriz e retorna o texto para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="5de28-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="5de28-124">Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma função JavaScript que usa `TextDecoder` para decodificar uma matriz passada e retornar o valor decodificado:</span><span class="sxs-lookup"><span data-stu-id="5de28-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="5de28-125">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( `.js` ) com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="5de28-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="5de28-126">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="5de28-126">The following component:</span></span>

* <span data-ttu-id="5de28-127">Invoca a `convertArray` função JavaScript usando `JSRuntime` quando um botão de componente ( **`Convert Array`** ) é selecionado.</span><span class="sxs-lookup"><span data-stu-id="5de28-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="5de28-128">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="5de28-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="5de28-129">A cadeia de caracteres é retornada para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="5de28-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="5de28-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="5de28-130">IJSRuntime</span></span>

<span data-ttu-id="5de28-131">Para usar a <xref:Microsoft.JSInterop.IJSRuntime> abstração, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="5de28-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="5de28-132">Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração no Razor componente ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5de28-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="5de28-133">Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5de28-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="5de28-134">A função é chamada com <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="5de28-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="5de28-135">Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração em uma classe ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="5de28-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="5de28-136">Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5de28-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="5de28-137">A função é chamada com `JSRuntime.InvokeAsync` e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="5de28-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="5de28-138">Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use o `[Inject]` atributo:</span><span class="sxs-lookup"><span data-stu-id="5de28-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="5de28-139">No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interagem com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="5de28-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="5de28-140">`showPrompt`: Produz uma solicitação para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="5de28-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="5de28-141">`displayWelcome`: Atribui uma mensagem de boas-vindas do chamador a um objeto DOM com um `id` de `welcome` .</span><span class="sxs-lookup"><span data-stu-id="5de28-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="5de28-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="5de28-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="5de28-143">Coloque a `<script>` marca que faz referência ao arquivo JavaScript no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="5de28-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="5de28-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="5de28-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="5de28-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="5de28-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="5de28-146">Não coloque uma `<script>` marca em um arquivo de componente porque a `<script>` marca não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="5de28-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="5de28-147">Os métodos .NET interoperam com as funções JavaScript no `exampleJsInterop.js` arquivo chamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5de28-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="5de28-148">A <xref:Microsoft.JSInterop.IJSRuntime> abstração é assíncrona para permitir Blazor Server cenários.</span><span class="sxs-lookup"><span data-stu-id="5de28-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="5de28-149">Se o aplicativo for um Blazor WebAssembly aplicativo e você quiser invocar uma função JavaScript de forma síncrona, downcast <xref:Microsoft.JSInterop.IJSInProcessRuntime> e chame <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="5de28-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="5de28-150">Recomendamos que a maioria das bibliotecas de interoperabilidade do JS use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="5de28-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="5de28-151">O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade do JS.</span><span class="sxs-lookup"><span data-stu-id="5de28-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="5de28-152">O componente:</span><span class="sxs-lookup"><span data-stu-id="5de28-152">The component:</span></span>

* <span data-ttu-id="5de28-153">Recebe a entrada do usuário por meio de um prompt do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5de28-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="5de28-154">Retorna o texto para o componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="5de28-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="5de28-155">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="5de28-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="5de28-156">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="5de28-156">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="5de28-157">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5de28-157">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="5de28-158">Quando `TriggerJsPrompt` é executado selecionando o botão do componente **`Trigger JavaScript Prompt`** , a `showPrompt` função JavaScript fornecida no `wwwroot/exampleJsInterop.js` arquivo é chamada.</span><span class="sxs-lookup"><span data-stu-id="5de28-158">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="5de28-159">A `showPrompt` função aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente.</span><span class="sxs-lookup"><span data-stu-id="5de28-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="5de28-160">O componente armazena o nome do usuário em uma variável local, `name` .</span><span class="sxs-lookup"><span data-stu-id="5de28-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="5de28-161">A cadeia de caracteres armazenada em `name` é incorporada a uma mensagem de boas-vindas, que é passada para uma função JavaScript, `displayWelcome` , que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="5de28-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="5de28-162">Chamar uma função JavaScript void</span><span class="sxs-lookup"><span data-stu-id="5de28-162">Call a void JavaScript function</span></span>

<span data-ttu-id="5de28-163">As funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são chamadas com <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5de28-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="5de28-164">Detectar quando um Blazor Server aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="5de28-164">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="5de28-165">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="5de28-165">Capture references to elements</span></span>

<span data-ttu-id="5de28-166">Alguns cenários de interoperabilidade JS exigem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="5de28-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="5de28-167">Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização, ou talvez seja necessário chamar APIs do tipo comando em um elemento, como `focus` ou `play` .</span><span class="sxs-lookup"><span data-stu-id="5de28-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="5de28-168">Capture referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="5de28-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="5de28-169">Adicione um `@ref` atributo ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="5de28-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="5de28-170">Defina um campo do tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cujo nome corresponde ao valor do `@ref` atributo.</span><span class="sxs-lookup"><span data-stu-id="5de28-170">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="5de28-171">O exemplo a seguir mostra a captura de uma referência ao `username` `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="5de28-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="5de28-172">Use apenas uma referência de elemento para converter o conteúdo de um elemento vazio que não interaja com Blazor .</span><span class="sxs-lookup"><span data-stu-id="5de28-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="5de28-173">Esse cenário é útil quando uma API de terceiros fornece conteúdo para o elemento.</span><span class="sxs-lookup"><span data-stu-id="5de28-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="5de28-174">Como o Blazor não interage com o elemento, não há possibilidade de um conflito entre a Blazor representação do elemento e o dom.</span><span class="sxs-lookup"><span data-stu-id="5de28-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="5de28-175">No exemplo a seguir, é *perigoso* modificar o conteúdo da lista não ordenada ( `ul` ) porque Blazor interage com o dom para preencher os itens de lista deste elemento ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="5de28-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="5de28-176">Se a interoperabilidade do JS converter o conteúdo do elemento `MyList` e Blazor tentar aplicar diffs ao elemento, as diferenças não corresponderão ao dom.</span><span class="sxs-lookup"><span data-stu-id="5de28-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="5de28-177">No que diz respeito ao código .NET, um <xref:Microsoft.AspNetCore.Components.ElementReference> é um identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="5de28-177">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="5de28-178">A *única* coisa que você pode fazer com o <xref:Microsoft.AspNetCore.Components.ElementReference> é passá-lo para o código JavaScript por meio da interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="5de28-178">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="5de28-179">Quando você faz isso, o código do lado do JavaScript recebe uma `HTMLElement` instância, que pode ser usada com APIs dom normais.</span><span class="sxs-lookup"><span data-stu-id="5de28-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="5de28-180">Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:</span><span class="sxs-lookup"><span data-stu-id="5de28-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="5de28-181">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="5de28-181">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="5de28-182">Para chamar uma função JavaScript que não retorna um valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5de28-182">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="5de28-183">O código a seguir define o foco na entrada de nome de usuário chamando a função JavaScript anterior com o capturado <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="5de28-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="5de28-184">Para usar um método de extensão, crie um método de extensão estático que receba a <xref:Microsoft.JSInterop.IJSRuntime> instância:</span><span class="sxs-lookup"><span data-stu-id="5de28-184">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="5de28-185">O `Focus` método é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="5de28-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="5de28-186">O exemplo a seguir pressupõe que o `Focus` método está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="5de28-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="5de28-187">A `username` variável é populada apenas depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="5de28-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="5de28-188">Se um não populado <xref:Microsoft.AspNetCore.Components.ElementReference> for passado para o código JavaScript, o código JavaScript receberá um valor de `null` .</span><span class="sxs-lookup"><span data-stu-id="5de28-188">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="5de28-189">Para manipular referências de elemento após a conclusão da renderização do componente (para definir o foco inicial em um elemento), use os [ `OnAfterRenderAsync` métodos de ciclo de vida do `OnAfterRender` componente ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="5de28-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="5de28-190">Ao trabalhar com tipos genéricos e retornar um valor, use <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="5de28-190">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="5de28-191">`GenericMethod`é chamado diretamente no objeto com um tipo.</span><span class="sxs-lookup"><span data-stu-id="5de28-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="5de28-192">O exemplo a seguir pressupõe que o `GenericMethod` está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="5de28-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="5de28-193">Elementos de referência entre componentes</span><span class="sxs-lookup"><span data-stu-id="5de28-193">Reference elements across components</span></span>

<span data-ttu-id="5de28-194">Uma <xref:Microsoft.AspNetCore.Components.ElementReference> só é garantida válida no método de um componente <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> (e uma referência de elemento é a `struct` ), portanto, uma referência de elemento não pode ser passada entre componentes.</span><span class="sxs-lookup"><span data-stu-id="5de28-194">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="5de28-195">Para que um componente pai torne uma referência de elemento disponível para outros componentes, o componente pai pode:</span><span class="sxs-lookup"><span data-stu-id="5de28-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="5de28-196">Permitir que componentes filho registrem retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="5de28-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="5de28-197">Invoque os retornos de chamada registrados durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> evento com a referência de elemento passada.</span><span class="sxs-lookup"><span data-stu-id="5de28-197">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="5de28-198">Indiretamente, essa abordagem permite que os componentes filho interajam com a referência de elemento do pai.</span><span class="sxs-lookup"><span data-stu-id="5de28-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="5de28-199">O exemplo a seguir Blazor WebAssembly ilustra a abordagem.</span><span class="sxs-lookup"><span data-stu-id="5de28-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="5de28-200">No `<head>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="5de28-200">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="5de28-201">No `<body>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="5de28-201">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="5de28-202">`Pages/Index.razor`(componente pai):</span><span class="sxs-lookup"><span data-stu-id="5de28-202">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="5de28-203">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="5de28-203">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="5de28-204">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5de28-204">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="5de28-205">`Shared/SurveyPrompt.razor`(componente filho):</span><span class="sxs-lookup"><span data-stu-id="5de28-205">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="5de28-206">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="5de28-206">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="5de28-207">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5de28-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="5de28-208">Proteger chamadas Interop JS</span><span class="sxs-lookup"><span data-stu-id="5de28-208">Harden JS interop calls</span></span>

<span data-ttu-id="5de28-209">A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável.</span><span class="sxs-lookup"><span data-stu-id="5de28-209">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="5de28-210">Por padrão, um Blazor Server aplicativo expira as chamadas de interoperabilidade js no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="5de28-210">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="5de28-211">Se um aplicativo puder tolerar um tempo limite mais agressivo, defina o tempo limite usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="5de28-211">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="5de28-212">Globalmente no `Startup.ConfigureServices` , especifique o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="5de28-212">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="5de28-213">Por invocação no código do componente, uma única chamada pode especificar o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="5de28-213">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="5de28-214">Para obter mais informações sobre esgotamento de recursos, consulte <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="5de28-214">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="5de28-215">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="5de28-215">Avoid circular object references</span></span>

<span data-ttu-id="5de28-216">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="5de28-216">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="5de28-217">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="5de28-217">.NET method calls.</span></span>
* <span data-ttu-id="5de28-218">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="5de28-218">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="5de28-219">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="5de28-219">For more information, see the following issues:</span></span>

* [<span data-ttu-id="5de28-220">Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="5de28-220">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="5de28-221">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)</span><span class="sxs-lookup"><span data-stu-id="5de28-221">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="5de28-222">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5de28-222">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="5de28-223">Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="5de28-223">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="5de28-224">[Executar grandes transferências de dados em Blazor Server aplicativos](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="5de28-224">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
