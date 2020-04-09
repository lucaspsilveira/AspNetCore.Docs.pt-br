---
title: ASP.NET Blazor core manipulação de eventos
author: guardrex
description: Saiba Blazormais sobre os recursos de manipulação de eventos, incluindo tipos de argumentos de eventos, retornos de eventos e gerenciamento de eventos padrão do navegador.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511360"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="78ef9-103">ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="78ef9-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="78ef9-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="78ef9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="78ef9-105">Os componentes da navalha fornecem recursos de manuseio de eventos.</span><span class="sxs-lookup"><span data-stu-id="78ef9-105">Razor components provide event handling features.</span></span> <span data-ttu-id="78ef9-106">Para um atributo [`@on{EVENT}`](xref:mvc/views/razor#onevent) de elemento `@onclick`HTML nomeado (por exemplo) com um valor digitado pelo delegado, um componente Razor trata o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="78ef9-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="78ef9-107">O código a `UpdateHeading` seguir chama o método quando o botão é selecionado na ui:</span><span class="sxs-lookup"><span data-stu-id="78ef9-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="78ef9-108">O código a `CheckChanged` seguir chama o método quando a caixa de seleção é alterada na ui:</span><span class="sxs-lookup"><span data-stu-id="78ef9-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="78ef9-109">Os manipuladores de eventos também podem <xref:System.Threading.Tasks.Task>ser assíncronos e retornar um .</span><span class="sxs-lookup"><span data-stu-id="78ef9-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="78ef9-110">Não há necessidade de chamar manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="78ef9-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="78ef9-111">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="78ef9-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="78ef9-112">No exemplo a `UpdateHeading` seguir, é chamado assíncronamente quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="78ef9-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="78ef9-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="78ef9-113">Event argument types</span></span>

<span data-ttu-id="78ef9-114">Para alguns eventos, tipos de argumento de eventos são permitidos.</span><span class="sxs-lookup"><span data-stu-id="78ef9-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="78ef9-115">Especificar um tipo de evento na chamada do método só é necessário se o tipo de evento for usado no método.</span><span class="sxs-lookup"><span data-stu-id="78ef9-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="78ef9-116">Os `EventArgs` suportes são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="78ef9-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="78ef9-117">Evento</span><span class="sxs-lookup"><span data-stu-id="78ef9-117">Event</span></span>            | <span data-ttu-id="78ef9-118">Classe</span><span class="sxs-lookup"><span data-stu-id="78ef9-118">Class</span></span>                | <span data-ttu-id="78ef9-119">Eventos e notas do DOM</span><span class="sxs-lookup"><span data-stu-id="78ef9-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="78ef9-120">Área de transferência</span><span class="sxs-lookup"><span data-stu-id="78ef9-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="78ef9-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="78ef9-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="78ef9-122">Arrastar</span><span class="sxs-lookup"><span data-stu-id="78ef9-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="78ef9-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="78ef9-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="78ef9-124">`DataTransfer`e `DataTransferItem` manter dados de itens arrastados.</span><span class="sxs-lookup"><span data-stu-id="78ef9-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="78ef9-125">Erro</span><span class="sxs-lookup"><span data-stu-id="78ef9-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="78ef9-126">Evento</span><span class="sxs-lookup"><span data-stu-id="78ef9-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="78ef9-127">*Geral*</span><span class="sxs-lookup"><span data-stu-id="78ef9-127">*General*</span></span><br><span data-ttu-id="78ef9-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="78ef9-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="78ef9-129">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="78ef9-129">*Clipboard*</span></span><br><span data-ttu-id="78ef9-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="78ef9-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="78ef9-131">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="78ef9-131">*Input*</span></span><br><span data-ttu-id="78ef9-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="78ef9-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="78ef9-133">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="78ef9-133">*Media*</span></span><br><span data-ttu-id="78ef9-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="78ef9-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="78ef9-135">Focus</span><span class="sxs-lookup"><span data-stu-id="78ef9-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="78ef9-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="78ef9-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="78ef9-137">Não inclui suporte `relatedTarget`para .</span><span class="sxs-lookup"><span data-stu-id="78ef9-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="78ef9-138">Entrada</span><span class="sxs-lookup"><span data-stu-id="78ef9-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="78ef9-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="78ef9-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="78ef9-140">Teclado</span><span class="sxs-lookup"><span data-stu-id="78ef9-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="78ef9-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="78ef9-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="78ef9-142">Mouse</span><span class="sxs-lookup"><span data-stu-id="78ef9-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="78ef9-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="78ef9-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="78ef9-144">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="78ef9-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="78ef9-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="78ef9-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="78ef9-146">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="78ef9-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="78ef9-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="78ef9-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="78ef9-148">Andamento</span><span class="sxs-lookup"><span data-stu-id="78ef9-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="78ef9-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="78ef9-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="78ef9-150">Toque</span><span class="sxs-lookup"><span data-stu-id="78ef9-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="78ef9-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="78ef9-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="78ef9-152">`TouchPoint`representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="78ef9-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="78ef9-153">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="78ef9-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="78ef9-154">[Classes EventArgs na fonte de referência ASP.NET Core (versão dotnet/aspnetcore/ramo 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="78ef9-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="78ef9-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Inclui informações sobre quais elementos HTML suportam cada evento DOM.</span><span class="sxs-lookup"><span data-stu-id="78ef9-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="78ef9-156">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="78ef9-156">Lambda expressions</span></span>

<span data-ttu-id="78ef9-157">[Expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="78ef9-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="78ef9-158">Muitas vezes é conveniente fechar sobre valores adicionais, como quando iterasobre um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="78ef9-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="78ef9-159">O exemplo a seguir cria três `UpdateHeading` botões, cada`MouseEventArgs`um dos quais`buttonNumber`chama passando um argumento de evento ( ) e seu número de botão ( ) quando selecionado na ui:</span><span class="sxs-lookup"><span data-stu-id="78ef9-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="78ef9-160">**Não** use a variável`i`loop `for` ( ) em um loop diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="78ef9-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="78ef9-161">Caso contrário, a mesma variável é usada `i`por todas as expressões lambda fazendo com que o valor seja o mesmo em todas as lambdas.</span><span class="sxs-lookup"><span data-stu-id="78ef9-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="78ef9-162">Sempre capture seu valor em`buttonNumber` uma variável local (no exemplo anterior) e, em seguida, use-o.</span><span class="sxs-lookup"><span data-stu-id="78ef9-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="78ef9-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="78ef9-163">EventCallback</span></span>

<span data-ttu-id="78ef9-164">Um cenário comum com componentes aninhados é o desejo de&mdash;executar o método `onclick` de um componente pai quando um evento componente filho ocorre, por exemplo, quando um evento ocorre na criança.</span><span class="sxs-lookup"><span data-stu-id="78ef9-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="78ef9-165">Para expor eventos entre componentes, use um `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="78ef9-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="78ef9-166">Um componente pai pode atribuir um método de `EventCallback`retorno de chamada a um componente filho .</span><span class="sxs-lookup"><span data-stu-id="78ef9-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="78ef9-167">O `ChildComponent` aplicativo na amostra *(Components/ChildComponent.razor)* demonstra `onclick` como o manipulador de `EventCallback` um botão é `ParentComponent`configurado para receber um delegado da amostra .</span><span class="sxs-lookup"><span data-stu-id="78ef9-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="78ef9-168">O `EventCallback` é digitado com `MouseEventArgs`, `onclick` o que é apropriado para um evento a partir de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="78ef9-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="78ef9-169">O `ParentComponent` conjunto da `EventCallback<T>` criança`OnClickCallback`( `ShowMessage` ) ao seu método.</span><span class="sxs-lookup"><span data-stu-id="78ef9-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="78ef9-170">*Páginas/ParentComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="78ef9-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="78ef9-171">Quando o botão estiver `ChildComponent`selecionado no:</span><span class="sxs-lookup"><span data-stu-id="78ef9-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="78ef9-172">O `ParentComponent`método `ShowMessage` é chamado.</span><span class="sxs-lookup"><span data-stu-id="78ef9-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="78ef9-173">`_messageText`é atualizado e exibido `ParentComponent`no .</span><span class="sxs-lookup"><span data-stu-id="78ef9-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="78ef9-174">Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no`ShowMessage`método de retorno de chamada ().</span><span class="sxs-lookup"><span data-stu-id="78ef9-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="78ef9-175">`StateHasChanged`é chamado automaticamente para `ParentComponent`rerenderizar o , assim como eventos crianças acionam a rerenderização do componente em manipuladores de eventos que executam dentro da criança.</span><span class="sxs-lookup"><span data-stu-id="78ef9-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="78ef9-176">`EventCallback`e `EventCallback<T>` permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="78ef9-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="78ef9-177">`EventCallback<T>`é fortemente digitado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="78ef9-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="78ef9-178">`EventCallback`é fracamente digitado e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="78ef9-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="78ef9-179">Invoque `EventCallback<T>` `InvokeAsync` um `EventCallback` ou <xref:System.Threading.Tasks.Task>com e aguarde o:</span><span class="sxs-lookup"><span data-stu-id="78ef9-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="78ef9-180">Use `EventCallback` `EventCallback<T>` e para os parâmetros dos componentes de manuseio e vinculação de eventos.</span><span class="sxs-lookup"><span data-stu-id="78ef9-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="78ef9-181">Prefira o `EventCallback<T>` fortemente digitado sobre `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="78ef9-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="78ef9-182">`EventCallback<T>`fornece melhor feedback de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="78ef9-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="78ef9-183">Semelhante a outros manipuladores de eventos de ui, especificando que o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="78ef9-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="78ef9-184">Use `EventCallback` quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="78ef9-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="78ef9-185">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="78ef9-185">Prevent default actions</span></span>

<span data-ttu-id="78ef9-186">Use [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) o atributo diretiva para impedir a ação padrão de um evento.</span><span class="sxs-lookup"><span data-stu-id="78ef9-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="78ef9-187">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="78ef9-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="78ef9-188">No exemplo a seguir, o comportamento padrão `@onkeypress:preventDefault` é impedido por especificar o atributo diretiva.</span><span class="sxs-lookup"><span data-stu-id="78ef9-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="78ef9-189">O contador incrementa, **+** e a chave não `<input>` é capturada no valor do elemento:</span><span class="sxs-lookup"><span data-stu-id="78ef9-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="78ef9-190">Especificar `@on{EVENT}:preventDefault` o atributo sem `@on{EVENT}:preventDefault="true"`um valor é equivalente a .</span><span class="sxs-lookup"><span data-stu-id="78ef9-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="78ef9-191">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="78ef9-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="78ef9-192">No exemplo a `_shouldPreventDefault` seguir, é `bool` um `false`campo definido para ou: `true`</span><span class="sxs-lookup"><span data-stu-id="78ef9-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="78ef9-193">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="78ef9-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="78ef9-194">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="78ef9-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="78ef9-195">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="78ef9-195">Stop event propagation</span></span>

<span data-ttu-id="78ef9-196">Use [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) o atributo diretiva para interromper a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="78ef9-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="78ef9-197">No exemplo a seguir, selecionar a caixa de seleção impede que eventos de clique da segunda criança `<div>` se prossigam para o pai `<div>`:</span><span class="sxs-lookup"><span data-stu-id="78ef9-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
