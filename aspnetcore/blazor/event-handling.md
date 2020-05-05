---
title: Manipulação Blazor de eventos de ASP.NET Core
author: guardrex
description: Saiba mais Blazorsobre os recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: a9b0d0efd4afd4941bd4d93f33adecdf3288992f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767064"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="f494a-103">ASP.NET Core manipulação de eventos mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="f494a-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="f494a-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f494a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f494a-105">Os componentes do Razor fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="f494a-105">Razor components provide event handling features.</span></span> <span data-ttu-id="f494a-106">Para um atributo de elemento HTML [`@on{EVENT}`](xref:mvc/views/razor#onevent) chamado (por exemplo `@onclick`,) com um valor de tipo delegado, um componente Razor trata o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="f494a-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="f494a-107">O código a seguir chama `UpdateHeading` o método quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="f494a-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="f494a-108">O código a seguir chama `CheckChanged` o método quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="f494a-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="f494a-109">Os manipuladores de eventos também podem ser assíncronos <xref:System.Threading.Tasks.Task>e retornar um.</span><span class="sxs-lookup"><span data-stu-id="f494a-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="f494a-110">Não é necessário chamar [StateHasChanged](xref:blazor/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="f494a-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="f494a-111">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="f494a-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="f494a-112">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="f494a-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="f494a-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="f494a-113">Event argument types</span></span>

<span data-ttu-id="f494a-114">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="f494a-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="f494a-115">A especificação de um tipo de evento na chamada do método só será necessária se o tipo de evento for usado no método.</span><span class="sxs-lookup"><span data-stu-id="f494a-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="f494a-116">Com `EventArgs` suporte, são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f494a-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="f494a-117">Evento</span><span class="sxs-lookup"><span data-stu-id="f494a-117">Event</span></span>            | <span data-ttu-id="f494a-118">Classe</span><span class="sxs-lookup"><span data-stu-id="f494a-118">Class</span></span>                | <span data-ttu-id="f494a-119">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="f494a-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="f494a-120">Área de Transferência</span><span class="sxs-lookup"><span data-stu-id="f494a-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="f494a-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="f494a-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="f494a-122">Arrastar</span><span class="sxs-lookup"><span data-stu-id="f494a-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="f494a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="f494a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="f494a-124">`DataTransfer`e `DataTransferItem` mantenha arrastado os dados do item.</span><span class="sxs-lookup"><span data-stu-id="f494a-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="f494a-125">Erro</span><span class="sxs-lookup"><span data-stu-id="f494a-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="f494a-126">Evento</span><span class="sxs-lookup"><span data-stu-id="f494a-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="f494a-127">*Geral*</span><span class="sxs-lookup"><span data-stu-id="f494a-127">*General*</span></span><br><span data-ttu-id="f494a-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="f494a-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="f494a-129">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="f494a-129">*Clipboard*</span></span><br><span data-ttu-id="f494a-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="f494a-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="f494a-131">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="f494a-131">*Input*</span></span><br><span data-ttu-id="f494a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="f494a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="f494a-133">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="f494a-133">*Media*</span></span><br><span data-ttu-id="f494a-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="f494a-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="f494a-135">Focus</span><span class="sxs-lookup"><span data-stu-id="f494a-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="f494a-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="f494a-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="f494a-137">Não inclui suporte para `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="f494a-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="f494a-138">Entrada</span><span class="sxs-lookup"><span data-stu-id="f494a-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="f494a-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="f494a-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="f494a-140">Keyboard</span><span class="sxs-lookup"><span data-stu-id="f494a-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="f494a-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="f494a-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="f494a-142">Mouse</span><span class="sxs-lookup"><span data-stu-id="f494a-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="f494a-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="f494a-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="f494a-144">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="f494a-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="f494a-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="f494a-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="f494a-146">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="f494a-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="f494a-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="f494a-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="f494a-148">Andamento</span><span class="sxs-lookup"><span data-stu-id="f494a-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="f494a-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="f494a-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="f494a-150">Touch</span><span class="sxs-lookup"><span data-stu-id="f494a-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="f494a-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="f494a-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="f494a-152">`TouchPoint`representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="f494a-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="f494a-153">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="f494a-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="f494a-154">[Classes EventArgs no ASP.NET Core fonte de referência (dotNet/aspnetcore versão/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="f494a-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="f494a-155">[MDN Web docs: o GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.</span><span class="sxs-lookup"><span data-stu-id="f494a-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="f494a-156">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="f494a-156">Lambda expressions</span></span>

<span data-ttu-id="f494a-157">As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="f494a-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="f494a-158">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="f494a-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="f494a-159">O exemplo a seguir cria três botões, cada um dos `UpdateHeading` quais chamadas passando um argumento`MouseEventArgs`de evento () e seu`buttonNumber`número de botão () quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="f494a-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="f494a-160">**Não** use a variável de loop (`i`) em um `for` loop diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="f494a-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="f494a-161">Caso contrário, a mesma variável é usada por todas as `i`expressões lambda, fazendo com que o valor seja o mesmo em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="f494a-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="f494a-162">Sempre Capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e use-o.</span><span class="sxs-lookup"><span data-stu-id="f494a-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="f494a-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="f494a-163">EventCallback</span></span>

<span data-ttu-id="f494a-164">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre&mdash;um evento de componente filho, por `onclick` exemplo, quando um evento ocorre no filho.</span><span class="sxs-lookup"><span data-stu-id="f494a-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="f494a-165">Para expor eventos entre componentes, use um `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="f494a-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="f494a-166">Um componente pai pode atribuir um método de retorno de chamada a um `EventCallback`componente filho.</span><span class="sxs-lookup"><span data-stu-id="f494a-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="f494a-167">O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o `onclick` manipulador de um botão é configurado para receber `EventCallback` um delegado de exemplo `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="f494a-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="f494a-168">O `EventCallback` é digitado `MouseEventArgs`com, que é apropriado para `onclick` um evento de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="f494a-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="f494a-169">O `ParentComponent` define o filho `EventCallback<T>` (`OnClickCallback`) para seu `ShowMessage` método.</span><span class="sxs-lookup"><span data-stu-id="f494a-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="f494a-170">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="f494a-170">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="f494a-171">Quando o botão estiver selecionado no `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="f494a-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="f494a-172">O `ParentComponent` `ShowMessage` método é chamado.</span><span class="sxs-lookup"><span data-stu-id="f494a-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="f494a-173">`_messageText`é atualizado e exibido no `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="f494a-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="f494a-174">Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no método do retorno de`ShowMessage`chamada ().</span><span class="sxs-lookup"><span data-stu-id="f494a-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="f494a-175">`StateHasChanged`é chamado automaticamente para renderizar novamente `ParentComponent`o, assim como eventos filho, rerenderização de componente em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="f494a-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="f494a-176">`EventCallback`e `EventCallback<T>` permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="f494a-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="f494a-177">`EventCallback<T>`é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="f494a-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="f494a-178">`EventCallback`está com tipo fraco e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="f494a-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="f494a-179">Invocar `EventCallback` um `EventCallback<T>` ou `InvokeAsync` com e aguardar <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="f494a-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="f494a-180">Use `EventCallback` e `EventCallback<T>` para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="f494a-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="f494a-181">Prefira o tipo fortemente `EventCallback<T>` tipado `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="f494a-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="f494a-182">`EventCallback<T>`fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="f494a-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="f494a-183">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="f494a-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="f494a-184">Use `EventCallback` quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="f494a-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="f494a-185">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="f494a-185">Prevent default actions</span></span>

<span data-ttu-id="f494a-186">Use o [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributo diretiva para impedir a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="f494a-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="f494a-187">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="f494a-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="f494a-188">No exemplo a seguir, o comportamento padrão é impedido pela especificação `@onkeypress:preventDefault` do atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="f494a-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="f494a-189">O contador é incrementado e a **+** chave não é capturada `<input>` no valor do elemento:</span><span class="sxs-lookup"><span data-stu-id="f494a-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="f494a-190">Especificar o `@on{EVENT}:preventDefault` atributo sem um valor é equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="f494a-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="f494a-191">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="f494a-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="f494a-192">No exemplo a seguir, `_shouldPreventDefault` é um `bool` conjunto de campos como `true` ou `false`:</span><span class="sxs-lookup"><span data-stu-id="f494a-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="f494a-193">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="f494a-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="f494a-194">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="f494a-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="f494a-195">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="f494a-195">Stop event propagation</span></span>

<span data-ttu-id="f494a-196">Use o [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributo diretiva para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="f494a-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="f494a-197">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do `<div>` segundo filho se propaguem `<div>`para o pai:</span><span class="sxs-lookup"><span data-stu-id="f494a-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
