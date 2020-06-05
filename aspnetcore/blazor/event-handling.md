---
<span data-ttu-id="2f603-101">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: guardrex Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="2f603-101">title: 'ASP.NET Core Blazor event handling' author: guardrex description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="2f603-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 06/04/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="2f603-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 06/04/2020 no-loc:</span></span>
- <span data-ttu-id="2f603-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2f603-103">'Blazor'</span></span>
- <span data-ttu-id="2f603-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2f603-104">'Identity'</span></span>
- <span data-ttu-id="2f603-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2f603-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2f603-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2f603-106">'Razor'</span></span>
- <span data-ttu-id="2f603-107">SignalRUID: mais incrivelmente/tratamento de eventos</span><span class="sxs-lookup"><span data-stu-id="2f603-107">'SignalR' uid: blazor/event-handling</span></span>

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="2f603-108">Manipulação de eventos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="2f603-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="2f603-109">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2f603-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="2f603-110">os componentes fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="2f603-110"> components provide event handling features.</span></span> <span data-ttu-id="2f603-111">Para um atributo de elemento HTML chamado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por exemplo, `@onclick` ) com um valor de tipo delegado, um Razor componente trata o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="2f603-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="2f603-112">O código a seguir chama o `UpdateHeading` método quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="2f603-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="2f603-113">O código a seguir chama o `CheckChanged` método quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="2f603-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="2f603-114">Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="2f603-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="2f603-115">Não é necessário chamar [StateHasChanged](xref:blazor/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="2f603-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="2f603-116">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="2f603-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="2f603-117">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="2f603-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="2f603-118">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="2f603-118">Event argument types</span></span>

<span data-ttu-id="2f603-119">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="2f603-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="2f603-120">A especificação de um tipo de evento na chamada do método só será necessária se o tipo de evento for usado no método.</span><span class="sxs-lookup"><span data-stu-id="2f603-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="2f603-121">Com suporte, <xref:System.EventArgs> são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2f603-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="2f603-122">Evento</span><span class="sxs-lookup"><span data-stu-id="2f603-122">Event</span></span>            | <span data-ttu-id="2f603-123">Class</span><span class="sxs-lookup"><span data-stu-id="2f603-123">Class</span></span>                | <span data-ttu-id="2f603-124">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="2f603-124">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="2f603-125">Área de Transferência</span><span class="sxs-lookup"><span data-stu-id="2f603-125">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="2f603-126">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="2f603-126">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="2f603-127">Arrastar</span><span class="sxs-lookup"><span data-stu-id="2f603-127">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="2f603-128">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="2f603-128">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="2f603-129"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> mantenha arrastado os dados do item.</span><span class="sxs-lookup"><span data-stu-id="2f603-129"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="2f603-130">Erro</span><span class="sxs-lookup"><span data-stu-id="2f603-130">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="2f603-131">Evento</span><span class="sxs-lookup"><span data-stu-id="2f603-131">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="2f603-132">*Geral*</span><span class="sxs-lookup"><span data-stu-id="2f603-132">*General*</span></span><br><span data-ttu-id="2f603-133">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="2f603-133">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="2f603-134">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="2f603-134">*Clipboard*</span></span><br><span data-ttu-id="2f603-135">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="2f603-135">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="2f603-136">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="2f603-136">*Input*</span></span><br><span data-ttu-id="2f603-137">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="2f603-137">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="2f603-138">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="2f603-138">*Media*</span></span><br><span data-ttu-id="2f603-139">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="2f603-139">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="2f603-140"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>mantém atributos para configurar os mapeamentos entre os nomes de evento e os tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="2f603-140"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="2f603-141">Focus</span><span class="sxs-lookup"><span data-stu-id="2f603-141">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="2f603-142">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="2f603-142">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="2f603-143">Não inclui suporte para `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="2f603-143">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="2f603-144">Entrada</span><span class="sxs-lookup"><span data-stu-id="2f603-144">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="2f603-145">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="2f603-145">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="2f603-146">Teclado</span><span class="sxs-lookup"><span data-stu-id="2f603-146">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="2f603-147">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="2f603-147">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="2f603-148">Mouse</span><span class="sxs-lookup"><span data-stu-id="2f603-148">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="2f603-149">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="2f603-149">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="2f603-150">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="2f603-150">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="2f603-151">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="2f603-151">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="2f603-152">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="2f603-152">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="2f603-153">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="2f603-153">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="2f603-154">Progresso</span><span class="sxs-lookup"><span data-stu-id="2f603-154">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="2f603-155">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="2f603-155">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="2f603-156">Toque</span><span class="sxs-lookup"><span data-stu-id="2f603-156">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="2f603-157">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="2f603-157">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="2f603-158"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="2f603-158"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="2f603-159">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="2f603-159">For more information, see the following resources:</span></span>

* <span data-ttu-id="2f603-160">[Classes EventArgs no ASP.NET Core fonte de referência (dotNet/aspnetcore versão/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="2f603-160">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="2f603-161">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.</span><span class="sxs-lookup"><span data-stu-id="2f603-161">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="2f603-162">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="2f603-162">Lambda expressions</span></span>

<span data-ttu-id="2f603-163">As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="2f603-163">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="2f603-164">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="2f603-164">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="2f603-165">O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e seu número de botão ( `buttonNumber` ) quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="2f603-165">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="2f603-166">**Não** use uma variável de loop diretamente em uma expressão lambda, como `i` no exemplo de `for` loop anterior ou em uma variável de referência em um `foreach` loop.</span><span class="sxs-lookup"><span data-stu-id="2f603-166">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="2f603-167">Caso contrário, a mesma variável é usada por todas as expressões lambda, o que resulta em uso do mesmo valor em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="2f603-167">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="2f603-168">Sempre Capture o valor da variável em uma variável local e, em seguida, use-o.</span><span class="sxs-lookup"><span data-stu-id="2f603-168">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="2f603-169">No exemplo anterior, a variável de loop `i` é atribuída a `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="2f603-169">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="2f603-170">EventCallback</span><span class="sxs-lookup"><span data-stu-id="2f603-170">EventCallback</span></span>

<span data-ttu-id="2f603-171">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho.</span><span class="sxs-lookup"><span data-stu-id="2f603-171">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="2f603-172">Um `onclick` evento que ocorre no componente filho é um caso de uso comum.</span><span class="sxs-lookup"><span data-stu-id="2f603-172">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="2f603-173">Para expor eventos entre componentes, use um <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="2f603-173">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="2f603-174">Um componente pai pode atribuir um método de retorno de chamada a um componente filho <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="2f603-174">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="2f603-175">O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o manipulador de um botão `onclick` é configurado para receber um <xref:Microsoft.AspNetCore.Components.EventCallback> delegado de exemplo `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="2f603-175">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="2f603-176">O <xref:Microsoft.AspNetCore.Components.EventCallback> é digitado com `MouseEventArgs` , que é apropriado para um `onclick` evento de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="2f603-176">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="2f603-177">O `ParentComponent` define o filho <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) para seu `ShowMessage` método.</span><span class="sxs-lookup"><span data-stu-id="2f603-177">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="2f603-178">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="2f603-178">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="2f603-179">Quando o botão estiver selecionado no `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="2f603-179">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="2f603-180">O `ParentComponent` `ShowMessage` método é chamado.</span><span class="sxs-lookup"><span data-stu-id="2f603-180">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="2f603-181">`messageText`é atualizado e exibido no `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="2f603-181">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="2f603-182">Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no método do retorno de chamada ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="2f603-182">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="2f603-183"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado automaticamente para renderizar novamente o `ParentComponent` , assim como eventos filho, rerenderização de componente em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="2f603-183"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="2f603-184"><xref:Microsoft.AspNetCore.Components.EventCallback>e <xref:Microsoft.AspNetCore.Components.EventCallback%601> permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="2f603-184"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="2f603-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601>é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="2f603-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="2f603-186"><xref:Microsoft.AspNetCore.Components.EventCallback>está com tipo fraco e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="2f603-186"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="2f603-187">Invocar um <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> com <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e aguardar <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="2f603-187">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="2f603-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="2f603-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="2f603-189">Prefira o tipo fortemente tipado <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="2f603-189">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="2f603-190"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="2f603-190"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="2f603-191">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="2f603-191">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="2f603-192">Use <xref:Microsoft.AspNetCore.Components.EventCallback> quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="2f603-192">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="2f603-193">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="2f603-193">Prevent default actions</span></span>

<span data-ttu-id="2f603-194">Use o [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributo diretiva para impedir a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="2f603-194">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="2f603-195">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="2f603-195">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="2f603-196">No exemplo a seguir, o comportamento padrão é impedido pela especificação do `@onkeypress:preventDefault` atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="2f603-196">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="2f603-197">O contador é incrementado e a **+** chave não é capturada no `<input>` valor do elemento:</span><span class="sxs-lookup"><span data-stu-id="2f603-197">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="2f603-198">Especificar o `@on{EVENT}:preventDefault` atributo sem um valor é equivalente a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="2f603-198">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="2f603-199">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="2f603-199">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="2f603-200">No exemplo a seguir, `shouldPreventDefault` é um `bool` conjunto de campos como `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="2f603-200">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="2f603-201">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="2f603-201">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="2f603-202">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="2f603-202">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="2f603-203">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="2f603-203">Stop event propagation</span></span>

<span data-ttu-id="2f603-204">Use o [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributo diretiva para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="2f603-204">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="2f603-205">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` se propaguem para o pai `<div>` :</span><span class="sxs-lookup"><span data-stu-id="2f603-205">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
