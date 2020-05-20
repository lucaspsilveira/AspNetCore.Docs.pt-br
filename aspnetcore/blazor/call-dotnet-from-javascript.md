---
<span data-ttu-id="630da-101">title: ' chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor ' autor: Descrição: ' saiba como invocar métodos .net de funções JavaScript em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="630da-101">title: 'Call .NET methods from JavaScript functions in ASP.NET Core Blazor' author: description: 'Learn how to invoke .NET methods from JavaScript functions in Blazor apps.'</span></span>
<span data-ttu-id="630da-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630da-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630da-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630da-103">'Blazor'</span></span>
- <span data-ttu-id="630da-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630da-104">'Identity'</span></span>
- <span data-ttu-id="630da-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630da-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="630da-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630da-106">'Razor'</span></span>
- <span data-ttu-id="630da-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="630da-107">'SignalR' uid:</span></span> 

---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="630da-108">Chamar métodos .NET de funções JavaScript no ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="630da-108">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="630da-109">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="630da-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="630da-110">Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="630da-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="630da-111">Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).</span><span class="sxs-lookup"><span data-stu-id="630da-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="630da-112">Este artigo aborda a invocação de métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="630da-112">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="630da-113">Para obter informações sobre como chamar funções JavaScript do .NET, consulte <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="630da-113">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="630da-114">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="630da-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="630da-115">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="630da-115">Static .NET method call</span></span>

<span data-ttu-id="630da-116">Para invocar um método .NET estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="630da-116">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="630da-117">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="630da-117">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="630da-118">A versão assíncrona é preferida para dar suporte a Blazor cenários de servidor.</span><span class="sxs-lookup"><span data-stu-id="630da-118">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="630da-119">O método .NET deve ser público, estático e ter o `[JSInvokable]` atributo.</span><span class="sxs-lookup"><span data-stu-id="630da-119">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="630da-120">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="630da-120">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="630da-121">O aplicativo de exemplo inclui um método C# para retornar uma `int` matriz.</span><span class="sxs-lookup"><span data-stu-id="630da-121">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="630da-122">O `JSInvokable` atributo é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="630da-122">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="630da-123">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="630da-123">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="630da-124">O JavaScript servido para o cliente invoca o método .NET do C#.</span><span class="sxs-lookup"><span data-stu-id="630da-124">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="630da-125">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="630da-125">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="630da-126">Quando o botão **disparar o método estático do .net ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="630da-126">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="630da-127">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="630da-127">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="630da-128">O quarto valor de matriz é enviado por push para a matriz ( `data.push(4);` ) retornada por `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="630da-128">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="630da-129">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o `JSInvokableAttribute` Construtor:</span><span class="sxs-lookup"><span data-stu-id="630da-129">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="630da-130">No arquivo JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="630da-130">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="630da-131">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="630da-131">Instance method call</span></span>

<span data-ttu-id="630da-132">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="630da-132">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="630da-133">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="630da-133">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="630da-134">Passe a instância do .NET por referência ao JavaScript:</span><span class="sxs-lookup"><span data-stu-id="630da-134">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="630da-135">Faça uma chamada estática para `DotNetObjectReference.Create` .</span><span class="sxs-lookup"><span data-stu-id="630da-135">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="630da-136">Empacote a instância em uma `DotNetObjectReference` instância e chame `Create` na `DotNetObjectReference` instância.</span><span class="sxs-lookup"><span data-stu-id="630da-136">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="630da-137">Descarte de `DotNetObjectReference` objetos (um exemplo aparece mais adiante nesta seção).</span><span class="sxs-lookup"><span data-stu-id="630da-137">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="630da-138">Invoque os métodos de instância do .NET na instância usando as `invokeMethod` `invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="630da-138">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="630da-139">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="630da-139">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="630da-140">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="630da-140">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="630da-141">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="630da-141">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="630da-142">Quando o botão do **método de instância .net de gatilho HelloHelper. sayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome `Blazor` para o método.</span><span class="sxs-lookup"><span data-stu-id="630da-142">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="630da-143">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="630da-143">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="630da-144">`CallHelloHelperSayHello`Invoca a função JavaScript `sayHello` com uma nova instância do `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="630da-144">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="630da-145">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="630da-145">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="630da-146">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="630da-146">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="630da-147">O nome é passado para o `HelloHelper` Construtor de, que define a `HelloHelper.Name` propriedade.</span><span class="sxs-lookup"><span data-stu-id="630da-147">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="630da-148">Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="630da-148">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="630da-149">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="630da-149">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="630da-150">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="630da-150">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="630da-151">Para evitar um vazamento de memória e permitir a coleta de lixo em um componente que cria um `DotNetObjectReference` , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="630da-151">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="630da-152">Descarte o objeto na classe que criou a `DotNetObjectReference` instância:</span><span class="sxs-lookup"><span data-stu-id="630da-152">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="630da-153">O padrão anterior mostrado na `ExampleJsInterop` classe também pode ser implementado em um componente:</span><span class="sxs-lookup"><span data-stu-id="630da-153">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

* <span data-ttu-id="630da-154">Quando o componente ou classe não descartar o `DotNetObjectReference` , descarte o objeto no cliente chamando `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="630da-154">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="630da-155">Chamada de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="630da-155">Component instance method call</span></span>

<span data-ttu-id="630da-156">Para invocar os métodos .NET de um componente:</span><span class="sxs-lookup"><span data-stu-id="630da-156">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="630da-157">Use a `invokeMethod` `invokeMethodAsync` função ou para fazer uma chamada de método estático para o componente.</span><span class="sxs-lookup"><span data-stu-id="630da-157">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="630da-158">O método estático do componente encapsula a chamada para seu método de instância como uma chamada `Action` .</span><span class="sxs-lookup"><span data-stu-id="630da-158">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="630da-159">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="630da-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="630da-160">*Páginas/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="630da-160">*Pages/JSInteropComponent.razor*:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="630da-161">Quando há vários componentes, cada um com métodos de instância para chamar, use uma classe auxiliar para invocar os métodos de instância (como `Action` s) de cada componente.</span><span class="sxs-lookup"><span data-stu-id="630da-161">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="630da-162">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="630da-162">In the following example:</span></span>

* <span data-ttu-id="630da-163">O `JSInterop` componente contém vários `ListItem` componentes.</span><span class="sxs-lookup"><span data-stu-id="630da-163">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="630da-164">Cada `ListItem` componente é composto por uma mensagem e um botão.</span><span class="sxs-lookup"><span data-stu-id="630da-164">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="630da-165">Quando um `ListItem` botão de componente é selecionado, `ListItem` esse `UpdateMessage` método altera o texto do item da lista e oculta o botão.</span><span class="sxs-lookup"><span data-stu-id="630da-165">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="630da-166">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="630da-166">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="630da-167">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="630da-167">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="630da-168">*Compartilhado/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="630da-168">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="630da-169">*Páginas/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="630da-169">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="630da-170">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="630da-170">Avoid circular object references</span></span>

<span data-ttu-id="630da-171">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="630da-171">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="630da-172">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="630da-172">.NET method calls.</span></span>
* <span data-ttu-id="630da-173">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="630da-173">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="630da-174">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="630da-174">For more information, see the following issues:</span></span>

* [<span data-ttu-id="630da-175">Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="630da-175">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="630da-176">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)</span><span class="sxs-lookup"><span data-stu-id="630da-176">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="630da-177">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="630da-177">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="630da-178">Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="630da-178">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="630da-179">[Executar grandes transferências de dados em Blazor aplicativos de servidor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="630da-179">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
