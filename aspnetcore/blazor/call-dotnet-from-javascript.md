---
title: Chamar métodos .NET de funções JavaScript no ASP.NET CoreBlazor
author: guardrex
description: Saiba como invocar métodos .NET de funções JavaScript em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 1bc75f0825b114a24def287bb7ccb11c27514f01
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767181"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a>Chamar métodos .NET de funções JavaScript no ASP.NET CoreBlazor

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript. Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).

Este artigo aborda a invocação de métodos .NET do JavaScript. Para obter informações sobre como chamar funções JavaScript do .NET, consulte <xref:blazor/call-javascript-from-dotnet>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Chamada de método .NET estático

Para invocar um método .NET estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou. Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos. A versão assíncrona é preferida para Blazor dar suporte a cenários de servidor. O método .NET deve ser público, estático e ter o `[JSInvokable]` atributo. Atualmente, não há suporte para chamar métodos genéricos abertos.

O aplicativo de exemplo inclui um método C# para retornar `int` uma matriz. O `JSInvokable` atributo é aplicado ao método.

*Páginas/JsInterop. Razor*:

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

O JavaScript servido para o cliente invoca o método .NET do C#.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quando o botão **disparar o método estático do .net ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.

A saída do console é:

```console
Array(4) [ 1, 2, 3, 4 ]
```

O quarto valor de matriz é enviado por push para`data.push(4);`a matriz ( `ReturnArrayAsync`) retornada por.

Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente `JSInvokableAttribute` usando o construtor:

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

Você também pode chamar os métodos de instância do .NET do JavaScript. Para invocar um método de instância .NET do JavaScript:

* Passe a instância do .NET por referência ao JavaScript:
  * Faça uma chamada estática para `DotNetObjectReference.Create`.
  * Empacote a instância em uma `DotNetObjectReference` instância e chame `Create` na `DotNetObjectReference` instância. Descarte de `DotNetObjectReference` objetos (um exemplo aparece mais adiante nesta seção).
* Invoque os métodos de instância do .NET na instância `invokeMethod` usando `invokeMethodAsync` as funções ou. A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.

> [!NOTE]
> O aplicativo de exemplo registra as mensagens no console do lado do cliente. Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.

Quando o botão do **método de instância .net de gatilho HelloHelper. sayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa `Blazor`um nome para o método.

*Páginas/JsInterop. Razor*:

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

`CallHelloHelperSayHello`Invoca a função `sayHello` JavaScript com uma nova instância do `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

O nome é passado para `HelloHelper`o construtor de, que define `HelloHelper.Name` a propriedade. Quando a função `sayHello` JavaScript é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.

*JsInteropClasses/HelloHelper. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Saída do console nas ferramentas de desenvolvedor da Web do navegador:

```console
Hello, Blazor!
```

Para evitar um vazamento de memória e permitir a coleta de lixo em um componente `DotNetObjectReference`que cria um, adote uma das seguintes abordagens:

* Descarte o objeto na classe que criou a `DotNetObjectReference` instância:

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

  O padrão anterior mostrado na `ExampleJsInterop` classe também pode ser implementado em um componente:

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

* Quando o componente ou classe não descartar `DotNetObjectReference`o, descarte o objeto no cliente chamando `.dispose()`:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Chamada de método de instância de componente

Para invocar os métodos .NET de um componente:

* Use a `invokeMethod` função `invokeMethodAsync` ou para fazer uma chamada de método estático para o componente.
* O método estático do componente encapsula a chamada para seu método de instância como uma chamada `Action`.

No JavaScript do lado do cliente:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Páginas/JSInteropComponent. Razor*:

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

Quando há vários componentes, cada um com métodos de instância para chamar, use uma classe auxiliar para invocar os métodos de `Action`instância (como s) de cada componente.

No exemplo a seguir:

* O `JSInterop` componente contém vários `ListItem` componentes.
* Cada `ListItem` componente é composto por uma mensagem e um botão.
* Quando um `ListItem` botão de componente é selecionado, `ListItem`esse `UpdateMessage` método altera o texto do item da lista e oculta o botão.

*MessageUpdateInvokeHelper.cs*:

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

*Compartilhado/ListItem. Razor*:

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

*Páginas/JSInterop. Razor*:

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

## <a name="avoid-circular-object-references"></a>Evitar referências circulares de objeto

Os objetos que contêm referências circulares não podem ser serializados no cliente para:

* Chamadas de método .NET.
* O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.

Para obter mais informações, consulte os seguintes problemas:

* [Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/call-javascript-from-dotnet>
* [Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Executar grandes transferências de dados Blazor em aplicativos de servidor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
