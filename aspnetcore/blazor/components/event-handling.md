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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 2fce394202be5df9af67e8afca27a0914f410402
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399030"
---
# <a name="aspnet-core-blazor-event-handling"></a>Manipulação de eventos de ASP.NET Core Blazor

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razoros componentes fornecem recursos de manipulação de eventos. Para um atributo de elemento HTML chamado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por exemplo, `@onclick` ) com um valor de tipo delegado, um Razor componente trata o valor do atributo como um manipulador de eventos.

O código a seguir chama o `UpdateHeading` método quando o botão é selecionado na interface do usuário:

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

O código a seguir chama o `CheckChanged` método quando a caixa de seleção é alterada na interface do usuário:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task> . Não é necessário chamar [StateHasChanged](xref:blazor/components/lifecycle#state-changes)manualmente. As exceções são registradas quando ocorrem.

No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:

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

## <a name="event-argument-types"></a>Tipos de argumento de evento

Para alguns eventos, são permitidos tipos de argumento de evento. A especificação de um parâmetro de evento em uma definição de método de evento é opcional e será necessária somente se o tipo de evento for usado no método. No exemplo a seguir, o `MouseEventArgs` argumento de evento é usado no `ShowMessage` método para definir o texto da mensagem:

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

Com suporte, <xref:System.EventArgs> são mostrados na tabela a seguir.

| Evento            | Classe                | Eventos e observações do DOM |
| ---------------- | -------------------- | -------------------- |
| Área de Transferência        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Arrastar             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> mantenha arrastado os dados do item. |
| Erro            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| Evento            | <xref:System.EventArgs> | *Geral*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Área de transferência*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Entrada*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit><br><br>*Mídia*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>mantém atributos para configurar os mapeamentos entre os nomes de evento e os tipos de argumento de evento. |
| Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Não inclui suporte para `relatedTarget` . |
| Entrada            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Teclado         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Ponteiro do mouse    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Botão de rolagem do mouse      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Progresso         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toque            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>representa um único ponto de contato em um dispositivo sensível ao toque. |

Para saber mais, consulte os recursos a seguir:

* [ `EventArgs` classes na fonte de referência de ASP.NET Core (aspnetcore dotnet/versão/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.

## <a name="lambda-expressions"></a>Expressões lambda

As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos. O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e seu número de botão ( `buttonNumber` ) quando selecionado na interface do usuário:

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
> **Não** use uma variável de loop diretamente em uma expressão lambda, como `i` no exemplo de `for` loop anterior. Caso contrário, a mesma variável é usada por todas as expressões lambda, o que resulta em uso do mesmo valor em todos os lambdas. Sempre Capture o valor da variável em uma variável local e, em seguida, use-o. No exemplo anterior, a variável de loop `i` é atribuída a `buttonNumber` .

## <a name="eventcallback"></a>EventCallback

Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho. Um `onclick` evento que ocorre no componente filho é um caso de uso comum. Para expor eventos entre componentes, use um <xref:Microsoft.AspNetCore.Components.EventCallback> . Um componente pai pode atribuir um método de retorno de chamada a um componente filho <xref:Microsoft.AspNetCore.Components.EventCallback> .

O `ChildComponent` no aplicativo de exemplo ( `Components/ChildComponent.razor` ) demonstra como o manipulador de um botão `onclick` é configurado para receber um <xref:Microsoft.AspNetCore.Components.EventCallback> delegado de exemplo `ParentComponent` . O <xref:Microsoft.AspNetCore.Components.EventCallback> é digitado com `MouseEventArgs` , que é apropriado para um `onclick` evento de um dispositivo periférico:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

O `ParentComponent` define o filho <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) para seu `ShowMessage` método.

`Pages/ParentComponent.razor`:

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

Quando o botão estiver selecionado no `ChildComponent` :

* O `ParentComponent` `ShowMessage` método é chamado. `messageText`é atualizado e exibido no `ParentComponent` .
* Uma chamada para [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) não é necessária no método do retorno de chamada ( `ShowMessage` ). <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado automaticamente para renderizar novamente o `ParentComponent` , assim como eventos filho, rerenderização de componente em manipuladores de eventos que são executados dentro do filho.

<xref:Microsoft.AspNetCore.Components.EventCallback>e <xref:Microsoft.AspNetCore.Components.EventCallback%601> permitir delegados assíncronos. <xref:Microsoft.AspNetCore.Components.EventCallback%601>é fortemente tipado e requer um tipo de argumento específico. <xref:Microsoft.AspNetCore.Components.EventCallback>está com tipo fraco e permite qualquer tipo de argumento.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Invocar um <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> com <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e aguardar <xref:System.Threading.Tasks.Task> :

```csharp
await OnClickCallback.InvokeAsync(arg);
```

Use <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> para manipulação de eventos e parâmetros de componente de associação.

Prefira o tipo fortemente tipado <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> . <xref:Microsoft.AspNetCore.Components.EventCallback%601>fornece melhores comentários de erro para os usuários do componente. Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional. Use <xref:Microsoft.AspNetCore.Components.EventCallback> quando não houver valor passado para o retorno de chamada.

## <a name="prevent-default-actions"></a>Impedir ações padrão

Use o [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributo diretiva para impedir a ação padrão para um evento.

Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto. No exemplo a seguir, o comportamento padrão é impedido pela especificação do `@onkeypress:preventDefault` atributo de diretiva. O contador é incrementado e a **+** chave não é capturada no `<input>` valor do elemento:

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

Especificar o `@on{EVENT}:preventDefault` atributo sem um valor é equivalente a `@on{EVENT}:preventDefault="true"` .

O valor do atributo também pode ser uma expressão. No exemplo a seguir, `shouldPreventDefault` é um `bool` conjunto de campos como `true` ou `false` :

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

Um manipulador de eventos não é necessário para impedir a ação padrão. O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.

## <a name="stop-event-propagation"></a>Parar a propagação do evento

Use o [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributo diretiva para parar a propagação do evento.

No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` se propaguem para o pai `<div>` :

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
