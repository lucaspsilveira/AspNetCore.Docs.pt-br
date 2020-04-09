---
title: Chamar métodos .NET a partir de funções JavaScript no ASP.NET CoreBlazor
author: guardrex
description: Aprenda a invocar métodos .NET a Blazor partir de funções JavaScript em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e2344dd15efd243a405373b6cf0362f28b48173a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976944"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>Chamar métodos .NET a partir de funções JavaScript no ASP.NET CoreBlazor

Por [Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Um Blazor aplicativo pode invocar funções JavaScript de métodos .NET e métodos .NET a partir de funções JavaScript. Esses cenários são chamados *de interoperabilidade JavaScript* *(Interop JS).*

Este artigo abrange a invocação de métodos .NET do JavaScript. Para obter informações sobre como chamar funções <xref:blazor/call-javascript-from-dotnet>JavaScript de .NET, consulte .

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Chamada de método static .NET

Para invocar um método static .NET `DotNet.invokeMethod` do `DotNet.invokeMethodAsync` JavaScript, use as funções ou. Passe no identificador do método estático que deseja chamar, o nome do conjunto contendo a função e quaisquer argumentos. A versão assíncrona Blazor é preferível para suportar cenários do Servidor. O método .NET deve ser público, `[JSInvokable]` estático e ter o atributo. Chamar métodos genéricos abertos não é suportado no momento.

O aplicativo de exemplo inclui um `int` método C# para retornar uma matriz. O `JSInvokable` atributo é aplicado ao método.

*Páginas/JsInterop.razor:*

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

JavaScript servido ao cliente invoca o método C# .NET.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quando o **método estático Trigger .NET ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor web do navegador.

A saída do console é:

```console
Array(4) [ 1, 2, 3, 4 ]
```

O valor da quarta matriz é`data.push(4);`empurrado para `ReturnArrayAsync`a matriz () devolvido por .

Por padrão, o identificador do método é o nome do `JSInvokableAttribute` método, mas você pode especificar um identificador diferente usando o construtor:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

No arquivo JavaScript do lado do cliente:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Chamada de método de instância

Você também pode chamar métodos de ocorrência .NET do JavaScript. Para invocar um método de instância .NET do JavaScript:

* Passe a instância .NET por referência a JavaScript:
  * Faça uma chamada `DotNetObjectReference.Create`estática para .
  * Enrole a `DotNetObjectReference` instância em `Create` uma `DotNetObjectReference` instância e acessa a instância. Descarte `DotNetObjectReference` objetos (um exemplo aparece mais tarde nesta seção).
* Invoque métodos de ocorrência `invokeMethod` .NET na instância usando as funções ou. `invokeMethodAsync` A instância .NET também pode ser aprovada como um argumento ao invocar outros métodos .NET do JavaScript.

> [!NOTE]
> O aplicativo de exemplo registra mensagens no console do lado do cliente. Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.

Quando o **método de ocorrência Trigger .NET HelloHelper.SayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um `Blazor`nome, para o método.

*Páginas/JsInterop.razor:*

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello`invoca a função `sayHello` JavaScript com `HelloHelper`uma nova instância de .

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

O nome é `HelloHelper`passado para construtor, `HelloHelper.Name` que define a propriedade. Quando a função `sayHello` JavaScript `HelloHelper.SayHello` é `Hello, {Name}!` executada, retorna a mensagem, que é escrita no console pela função JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Saída do console nas ferramentas de desenvolvedor web do navegador:

```console
Hello, Blazor!
```

Para evitar um vazamento de memória e permitir `DotNetObjectReference`a coleta de lixo em um componente que crie uma, adote uma das seguintes abordagens:

* Descarte o objeto na classe que `DotNetObjectReference` criou a instância:

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime _jsRuntime;
      private DotNetObjectReference<HelloHelper> _objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return _jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

  O padrão anterior `ExampleJsInterop` mostrado na classe também pode ser implementado em um componente:

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> _objRef;

      public async Task TriggerNetInstanceMethod()
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

* Quando o componente ou classe não `DotNetObjectReference`descartar o , descarte o `.dispose()`objeto no cliente ligando:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Chamada do método de instância do componente

Para invocar os métodos .NET de um componente:

* Use `invokeMethod` a `invokeMethodAsync` função ou para fazer uma chamada de método estático para o componente.
* O método estático do componente envolve a chamada ao `Action`seu método de ocorrência como um invocado .

No JavaScript do lado do cliente:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Páginas/JSInteropComponent.razor:*

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

Quando houver vários componentes, cada um com métodos de ocorrência para chamar, `Action`use uma classe auxiliar para invocar os métodos de ocorrência (as s) de cada componente.

No exemplo a seguir:

* O `JSInterop` componente `ListItem` contém vários componentes.
* Cada `ListItem` componente é composto por uma mensagem e um botão.
* Quando `ListItem` um botão de `ListItem`componente `UpdateMessage` é selecionado, esse método altera o texto do item da lista e oculta o botão.

*MessageUpdateInvokeHelper.cs:*

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

No JavaScript do lado do cliente:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Compartilhado/ListItem.razor:*

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

*Páginas/JSInterop.razor:*

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evite referências de objetos circulares

Objetos que contêm referências circulares não podem ser serializados no cliente para:

* Chamadas do método .NET.
* O método JavaScript chama a partir de C# quando o tipo de retorno tem referências circulares.

Para obter mais informações, consulte os seguintes problemas:

* [As referências circulares não são suportadas, tome duas (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/call-javascript-from-dotnet>
* [Exemplo InteropComponent.razor (repositório dotnet/AspNetCore GitHub, ramo de versão 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Realizar grandes transferências Blazor de dados em aplicativos do Servidor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
