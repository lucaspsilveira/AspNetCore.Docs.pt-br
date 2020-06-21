---
title: Manipulação de eventos de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: d15d8355b321c2938d68a109c5115db78280f6b6
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122081"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="590db-103">Manipulação de eventos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="590db-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="590db-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="590db-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="590db-105">os componentes fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="590db-105"> components provide event handling features.</span></span> <span data-ttu-id="590db-106">Para um atributo de elemento HTML chamado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por exemplo, `@onclick` ) com um valor de tipo delegado, um Razor componente trata o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="590db-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="590db-107">O código a seguir chama o `UpdateHeading` método quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="590db-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="590db-108">O código a seguir chama o `CheckChanged` método quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="590db-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="590db-109">Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="590db-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="590db-110">Não é necessário chamar [StateHasChanged](xref:blazor/components/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="590db-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="590db-111">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="590db-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="590db-112">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="590db-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="590db-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="590db-113">Event argument types</span></span>

<span data-ttu-id="590db-114">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="590db-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="590db-115">A especificação de um parâmetro de evento em uma definição de método de evento é opcional e será necessária somente se o tipo de evento for usado no método.</span><span class="sxs-lookup"><span data-stu-id="590db-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="590db-116">No exemplo a seguir, o `MouseEventArgs` argumento de evento é usado no `ShowMessage` método para definir o texto da mensagem:</span><span class="sxs-lookup"><span data-stu-id="590db-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="590db-117">Com suporte, <xref:System.EventArgs> são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="590db-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="590db-118">Evento</span><span class="sxs-lookup"><span data-stu-id="590db-118">Event</span></span>            | <span data-ttu-id="590db-119">Classe</span><span class="sxs-lookup"><span data-stu-id="590db-119">Class</span></span>                | <span data-ttu-id="590db-120">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="590db-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="590db-121">Área de Transferência</span><span class="sxs-lookup"><span data-stu-id="590db-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="590db-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="590db-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="590db-123">Arrastar</span><span class="sxs-lookup"><span data-stu-id="590db-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="590db-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="590db-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="590db-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> mantenha arrastado os dados do item.</span><span class="sxs-lookup"><span data-stu-id="590db-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="590db-126">Erro</span><span class="sxs-lookup"><span data-stu-id="590db-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="590db-127">Evento</span><span class="sxs-lookup"><span data-stu-id="590db-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="590db-128">*Geral*</span><span class="sxs-lookup"><span data-stu-id="590db-128">*General*</span></span><br><span data-ttu-id="590db-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="590db-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="590db-130">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="590db-130">*Clipboard*</span></span><br><span data-ttu-id="590db-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="590db-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="590db-132">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="590db-132">*Input*</span></span><br><span data-ttu-id="590db-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="590db-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="590db-134">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="590db-134">*Media*</span></span><br><span data-ttu-id="590db-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="590db-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="590db-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>mantém atributos para configurar os mapeamentos entre os nomes de evento e os tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="590db-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="590db-137">Focus</span><span class="sxs-lookup"><span data-stu-id="590db-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="590db-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="590db-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="590db-139">Não inclui suporte para `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="590db-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="590db-140">Entrada</span><span class="sxs-lookup"><span data-stu-id="590db-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="590db-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="590db-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="590db-142">Teclado</span><span class="sxs-lookup"><span data-stu-id="590db-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="590db-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="590db-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="590db-144">Mouse</span><span class="sxs-lookup"><span data-stu-id="590db-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="590db-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="590db-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="590db-146">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="590db-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="590db-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="590db-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="590db-148">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="590db-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="590db-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="590db-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="590db-150">Andamento</span><span class="sxs-lookup"><span data-stu-id="590db-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="590db-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="590db-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="590db-152">Toque</span><span class="sxs-lookup"><span data-stu-id="590db-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="590db-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="590db-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="590db-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="590db-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="590db-155">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="590db-155">For more information, see the following resources:</span></span>

* <span data-ttu-id="590db-156">[Classes EventArgs no ASP.NET Core fonte de referência (dotNet/aspnetcore versão/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="590db-156">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="590db-157">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.</span><span class="sxs-lookup"><span data-stu-id="590db-157">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="590db-158">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="590db-158">Lambda expressions</span></span>

<span data-ttu-id="590db-159">As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="590db-159">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="590db-160">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="590db-160">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="590db-161">O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e seu número de botão ( `buttonNumber` ) quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="590db-161">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="590db-162">**Não** use uma variável de loop diretamente em uma expressão lambda, como `i` no exemplo de `for` loop anterior ou em uma variável de referência em um `foreach` loop.</span><span class="sxs-lookup"><span data-stu-id="590db-162">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="590db-163">Caso contrário, a mesma variável é usada por todas as expressões lambda, o que resulta em uso do mesmo valor em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="590db-163">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="590db-164">Sempre Capture o valor da variável em uma variável local e, em seguida, use-o.</span><span class="sxs-lookup"><span data-stu-id="590db-164">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="590db-165">No exemplo anterior, a variável de loop `i` é atribuída a `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="590db-165">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="590db-166">EventCallback</span><span class="sxs-lookup"><span data-stu-id="590db-166">EventCallback</span></span>

<span data-ttu-id="590db-167">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho.</span><span class="sxs-lookup"><span data-stu-id="590db-167">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="590db-168">Um `onclick` evento que ocorre no componente filho é um caso de uso comum.</span><span class="sxs-lookup"><span data-stu-id="590db-168">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="590db-169">Para expor eventos entre componentes, use um <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="590db-169">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="590db-170">Um componente pai pode atribuir um método de retorno de chamada a um componente filho <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="590db-170">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="590db-171">O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o manipulador de um botão `onclick` é configurado para receber um <xref:Microsoft.AspNetCore.Components.EventCallback> delegado de exemplo `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="590db-171">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="590db-172">O <xref:Microsoft.AspNetCore.Components.EventCallback> é digitado com `MouseEventArgs` , que é apropriado para um `onclick` evento de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="590db-172">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="590db-173">O `ParentComponent` define o filho <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) para seu `ShowMessage` método.</span><span class="sxs-lookup"><span data-stu-id="590db-173">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="590db-174">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="590db-174">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="590db-175">Quando o botão estiver selecionado no `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="590db-175">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="590db-176">O `ParentComponent` `ShowMessage` método é chamado.</span><span class="sxs-lookup"><span data-stu-id="590db-176">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="590db-177">`messageText`é atualizado e exibido no `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="590db-177">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="590db-178">Uma chamada para [StateHasChanged](xref:blazor/components/lifecycle#state-changes) não é necessária no método do retorno de chamada ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="590db-178">A call to [StateHasChanged](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="590db-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado automaticamente para renderizar novamente o `ParentComponent` , assim como eventos filho, rerenderização de componente em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="590db-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="590db-180"><xref:Microsoft.AspNetCore.Components.EventCallback>e <xref:Microsoft.AspNetCore.Components.EventCallback%601> permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="590db-180"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="590db-181"><xref:Microsoft.AspNetCore.Components.EventCallback%601>é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="590db-181"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="590db-182"><xref:Microsoft.AspNetCore.Components.EventCallback>está com tipo fraco e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="590db-182"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="590db-183">Invocar um <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> com <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e aguardar <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="590db-183">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="590db-184">Use <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="590db-184">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="590db-185">Prefira o tipo fortemente tipado <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="590db-185">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="590db-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="590db-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="590db-187">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="590db-187">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="590db-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="590db-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="590db-189">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="590db-189">Prevent default actions</span></span>

<span data-ttu-id="590db-190">Use o [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributo diretiva para impedir a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="590db-190">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="590db-191">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="590db-191">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="590db-192">No exemplo a seguir, o comportamento padrão é impedido pela especificação do `@onkeypress:preventDefault` atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="590db-192">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="590db-193">O contador é incrementado e a **+** chave não é capturada no `<input>` valor do elemento:</span><span class="sxs-lookup"><span data-stu-id="590db-193">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="590db-194">Especificar o `@on{EVENT}:preventDefault` atributo sem um valor é equivalente a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="590db-194">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="590db-195">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="590db-195">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="590db-196">No exemplo a seguir, `shouldPreventDefault` é um `bool` conjunto de campos como `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="590db-196">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="590db-197">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="590db-197">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="590db-198">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="590db-198">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="590db-199">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="590db-199">Stop event propagation</span></span>

<span data-ttu-id="590db-200">Use o [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributo diretiva para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="590db-200">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="590db-201">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` se propaguem para o pai `<div>` :</span><span class="sxs-lookup"><span data-stu-id="590db-201">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
