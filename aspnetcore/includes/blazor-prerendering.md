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
Embora Blazor um aplicativo do Server esteja pré-renderizando, certas ações, como chamar o JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida. Os componentes podem precisar renderizar de forma diferente quando pré-renderizados.

Para atrasar as chamadas de interop do JavaScript até que a conexão com o navegador seja estabelecida, você pode usar o [evento do ciclo de vida do componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). Este evento só é chamado depois que o aplicativo é totalmente renderizado e a conexão com o cliente é estabelecida.

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

Para o código de `setElementText` exemplo anterior, `<head>` forneça umaBlazor função JavaScript dentro do elemento deBlazor *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Server). A função é `IJSRuntime.InvokeVoidAsync` chamada com e não retorna um valor:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> O exemplo anterior modifica o Modelo de Objeto de Documento (DOM) diretamente para fins de demonstração. Modificar diretamente o DOM com JavaScript não é recomendado na maioria Blazordos cenários porque o JavaScript pode interferir no rastreamento de alterações.

O componente a seguir demonstra como usar o JavaScript interop como parte da lógica de inicialização de um componente de uma maneira compatível com a pré-renderização. O componente mostra que é possível acionar uma `OnAfterRenderAsync`atualização de renderização de dentro . O desenvolvedor deve evitar criar um loop infinito neste cenário.

Onde `JSRuntime.InvokeAsync` é `ElementRef` chamado, só `OnAfterRenderAsync` é usado em e não em qualquer método de ciclo de vida anterior porque não há nenhum elemento JavaScript até depois que o componente é renderizado.

[StateHasChanged](xref:blazor/lifecycle#state-changes) é chamado para rerenderizar o componente com o novo estado obtido a partir da chamada interop JavaScript. O código não cria um `StateHasChanged` loop infinito `infoFromJs` porque `null`só é chamado quando é .

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

Para o código de `setElementText` exemplo anterior, `<head>` forneça umaBlazor função JavaScript dentro do elemento deBlazor *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Server). A função é `IJSRuntime.InvokeAsync` chamada com e retorna um valor:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> O exemplo anterior modifica o Modelo de Objeto de Documento (DOM) diretamente para fins de demonstração. Modificar diretamente o DOM com JavaScript não é recomendado na maioria Blazordos cenários porque o JavaScript pode interferir no rastreamento de alterações.
