---
title: Chamar funções JavaScript a partir de métodos .NET no ASP.NET CoreBlazor
author: guardrex
description: Aprenda a invocar funções JavaScript a Blazor partir de métodos .NET em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977009"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="59aa0-103">Chamar funções JavaScript a partir de métodos .NET no ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="59aa0-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="59aa0-104">Por [Javier Calvarro Nelson,](https://github.com/javiercn) [Daniel Roth](https://github.com/danroth27)e Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="59aa0-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="59aa0-105">Um Blazor aplicativo pode invocar funções JavaScript de métodos .NET e métodos .NET a partir de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59aa0-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="59aa0-106">Esses cenários são chamados *de interoperabilidade JavaScript* *(Interop JS).*</span><span class="sxs-lookup"><span data-stu-id="59aa0-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="59aa0-107">Este artigo abrange a invocação de funções JavaScript de .NET.</span><span class="sxs-lookup"><span data-stu-id="59aa0-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="59aa0-108">Para obter informações sobre como chamar métodos <xref:blazor/call-dotnet-from-javascript>.NET do JavaScript, consulte .</span><span class="sxs-lookup"><span data-stu-id="59aa0-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="59aa0-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59aa0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="59aa0-110">Para ligar para JavaScript de `IJSRuntime` .NET, use a abstração.</span><span class="sxs-lookup"><span data-stu-id="59aa0-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="59aa0-111">Para emitir chamadas de interop `IJSRuntime` JS, injete a abstração em seu componente.</span><span class="sxs-lookup"><span data-stu-id="59aa0-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="59aa0-112">O `InvokeAsync<T>` método tem um identificador para a função JavaScript que você deseja invocar juntamente com qualquer número de argumentos serializáveis json.</span><span class="sxs-lookup"><span data-stu-id="59aa0-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="59aa0-113">O identificador de função é`window`relativo ao escopo global ( ).</span><span class="sxs-lookup"><span data-stu-id="59aa0-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="59aa0-114">Se você quiser `window.someScope.someFunction`ligar, o `someScope.someFunction`identificador é .</span><span class="sxs-lookup"><span data-stu-id="59aa0-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="59aa0-115">Não há necessidade de registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="59aa0-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="59aa0-116">O tipo `T` de retorno também deve ser serializável JSON.</span><span class="sxs-lookup"><span data-stu-id="59aa0-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="59aa0-117">`T`deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.</span><span class="sxs-lookup"><span data-stu-id="59aa0-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="59aa0-118">Para Blazor aplicativos do Servidor com pré-renderização ativada, não é possível chamar o JavaScript durante a pré-renderização inicial.</span><span class="sxs-lookup"><span data-stu-id="59aa0-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="59aa0-119">As chamadas interop JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="59aa0-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="59aa0-120">Para obter mais informações, consulte o [Detect quando um Blazor aplicativo do Servidor estiver fazendo pré-renderização.](#detect-when-a-blazor-server-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="59aa0-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="59aa0-121">O exemplo a seguir é baseado no [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), um decodificador baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59aa0-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="59aa0-122">O exemplo demonstra como invocar uma função JavaScript a partir de um método C#.</span><span class="sxs-lookup"><span data-stu-id="59aa0-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="59aa0-123">A função JavaScript aceita uma matriz de bytes de um método C#, decodifica a matriz e retorna o texto ao componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="59aa0-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="59aa0-124">Dentro `<head>` do elementoBlazor de *wwwroot/index.html* ( WebAssembly) ouBlazor *Pages/_Host.cshtml* (Server), forneça uma função JavaScript que usa `TextDecoder` para decodificar um array passado e retornar o valor decodificado:</span><span class="sxs-lookup"><span data-stu-id="59aa0-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="59aa0-125">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado a partir de um arquivo JavaScript *(.js)* com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="59aa0-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="59aa0-126">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="59aa0-126">The following component:</span></span>

* <span data-ttu-id="59aa0-127">Invoca a `convertArray` função JavaScript usando `JSRuntime` quando um botão de componente **(Convert Array)** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="59aa0-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="59aa0-128">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma seqüência de string.</span><span class="sxs-lookup"><span data-stu-id="59aa0-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="59aa0-129">A seqüência é devolvida ao componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="59aa0-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="59aa0-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="59aa0-130">IJSRuntime</span></span>

<span data-ttu-id="59aa0-131">Para utilizar `IJSRuntime` a abstração, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="59aa0-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="59aa0-132">Injete a `IJSRuntime` abstração no componente Razor *(.razor*):</span><span class="sxs-lookup"><span data-stu-id="59aa0-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="59aa0-133">Dentro `<head>` do elementoBlazor de *wwwroot/index.html* ( WebAssembly) ouBlazor *Pages/_Host.cshtml* (Server), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59aa0-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="59aa0-134">A função é `IJSRuntime.InvokeVoidAsync` chamada com e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="59aa0-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="59aa0-135">Injete a `IJSRuntime` abstração em uma classe (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="59aa0-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="59aa0-136">Dentro `<head>` do elementoBlazor de *wwwroot/index.html* ( WebAssembly) ouBlazor *Pages/_Host.cshtml* (Server), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59aa0-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="59aa0-137">A função é `JSRuntime.InvokeAsync` chamada com e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="59aa0-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="59aa0-138">Para geração dinâmica de conteúdo com `[Inject]` [BuildRenderTree,](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)use o atributo:</span><span class="sxs-lookup"><span data-stu-id="59aa0-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="59aa0-139">No aplicativo de amostra do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interage com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="59aa0-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="59aa0-140">`showPrompt`&ndash; Produz um prompt para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="59aa0-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="59aa0-141">`displayWelcome`&ndash; Atribui uma mensagem de boas-vindas do `id` chamador a um objeto DOM com um de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="59aa0-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="59aa0-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="59aa0-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="59aa0-143">Coloque `<script>` a tag que faz referência ao arquivoBlazor JavaScript no arquivo *wwwroot/index.html* (WebAssembly) ou *páginas/_Host.cshtml* (Server).Blazor</span><span class="sxs-lookup"><span data-stu-id="59aa0-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="59aa0-144">*wwwroot/index.html* Blazor (WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="59aa0-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="59aa0-145">*Páginas/_Host.cshtml* Blazor (Servidor):</span><span class="sxs-lookup"><span data-stu-id="59aa0-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="59aa0-146">Não coloque uma `<script>` tag em um `<script>` arquivo de componente porque a tag não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="59aa0-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="59aa0-147">Os métodos .NET intercalam com as funções JavaScript no arquivo `IJSRuntime.InvokeAsync<T>` *JsInterop.js por* chamada .</span><span class="sxs-lookup"><span data-stu-id="59aa0-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="59aa0-148">A `IJSRuntime` abstração é assíncrona Blazor para permitir cenários do Servidor.</span><span class="sxs-lookup"><span data-stu-id="59aa0-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="59aa0-149">Se o aplicativo Blazor for um aplicativo WebAssembly e você quiser invocar uma `IJSInProcessRuntime` função `Invoke<T>` JavaScript sincronizadamente, abaixado para e chamar em vez disso.</span><span class="sxs-lookup"><span data-stu-id="59aa0-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="59aa0-150">Recomendamos que a maioria das bibliotecas interop JS use as APIs de async para garantir que as bibliotecas estejam disponíveis em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="59aa0-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="59aa0-151">O aplicativo de amostra inclui um componente para demonstrar a interop JS.</span><span class="sxs-lookup"><span data-stu-id="59aa0-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="59aa0-152">O componente:</span><span class="sxs-lookup"><span data-stu-id="59aa0-152">The component:</span></span>

* <span data-ttu-id="59aa0-153">Recebe a entrada do usuário através de um prompt JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59aa0-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="59aa0-154">Retorna o texto ao componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="59aa0-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="59aa0-155">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="59aa0-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="59aa0-156">*Páginas/JSInterop.razor:*</span><span class="sxs-lookup"><span data-stu-id="59aa0-156">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
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

1. <span data-ttu-id="59aa0-157">Quando `TriggerJsPrompt` é executado selecionando o botão **Trigger JavaScript** Prompt `showPrompt` do componente, a função JavaScript fornecida no arquivo *wwwroot/exampleJsInterop.js* é chamada.</span><span class="sxs-lookup"><span data-stu-id="59aa0-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="59aa0-158">A `showPrompt` função aceita a entrada do usuário (nome do usuário), que é codificado por HTML e devolvido ao componente.</span><span class="sxs-lookup"><span data-stu-id="59aa0-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="59aa0-159">O componente armazena o nome do usuário `name`em uma variável local, .</span><span class="sxs-lookup"><span data-stu-id="59aa0-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="59aa0-160">A seqüência de caracteres armazenada `name` é incorporada em uma `displayWelcome`mensagem de boas-vindas, que é passada para uma função JavaScript, que torna a mensagem de boas-vindas em uma tag de título.</span><span class="sxs-lookup"><span data-stu-id="59aa0-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="59aa0-161">Chamada de uma função JavaScript vazia</span><span class="sxs-lookup"><span data-stu-id="59aa0-161">Call a void JavaScript function</span></span>

<span data-ttu-id="59aa0-162">As funções JavaScript que retornam [void(0)/void](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) `IJSRuntime.InvokeVoidAsync`0 ou [indefinidas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são chamadas com .</span><span class="sxs-lookup"><span data-stu-id="59aa0-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="59aa0-163">Detecte Blazor quando um aplicativo do Servidor está fazendo a pré-renderização</span><span class="sxs-lookup"><span data-stu-id="59aa0-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="59aa0-164">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="59aa0-164">Capture references to elements</span></span>

<span data-ttu-id="59aa0-165">Alguns cenários de Interop JS requerem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="59aa0-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="59aa0-166">Por exemplo, uma biblioteca de ia de usuário pode exigir uma referência de elemento para inicialização, ou você pode precisar chamar APIs semelhantes a comandos em um elemento, como `focus` ou `play`.</span><span class="sxs-lookup"><span data-stu-id="59aa0-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="59aa0-167">Capturar referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="59aa0-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="59aa0-168">Adicione `@ref` um atributo ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="59aa0-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="59aa0-169">Defina um `ElementReference` campo de tipo cujo `@ref` nome corresponda ao valor do atributo.</span><span class="sxs-lookup"><span data-stu-id="59aa0-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="59aa0-170">O exemplo a seguir mostra `username` `<input>` a captura de uma referência ao elemento:</span><span class="sxs-lookup"><span data-stu-id="59aa0-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="59aa0-171">Use apenas uma referência de elemento para alterar o conteúdo Blazorde um elemento vazio que não interage com .</span><span class="sxs-lookup"><span data-stu-id="59aa0-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="59aa0-172">Este cenário é útil quando uma API de terceiros fornece conteúdo para o elemento.</span><span class="sxs-lookup"><span data-stu-id="59aa0-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="59aa0-173">Como Blazor não interage com o elemento, não há possibilidade Blazorde um conflito entre a representação do elemento e o DOM.</span><span class="sxs-lookup"><span data-stu-id="59aa0-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="59aa0-174">No exemplo a seguir, é *perigoso* alterar o conteúdo da`ul`lista Blazor não ordenada ( ) porque interage com o DOM para preencher os itens da lista deste elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="59aa0-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="59aa0-175">Se JS interop mutar o `MyList` Blazor conteúdo do elemento e tentar aplicar diffs ao elemento, os difusores não corresponderão ao DOM.</span><span class="sxs-lookup"><span data-stu-id="59aa0-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="59aa0-176">No que diz respeito ao `ElementReference` código .NET, um é uma alça opaca.</span><span class="sxs-lookup"><span data-stu-id="59aa0-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="59aa0-177">A *única* coisa que `ElementReference` você pode fazer com é passá-lo através de código JavaScript via JS interop.</span><span class="sxs-lookup"><span data-stu-id="59aa0-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="59aa0-178">Quando você faz isso, o código lado `HTMLElement` JavaScript recebe uma instância, que pode ser usada com APIs do DOM normais.</span><span class="sxs-lookup"><span data-stu-id="59aa0-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="59aa0-179">Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:</span><span class="sxs-lookup"><span data-stu-id="59aa0-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="59aa0-180">*exemploJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="59aa0-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="59aa0-181">Para chamar uma função JavaScript que não `IJSRuntime.InvokeVoidAsync`retorne um valor, use .</span><span class="sxs-lookup"><span data-stu-id="59aa0-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="59aa0-182">O código a seguir define o foco na entrada de nome `ElementReference`de usuário chamando a função JavaScript anterior com a captura:</span><span class="sxs-lookup"><span data-stu-id="59aa0-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="59aa0-183">Para usar um método de extensão, `IJSRuntime` crie um método de extensão estática que receba a instância:</span><span class="sxs-lookup"><span data-stu-id="59aa0-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="59aa0-184">O `Focus` método é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="59aa0-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="59aa0-185">O exemplo a seguir `Focus` assume que `JsInteropClasses` o método está disponível no namespace:</span><span class="sxs-lookup"><span data-stu-id="59aa0-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="59aa0-186">A `username` variável só é preenchida após a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="59aa0-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="59aa0-187">Se um despovoado `ElementReference` for passado para o código `null`JavaScript, o código JavaScript receberá um valor de .</span><span class="sxs-lookup"><span data-stu-id="59aa0-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="59aa0-188">Para manipular as referências do elemento após a renderização concluída (para definir o foco inicial em um elemento) use os [métodos onAfterRenderAsync ou OnAfterRender.](xref:blazor/lifecycle#after-component-render)</span><span class="sxs-lookup"><span data-stu-id="59aa0-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="59aa0-189">Ao trabalhar com tipos genéricos e devolver um valor, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="59aa0-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="59aa0-190">`GenericMethod`é chamado diretamente no objeto com um tipo.</span><span class="sxs-lookup"><span data-stu-id="59aa0-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="59aa0-191">O exemplo a seguir `GenericMethod` assume que `JsInteropClasses` o está disponível no namespace:</span><span class="sxs-lookup"><span data-stu-id="59aa0-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="59aa0-192">Elementos de referência entre componentes</span><span class="sxs-lookup"><span data-stu-id="59aa0-192">Reference elements across components</span></span>

<span data-ttu-id="59aa0-193">Um `ElementReference` só é garantido válido `OnAfterRender` no método de um `struct`componente (e uma referência de elemento é a), de modo que uma referência de elemento não pode ser passada entre componentes.</span><span class="sxs-lookup"><span data-stu-id="59aa0-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="59aa0-194">Para que um componente pai disponibilize uma referência de elemento a outros componentes, o componente pai pode:</span><span class="sxs-lookup"><span data-stu-id="59aa0-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="59aa0-195">Permitir que os componentes da criança registrem retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="59aa0-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="59aa0-196">Invoque os retornos `OnAfterRender` de chamada registrados durante o evento com a referência do elemento aprovado.</span><span class="sxs-lookup"><span data-stu-id="59aa0-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="59aa0-197">Indiretamente, essa abordagem permite que os componentes da criança interajam com a referência do elemento dos pais.</span><span class="sxs-lookup"><span data-stu-id="59aa0-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="59aa0-198">O Blazor exemplo do WebAssembly a seguir ilustra a abordagem.</span><span class="sxs-lookup"><span data-stu-id="59aa0-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="59aa0-199">Na `<head>` *wwwroot/index.html:*</span><span class="sxs-lookup"><span data-stu-id="59aa0-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="59aa0-200">Na `<body>` *wwwroot/index.html:*</span><span class="sxs-lookup"><span data-stu-id="59aa0-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="59aa0-201">*Páginas/Index.razor* (componente pai):</span><span class="sxs-lookup"><span data-stu-id="59aa0-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="59aa0-202">*Páginas/Index.razor.cs:*</span><span class="sxs-lookup"><span data-stu-id="59aa0-202">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

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
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="59aa0-203">*Compartilhado/SurveyPrompt.razor* (componente filho):</span><span class="sxs-lookup"><span data-stu-id="59aa0-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="59aa0-204">*Compartilhado/SurveyPrompt.razor.cs:*</span><span class="sxs-lookup"><span data-stu-id="59aa0-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a><span data-ttu-id="59aa0-205">Harden JS interop chamadas</span><span class="sxs-lookup"><span data-stu-id="59aa0-205">Harden JS interop calls</span></span>

<span data-ttu-id="59aa0-206">O interop JS pode falhar devido a erros de rede e deve ser tratado como não confiável.</span><span class="sxs-lookup"><span data-stu-id="59aa0-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="59aa0-207">Por padrão, Blazor um aplicativo do Server avalia as chamadas de interop js no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="59aa0-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="59aa0-208">Se um aplicativo pode tolerar um tempo mais agressivo, como 10 segundos, defina o tempo máximo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="59aa0-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="59aa0-209">Globalmente `Startup.ConfigureServices`em , especifique o tempo mais caro:</span><span class="sxs-lookup"><span data-stu-id="59aa0-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="59aa0-210">Por invocação no código do componente, uma única chamada pode especificar o tempo hámenos:</span><span class="sxs-lookup"><span data-stu-id="59aa0-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="59aa0-211">Para obter mais informações <xref:security/blazor/server>sobre o esgotamento dos recursos, consulte .</span><span class="sxs-lookup"><span data-stu-id="59aa0-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="59aa0-212">Evite referências de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="59aa0-212">Avoid circular object references</span></span>

<span data-ttu-id="59aa0-213">Objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="59aa0-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="59aa0-214">Chamadas do método .NET.</span><span class="sxs-lookup"><span data-stu-id="59aa0-214">.NET method calls.</span></span>
* <span data-ttu-id="59aa0-215">O método JavaScript chama a partir de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="59aa0-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="59aa0-216">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="59aa0-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="59aa0-217">As referências circulares não são suportadas, tome duas (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="59aa0-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="59aa0-218">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="59aa0-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="59aa0-219">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="59aa0-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="59aa0-220">Exemplo InteropComponent.razor (repositório dotnet/AspNetCore GitHub, ramo de versão 3.1)</span><span class="sxs-lookup"><span data-stu-id="59aa0-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="59aa0-221">[Realizar grandes transferências Blazor de dados em aplicativos do Servidor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="59aa0-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
