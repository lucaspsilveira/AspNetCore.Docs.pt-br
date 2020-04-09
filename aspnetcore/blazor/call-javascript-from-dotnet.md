---
title: Chamar funções JavaScript a partir de métodos .NET no ASP.NET CoreBlazor
author: guardrex
description: Aprenda a invocar funções JavaScript a Blazor partir de métodos .NET em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977009"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>Chamar funções JavaScript a partir de métodos .NET no ASP.NET CoreBlazor

Por [Javier Calvarro Nelson,](https://github.com/javiercn) [Daniel Roth](https://github.com/danroth27)e Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Um Blazor aplicativo pode invocar funções JavaScript de métodos .NET e métodos .NET a partir de funções JavaScript. Esses cenários são chamados *de interoperabilidade JavaScript* *(Interop JS).*

Este artigo abrange a invocação de funções JavaScript de .NET. Para obter informações sobre como chamar métodos <xref:blazor/call-dotnet-from-javascript>.NET do JavaScript, consulte .

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Para ligar para JavaScript de `IJSRuntime` .NET, use a abstração. Para emitir chamadas de interop `IJSRuntime` JS, injete a abstração em seu componente. O `InvokeAsync<T>` método tem um identificador para a função JavaScript que você deseja invocar juntamente com qualquer número de argumentos serializáveis json. O identificador de função é`window`relativo ao escopo global ( ). Se você quiser `window.someScope.someFunction`ligar, o `someScope.someFunction`identificador é . Não há necessidade de registrar a função antes que ela seja chamada. O tipo `T` de retorno também deve ser serializável JSON. `T`deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.

Para Blazor aplicativos do Servidor com pré-renderização ativada, não é possível chamar o JavaScript durante a pré-renderização inicial. As chamadas interop JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida. Para obter mais informações, consulte o [Detect quando um Blazor aplicativo do Servidor estiver fazendo pré-renderização.](#detect-when-a-blazor-server-app-is-prerendering)

O exemplo a seguir é baseado no [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), um decodificador baseado em JavaScript. O exemplo demonstra como invocar uma função JavaScript a partir de um método C#. A função JavaScript aceita uma matriz de bytes de um método C#, decodifica a matriz e retorna o texto ao componente para exibição.

Dentro `<head>` do elementoBlazor de *wwwroot/index.html* ( WebAssembly) ouBlazor *Pages/_Host.cshtml* (Server), forneça uma função JavaScript que usa `TextDecoder` para decodificar um array passado e retornar o valor decodificado:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado a partir de um arquivo JavaScript *(.js)* com uma referência ao arquivo de script:

```html
<script src="exampleJsInterop.js"></script>
```

O seguinte componente:

* Invoca a `convertArray` função JavaScript usando `JSRuntime` quando um botão de componente **(Convert Array)** é selecionado.
* Depois que a função JavaScript é chamada, a matriz passada é convertida em uma seqüência de string. A seqüência é devolvida ao componente para exibição.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Para utilizar `IJSRuntime` a abstração, adote qualquer uma das seguintes abordagens:

* Injete a `IJSRuntime` abstração no componente Razor *(.razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Dentro `<head>` do elementoBlazor de *wwwroot/index.html* ( WebAssembly) ouBlazor *Pages/_Host.cshtml* (Server), forneça uma `handleTickerChanged` função JavaScript. A função é `IJSRuntime.InvokeVoidAsync` chamada com e não retorna um valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Injete a `IJSRuntime` abstração em uma classe (*.cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Dentro `<head>` do elementoBlazor de *wwwroot/index.html* ( WebAssembly) ouBlazor *Pages/_Host.cshtml* (Server), forneça uma `handleTickerChanged` função JavaScript. A função é `JSRuntime.InvokeAsync` chamada com e retorna um valor:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Para geração dinâmica de conteúdo com `[Inject]` [BuildRenderTree,](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)use o atributo:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

No aplicativo de amostra do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interage com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:

* `showPrompt`&ndash; Produz um prompt para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.
* `displayWelcome`&ndash; Atribui uma mensagem de boas-vindas do `id` chamador a um objeto DOM com um de `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Coloque `<script>` a tag que faz referência ao arquivoBlazor JavaScript no arquivo *wwwroot/index.html* (WebAssembly) ou *páginas/_Host.cshtml* (Server).Blazor

*wwwroot/index.html* Blazor (WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Páginas/_Host.cshtml* Blazor (Servidor):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Não coloque uma `<script>` tag em um `<script>` arquivo de componente porque a tag não pode ser atualizada dinamicamente.

Os métodos .NET intercalam com as funções JavaScript no arquivo `IJSRuntime.InvokeAsync<T>` *JsInterop.js por* chamada .

A `IJSRuntime` abstração é assíncrona Blazor para permitir cenários do Servidor. Se o aplicativo Blazor for um aplicativo WebAssembly e você quiser invocar uma `IJSInProcessRuntime` função `Invoke<T>` JavaScript sincronizadamente, abaixado para e chamar em vez disso. Recomendamos que a maioria das bibliotecas interop JS use as APIs de async para garantir que as bibliotecas estejam disponíveis em todos os cenários.

O aplicativo de amostra inclui um componente para demonstrar a interop JS. O componente:

* Recebe a entrada do usuário através de um prompt JavaScript.
* Retorna o texto ao componente para processamento.
* Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.

*Páginas/JSInterop.razor:*

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. Quando `TriggerJsPrompt` é executado selecionando o botão **Trigger JavaScript** Prompt `showPrompt` do componente, a função JavaScript fornecida no arquivo *wwwroot/exampleJsInterop.js* é chamada.
1. A `showPrompt` função aceita a entrada do usuário (nome do usuário), que é codificado por HTML e devolvido ao componente. O componente armazena o nome do usuário `name`em uma variável local, .
1. A seqüência de caracteres armazenada `name` é incorporada em uma `displayWelcome`mensagem de boas-vindas, que é passada para uma função JavaScript, que torna a mensagem de boas-vindas em uma tag de título.

## <a name="call-a-void-javascript-function"></a>Chamada de uma função JavaScript vazia

As funções JavaScript que retornam [void(0)/void](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) `IJSRuntime.InvokeVoidAsync`0 ou [indefinidas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são chamadas com .

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Detecte Blazor quando um aplicativo do Servidor está fazendo a pré-renderização
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Capturar referências a elementos

Alguns cenários de Interop JS requerem referências a elementos HTML. Por exemplo, uma biblioteca de ia de usuário pode exigir uma referência de elemento para inicialização, ou você pode precisar chamar APIs semelhantes a comandos em um elemento, como `focus` ou `play`.

Capturar referências a elementos HTML em um componente usando a seguinte abordagem:

* Adicione `@ref` um atributo ao elemento HTML.
* Defina um `ElementReference` campo de tipo cujo `@ref` nome corresponda ao valor do atributo.

O exemplo a seguir mostra `username` `<input>` a captura de uma referência ao elemento:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Use apenas uma referência de elemento para alterar o conteúdo Blazorde um elemento vazio que não interage com . Este cenário é útil quando uma API de terceiros fornece conteúdo para o elemento. Como Blazor não interage com o elemento, não há possibilidade Blazorde um conflito entre a representação do elemento e o DOM.
>
> No exemplo a seguir, é *perigoso* alterar o conteúdo da`ul`lista Blazor não ordenada ( ) porque interage com o DOM para preencher os itens da lista deste elemento (`<li>`):
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Se JS interop mutar o `MyList` Blazor conteúdo do elemento e tentar aplicar diffs ao elemento, os difusores não corresponderão ao DOM.

No que diz respeito ao `ElementReference` código .NET, um é uma alça opaca. A *única* coisa que `ElementReference` você pode fazer com é passá-lo através de código JavaScript via JS interop. Quando você faz isso, o código lado `HTMLElement` JavaScript recebe uma instância, que pode ser usada com APIs do DOM normais.

Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:

*exemploJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Para chamar uma função JavaScript que não `IJSRuntime.InvokeVoidAsync`retorne um valor, use . O código a seguir define o foco na entrada de nome `ElementReference`de usuário chamando a função JavaScript anterior com a captura:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Para usar um método de extensão, `IJSRuntime` crie um método de extensão estática que receba a instância:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

O `Focus` método é chamado diretamente no objeto. O exemplo a seguir `Focus` assume que `JsInteropClasses` o método está disponível no namespace:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> A `username` variável só é preenchida após a renderização do componente. Se um despovoado `ElementReference` for passado para o código `null`JavaScript, o código JavaScript receberá um valor de . Para manipular as referências do elemento após a renderização concluída (para definir o foco inicial em um elemento) use os [métodos onAfterRenderAsync ou OnAfterRender.](xref:blazor/lifecycle#after-component-render)

Ao trabalhar com tipos genéricos e devolver um valor, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`é chamado diretamente no objeto com um tipo. O exemplo a seguir `GenericMethod` assume que `JsInteropClasses` o está disponível no namespace:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementos de referência entre componentes

Um `ElementReference` só é garantido válido `OnAfterRender` no método de um `struct`componente (e uma referência de elemento é a), de modo que uma referência de elemento não pode ser passada entre componentes.

Para que um componente pai disponibilize uma referência de elemento a outros componentes, o componente pai pode:

* Permitir que os componentes da criança registrem retornos de chamada.
* Invoque os retornos `OnAfterRender` de chamada registrados durante o evento com a referência do elemento aprovado. Indiretamente, essa abordagem permite que os componentes da criança interajam com a referência do elemento dos pais.

O Blazor exemplo do WebAssembly a seguir ilustra a abordagem.

Na `<head>` *wwwroot/index.html:*

```html
<style>
    .red { color: red }
</style>
```

Na `<body>` *wwwroot/index.html:*

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Páginas/Index.razor* (componente pai):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Páginas/Index.razor.cs:*

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Compartilhado/SurveyPrompt.razor* (componente filho):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Compartilhado/SurveyPrompt.razor.cs:*

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Harden JS interop chamadas

O interop JS pode falhar devido a erros de rede e deve ser tratado como não confiável. Por padrão, Blazor um aplicativo do Server avalia as chamadas de interop js no servidor após um minuto. Se um aplicativo pode tolerar um tempo mais agressivo, como 10 segundos, defina o tempo máximo usando uma das seguintes abordagens:

* Globalmente `Startup.ConfigureServices`em , especifique o tempo mais caro:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Por invocação no código do componente, uma única chamada pode especificar o tempo hámenos:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Para obter mais informações <xref:security/blazor/server>sobre o esgotamento dos recursos, consulte .

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evite referências de objetos circulares

Objetos que contêm referências circulares não podem ser serializados no cliente para:

* Chamadas do método .NET.
* O método JavaScript chama a partir de C# quando o tipo de retorno tem referências circulares.

Para obter mais informações, consulte os seguintes problemas:

* [As referências circulares não são suportadas, tome duas (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/call-dotnet-from-javascript>
* [Exemplo InteropComponent.razor (repositório dotnet/AspNetCore GitHub, ramo de versão 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Realizar grandes transferências Blazor de dados em aplicativos do Servidor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
