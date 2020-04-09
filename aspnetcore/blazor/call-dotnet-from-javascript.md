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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="b6f00-103">Chamar métodos .NET a partir de funções JavaScript no ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="b6f00-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="b6f00-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b6f00-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b6f00-105">Um Blazor aplicativo pode invocar funções JavaScript de métodos .NET e métodos .NET a partir de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b6f00-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="b6f00-106">Esses cenários são chamados *de interoperabilidade JavaScript* *(Interop JS).*</span><span class="sxs-lookup"><span data-stu-id="b6f00-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="b6f00-107">Este artigo abrange a invocação de métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b6f00-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="b6f00-108">Para obter informações sobre como chamar funções <xref:blazor/call-javascript-from-dotnet>JavaScript de .NET, consulte .</span><span class="sxs-lookup"><span data-stu-id="b6f00-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="b6f00-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b6f00-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="b6f00-110">Chamada de método static .NET</span><span class="sxs-lookup"><span data-stu-id="b6f00-110">Static .NET method call</span></span>

<span data-ttu-id="b6f00-111">Para invocar um método static .NET `DotNet.invokeMethod` do `DotNet.invokeMethodAsync` JavaScript, use as funções ou.</span><span class="sxs-lookup"><span data-stu-id="b6f00-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="b6f00-112">Passe no identificador do método estático que deseja chamar, o nome do conjunto contendo a função e quaisquer argumentos.</span><span class="sxs-lookup"><span data-stu-id="b6f00-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="b6f00-113">A versão assíncrona Blazor é preferível para suportar cenários do Servidor.</span><span class="sxs-lookup"><span data-stu-id="b6f00-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="b6f00-114">O método .NET deve ser público, `[JSInvokable]` estático e ter o atributo.</span><span class="sxs-lookup"><span data-stu-id="b6f00-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="b6f00-115">Chamar métodos genéricos abertos não é suportado no momento.</span><span class="sxs-lookup"><span data-stu-id="b6f00-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="b6f00-116">O aplicativo de exemplo inclui um `int` método C# para retornar uma matriz.</span><span class="sxs-lookup"><span data-stu-id="b6f00-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="b6f00-117">O `JSInvokable` atributo é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="b6f00-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="b6f00-118">*Páginas/JsInterop.razor:*</span><span class="sxs-lookup"><span data-stu-id="b6f00-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="b6f00-119">JavaScript servido ao cliente invoca o método C# .NET.</span><span class="sxs-lookup"><span data-stu-id="b6f00-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="b6f00-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="b6f00-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="b6f00-121">Quando o **método estático Trigger .NET ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor web do navegador.</span><span class="sxs-lookup"><span data-stu-id="b6f00-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="b6f00-122">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="b6f00-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="b6f00-123">O valor da quarta matriz é`data.push(4);`empurrado para `ReturnArrayAsync`a matriz () devolvido por .</span><span class="sxs-lookup"><span data-stu-id="b6f00-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="b6f00-124">Por padrão, o identificador do método é o nome do `JSInvokableAttribute` método, mas você pode especificar um identificador diferente usando o construtor:</span><span class="sxs-lookup"><span data-stu-id="b6f00-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="b6f00-125">No arquivo JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="b6f00-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="b6f00-126">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="b6f00-126">Instance method call</span></span>

<span data-ttu-id="b6f00-127">Você também pode chamar métodos de ocorrência .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b6f00-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="b6f00-128">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b6f00-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="b6f00-129">Passe a instância .NET por referência a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b6f00-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="b6f00-130">Faça uma chamada `DotNetObjectReference.Create`estática para .</span><span class="sxs-lookup"><span data-stu-id="b6f00-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="b6f00-131">Enrole a `DotNetObjectReference` instância em `Create` uma `DotNetObjectReference` instância e acessa a instância.</span><span class="sxs-lookup"><span data-stu-id="b6f00-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="b6f00-132">Descarte `DotNetObjectReference` objetos (um exemplo aparece mais tarde nesta seção).</span><span class="sxs-lookup"><span data-stu-id="b6f00-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="b6f00-133">Invoque métodos de ocorrência `invokeMethod` .NET na instância usando as funções ou. `invokeMethodAsync`</span><span class="sxs-lookup"><span data-stu-id="b6f00-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="b6f00-134">A instância .NET também pode ser aprovada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b6f00-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="b6f00-135">O aplicativo de exemplo registra mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b6f00-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="b6f00-136">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="b6f00-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="b6f00-137">Quando o **método de ocorrência Trigger .NET HelloHelper.SayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um `Blazor`nome, para o método.</span><span class="sxs-lookup"><span data-stu-id="b6f00-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="b6f00-138">*Páginas/JsInterop.razor:*</span><span class="sxs-lookup"><span data-stu-id="b6f00-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="b6f00-139">`CallHelloHelperSayHello`invoca a função `sayHello` JavaScript com `HelloHelper`uma nova instância de .</span><span class="sxs-lookup"><span data-stu-id="b6f00-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="b6f00-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="b6f00-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="b6f00-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="b6f00-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="b6f00-142">O nome é `HelloHelper`passado para construtor, `HelloHelper.Name` que define a propriedade.</span><span class="sxs-lookup"><span data-stu-id="b6f00-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="b6f00-143">Quando a função `sayHello` JavaScript `HelloHelper.SayHello` é `Hello, {Name}!` executada, retorna a mensagem, que é escrita no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b6f00-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="b6f00-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="b6f00-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="b6f00-145">Saída do console nas ferramentas de desenvolvedor web do navegador:</span><span class="sxs-lookup"><span data-stu-id="b6f00-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="b6f00-146">Para evitar um vazamento de memória e permitir `DotNetObjectReference`a coleta de lixo em um componente que crie uma, adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="b6f00-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="b6f00-147">Descarte o objeto na classe que `DotNetObjectReference` criou a instância:</span><span class="sxs-lookup"><span data-stu-id="b6f00-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="b6f00-148">O padrão anterior `ExampleJsInterop` mostrado na classe também pode ser implementado em um componente:</span><span class="sxs-lookup"><span data-stu-id="b6f00-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="b6f00-149">Quando o componente ou classe não `DotNetObjectReference`descartar o , descarte o `.dispose()`objeto no cliente ligando:</span><span class="sxs-lookup"><span data-stu-id="b6f00-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="b6f00-150">Chamada do método de instância do componente</span><span class="sxs-lookup"><span data-stu-id="b6f00-150">Component instance method call</span></span>

<span data-ttu-id="b6f00-151">Para invocar os métodos .NET de um componente:</span><span class="sxs-lookup"><span data-stu-id="b6f00-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="b6f00-152">Use `invokeMethod` a `invokeMethodAsync` função ou para fazer uma chamada de método estático para o componente.</span><span class="sxs-lookup"><span data-stu-id="b6f00-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="b6f00-153">O método estático do componente envolve a chamada ao `Action`seu método de ocorrência como um invocado .</span><span class="sxs-lookup"><span data-stu-id="b6f00-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="b6f00-154">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="b6f00-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="b6f00-155">*Páginas/JSInteropComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="b6f00-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="b6f00-156">Quando houver vários componentes, cada um com métodos de ocorrência para chamar, `Action`use uma classe auxiliar para invocar os métodos de ocorrência (as s) de cada componente.</span><span class="sxs-lookup"><span data-stu-id="b6f00-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="b6f00-157">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b6f00-157">In the following example:</span></span>

* <span data-ttu-id="b6f00-158">O `JSInterop` componente `ListItem` contém vários componentes.</span><span class="sxs-lookup"><span data-stu-id="b6f00-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="b6f00-159">Cada `ListItem` componente é composto por uma mensagem e um botão.</span><span class="sxs-lookup"><span data-stu-id="b6f00-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="b6f00-160">Quando `ListItem` um botão de `ListItem`componente `UpdateMessage` é selecionado, esse método altera o texto do item da lista e oculta o botão.</span><span class="sxs-lookup"><span data-stu-id="b6f00-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="b6f00-161">*MessageUpdateInvokeHelper.cs:*</span><span class="sxs-lookup"><span data-stu-id="b6f00-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="b6f00-162">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="b6f00-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="b6f00-163">*Compartilhado/ListItem.razor:*</span><span class="sxs-lookup"><span data-stu-id="b6f00-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="b6f00-164">*Páginas/JSInterop.razor:*</span><span class="sxs-lookup"><span data-stu-id="b6f00-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="b6f00-165">Evite referências de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="b6f00-165">Avoid circular object references</span></span>

<span data-ttu-id="b6f00-166">Objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="b6f00-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="b6f00-167">Chamadas do método .NET.</span><span class="sxs-lookup"><span data-stu-id="b6f00-167">.NET method calls.</span></span>
* <span data-ttu-id="b6f00-168">O método JavaScript chama a partir de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="b6f00-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="b6f00-169">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="b6f00-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="b6f00-170">As referências circulares não são suportadas, tome duas (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="b6f00-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="b6f00-171">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="b6f00-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="b6f00-172">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b6f00-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="b6f00-173">Exemplo InteropComponent.razor (repositório dotnet/AspNetCore GitHub, ramo de versão 3.1)</span><span class="sxs-lookup"><span data-stu-id="b6f00-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="b6f00-174">[Realizar grandes transferências Blazor de dados em aplicativos do Servidor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="b6f00-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
