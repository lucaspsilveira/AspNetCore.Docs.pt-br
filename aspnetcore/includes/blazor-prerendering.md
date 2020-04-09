---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659715"
---
<span data-ttu-id="e1b92-101">Embora Blazor um aplicativo do Server esteja pré-renderizando, certas ações, como chamar o JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="e1b92-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="e1b92-102">Os componentes podem precisar renderizar de forma diferente quando pré-renderizados.</span><span class="sxs-lookup"><span data-stu-id="e1b92-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="e1b92-103">Para atrasar as chamadas de interop do JavaScript até que a conexão com o navegador seja estabelecida, você pode usar o [evento do ciclo de vida do componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="e1b92-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="e1b92-104">Este evento só é chamado depois que o aplicativo é totalmente renderizado e a conexão com o cliente é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="e1b92-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="e1b92-105">Para o código de `setElementText` exemplo anterior, `<head>` forneça umaBlazor função JavaScript dentro do elemento deBlazor *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Server).</span><span class="sxs-lookup"><span data-stu-id="e1b92-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="e1b92-106">A função é `IJSRuntime.InvokeVoidAsync` chamada com e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="e1b92-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="e1b92-107">O exemplo anterior modifica o Modelo de Objeto de Documento (DOM) diretamente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="e1b92-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="e1b92-108">Modificar diretamente o DOM com JavaScript não é recomendado na maioria Blazordos cenários porque o JavaScript pode interferir no rastreamento de alterações.</span><span class="sxs-lookup"><span data-stu-id="e1b92-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="e1b92-109">O componente a seguir demonstra como usar o JavaScript interop como parte da lógica de inicialização de um componente de uma maneira compatível com a pré-renderização.</span><span class="sxs-lookup"><span data-stu-id="e1b92-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="e1b92-110">O componente mostra que é possível acionar uma `OnAfterRenderAsync`atualização de renderização de dentro .</span><span class="sxs-lookup"><span data-stu-id="e1b92-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="e1b92-111">O desenvolvedor deve evitar criar um loop infinito neste cenário.</span><span class="sxs-lookup"><span data-stu-id="e1b92-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="e1b92-112">Onde `JSRuntime.InvokeAsync` é `ElementRef` chamado, só `OnAfterRenderAsync` é usado em e não em qualquer método de ciclo de vida anterior porque não há nenhum elemento JavaScript até depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="e1b92-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="e1b92-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) é chamado para rerenderizar o componente com o novo estado obtido a partir da chamada interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e1b92-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="e1b92-114">O código não cria um `StateHasChanged` loop infinito `infoFromJs` porque `null`só é chamado quando é .</span><span class="sxs-lookup"><span data-stu-id="e1b92-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="e1b92-115">Para o código de `setElementText` exemplo anterior, `<head>` forneça umaBlazor função JavaScript dentro do elemento deBlazor *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Server).</span><span class="sxs-lookup"><span data-stu-id="e1b92-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="e1b92-116">A função é `IJSRuntime.InvokeAsync` chamada com e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="e1b92-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="e1b92-117">O exemplo anterior modifica o Modelo de Objeto de Documento (DOM) diretamente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="e1b92-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="e1b92-118">Modificar diretamente o DOM com JavaScript não é recomendado na maioria Blazordos cenários porque o JavaScript pode interferir no rastreamento de alterações.</span><span class="sxs-lookup"><span data-stu-id="e1b92-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
