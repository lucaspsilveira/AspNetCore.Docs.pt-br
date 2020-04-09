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
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)

Os componentes da navalha fornecem recursos de manuseio de eventos. Para um atributo [`@on{EVENT}`](xref:mvc/views/razor#onevent) de elemento `@onclick`HTML nomeado (por exemplo) com um valor digitado pelo delegado, um componente Razor trata o valor do atributo como um manipulador de eventos.

O código a `UpdateHeading` seguir chama o método quando o botão é selecionado na ui:

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

O código a `CheckChanged` seguir chama o método quando a caixa de seleção é alterada na ui:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Os manipuladores de eventos também podem <xref:System.Threading.Tasks.Task>ser assíncronos e retornar um . Não há necessidade de chamar manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes). As exceções são registradas quando ocorrem.

No exemplo a `UpdateHeading` seguir, é chamado assíncronamente quando o botão é selecionado:

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

Para alguns eventos, tipos de argumento de eventos são permitidos. Especificar um tipo de evento na chamada do método só é necessário se o tipo de evento for usado no método.

Os `EventArgs` suportes são mostrados na tabela a seguir.

| Evento            | Classe                | Eventos e notas do DOM |
| ---------------- | -------------------- | -------------------- |
| Área de transferência        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Arrastar             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`e `DataTransferItem` manter dados de itens arrastados. |
| Erro            | `ErrorEventArgs`     | `onerror` |
| Evento            | `EventArgs`          | *Geral*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Área de transferência*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Entrada*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Mídia*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Não inclui suporte `relatedTarget`para . |
| Entrada            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Teclado         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Ponteiro do mouse    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Botão de rolagem do mouse      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Andamento         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toque            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`representa um único ponto de contato em um dispositivo sensível ao toque. |

Para saber mais, consulte os recursos a seguir:

* [Classes EventArgs na fonte de referência ASP.NET Core (versão dotnet/aspnetcore/ramo 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Inclui informações sobre quais elementos HTML suportam cada evento DOM.

## <a name="lambda-expressions"></a>Expressões lambda

[Expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Muitas vezes é conveniente fechar sobre valores adicionais, como quando iterasobre um conjunto de elementos. O exemplo a seguir cria três `UpdateHeading` botões, cada`MouseEventArgs`um dos quais`buttonNumber`chama passando um argumento de evento ( ) e seu número de botão ( ) quando selecionado na ui:

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
> **Não** use a variável`i`loop `for` ( ) em um loop diretamente em uma expressão lambda. Caso contrário, a mesma variável é usada `i`por todas as expressões lambda fazendo com que o valor seja o mesmo em todas as lambdas. Sempre capture seu valor em`buttonNumber` uma variável local (no exemplo anterior) e, em seguida, use-o.

## <a name="eventcallback"></a>EventCallback

Um cenário comum com componentes aninhados é o desejo de&mdash;executar o método `onclick` de um componente pai quando um evento componente filho ocorre, por exemplo, quando um evento ocorre na criança. Para expor eventos entre componentes, use um `EventCallback`. Um componente pai pode atribuir um método de `EventCallback`retorno de chamada a um componente filho .

O `ChildComponent` aplicativo na amostra *(Components/ChildComponent.razor)* demonstra `onclick` como o manipulador de `EventCallback` um botão é `ParentComponent`configurado para receber um delegado da amostra . O `EventCallback` é digitado com `MouseEventArgs`, `onclick` o que é apropriado para um evento a partir de um dispositivo periférico:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

O `ParentComponent` conjunto da `EventCallback<T>` criança`OnClickCallback`( `ShowMessage` ) ao seu método.

*Páginas/ParentComponent.razor:*

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

Quando o botão estiver `ChildComponent`selecionado no:

* O `ParentComponent`método `ShowMessage` é chamado. `_messageText`é atualizado e exibido `ParentComponent`no .
* Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no`ShowMessage`método de retorno de chamada (). `StateHasChanged`é chamado automaticamente para `ParentComponent`rerenderizar o , assim como eventos crianças acionam a rerenderização do componente em manipuladores de eventos que executam dentro da criança.

`EventCallback`e `EventCallback<T>` permitir delegados assíncronos. `EventCallback<T>`é fortemente digitado e requer um tipo de argumento específico. `EventCallback`é fracamente digitado e permite qualquer tipo de argumento.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Invoque `EventCallback<T>` `InvokeAsync` um `EventCallback` ou <xref:System.Threading.Tasks.Task>com e aguarde o:

```csharp
await callback.InvokeAsync(arg);
```

Use `EventCallback` `EventCallback<T>` e para os parâmetros dos componentes de manuseio e vinculação de eventos.

Prefira o `EventCallback<T>` fortemente digitado sobre `EventCallback`. `EventCallback<T>`fornece melhor feedback de erro para os usuários do componente. Semelhante a outros manipuladores de eventos de ui, especificando que o parâmetro de evento é opcional. Use `EventCallback` quando não houver valor passado para o retorno de chamada.

## <a name="prevent-default-actions"></a>Impedir ações padrão

Use [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) o atributo diretiva para impedir a ação padrão de um evento.

Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto. No exemplo a seguir, o comportamento padrão `@onkeypress:preventDefault` é impedido por especificar o atributo diretiva. O contador incrementa, **+** e a chave não `<input>` é capturada no valor do elemento:

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

Especificar `@on{EVENT}:preventDefault` o atributo sem `@on{EVENT}:preventDefault="true"`um valor é equivalente a .

O valor do atributo também pode ser uma expressão. No exemplo a `_shouldPreventDefault` seguir, é `bool` um `false`campo definido para ou: `true`

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Um manipulador de eventos não é necessário para impedir a ação padrão. O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.

## <a name="stop-event-propagation"></a>Parar a propagação do evento

Use [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) o atributo diretiva para interromper a propagação do evento.

No exemplo a seguir, selecionar a caixa de seleção impede que eventos de clique da segunda criança `<div>` se prossigam para o pai `<div>`:

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
