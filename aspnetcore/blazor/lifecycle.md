---
title: ciclo Blazor de vida do núcleo ASP.NET
author: guardrex
description: Aprenda a usar os métodos de Blazor ciclo de vida dos componentes Razor em ASP.NET aplicativos Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791460"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="8ff22-103">ciclo Blazor de vida do núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="8ff22-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="8ff22-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8ff22-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8ff22-105">A Blazor estrutura inclui métodos síncronos e assíncronos do ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="8ff22-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="8ff22-106">Substituir métodos do ciclo de vida para executar operações adicionais em componentes durante a inicialização e renderização dos componentes.</span><span class="sxs-lookup"><span data-stu-id="8ff22-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="8ff22-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="8ff22-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="8ff22-108">Métodos de inicialização de componentes</span><span class="sxs-lookup"><span data-stu-id="8ff22-108">Component initialization methods</span></span>

<span data-ttu-id="8ff22-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="8ff22-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="8ff22-110">Use `OnInitializedAsync` quando o componente realizar uma operação assíncrona e deve ser atualizado quando a operação estiver concluída.</span><span class="sxs-lookup"><span data-stu-id="8ff22-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="8ff22-111">Para uma operação síncrona, anular: `OnInitialized`</span><span class="sxs-lookup"><span data-stu-id="8ff22-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="8ff22-112">Para realizar uma operação assíncrona, anule `OnInitializedAsync` e use a `await` palavra-chave na operação:</span><span class="sxs-lookup"><span data-stu-id="8ff22-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="8ff22-113">Aplicativos de servidor que `OnInitializedAsync` [prerenderizam sua](xref:blazor/hosting-model-configuration#render-mode) chamada de conteúdo duas **_vezes:_**</span><span class="sxs-lookup"><span data-stu-id="8ff22-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="8ff22-114">Uma vez quando o componente é inicialmente renderizado estáticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="8ff22-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="8ff22-115">Uma segunda vez quando o navegador estabelece uma conexão de volta ao servidor.</span><span class="sxs-lookup"><span data-stu-id="8ff22-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="8ff22-116">Para evitar que `OnInitializedAsync` o código do desenvolvedor seja executado duas vezes, consulte a [reconexão Stateful após a seção de pré-renderização.](#stateful-reconnection-after-prerendering)</span><span class="sxs-lookup"><span data-stu-id="8ff22-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="8ff22-117">Embora Blazor um aplicativo do Server esteja pré-renderizando, certas ações, como chamar o JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="8ff22-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="8ff22-118">Os componentes podem precisar renderizar de forma diferente quando pré-renderizados.</span><span class="sxs-lookup"><span data-stu-id="8ff22-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="8ff22-119">Para obter mais informações, consulte o [Detectar quando o aplicativo estiver fazendo pré-renderização.](#detect-when-the-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="8ff22-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="8ff22-120">Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação.</span><span class="sxs-lookup"><span data-stu-id="8ff22-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="8ff22-121">Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="8ff22-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="8ff22-122">Antes que os parâmetros sejam definidos</span><span class="sxs-lookup"><span data-stu-id="8ff22-122">Before parameters are set</span></span>

<span data-ttu-id="8ff22-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>define parâmetros fornecidos pelo pai do componente na árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="8ff22-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="8ff22-124"><xref:Microsoft.AspNetCore.Components.ParameterView>contém todo o conjunto de `SetParametersAsync` valores de parâmetros cada vez é chamado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="8ff22-125">A implementação `SetParametersAsync` padrão de define o `[Parameter]` `[CascadingParameter]` valor de cada propriedade `ParameterView`com o ou atributo que tenha um valor correspondente no .</span><span class="sxs-lookup"><span data-stu-id="8ff22-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="8ff22-126">Os parâmetros que não possuem `ParameterView` um valor correspondente são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="8ff22-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="8ff22-127">Se `base.SetParametersAync` não for invocado, o código personalizado pode interpretar o valor dos parâmetros de entrada de qualquer maneira necessária.</span><span class="sxs-lookup"><span data-stu-id="8ff22-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="8ff22-128">Por exemplo, não há necessidade de atribuir os parâmetros de entrada às propriedades da classe.</span><span class="sxs-lookup"><span data-stu-id="8ff22-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="8ff22-129">Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação.</span><span class="sxs-lookup"><span data-stu-id="8ff22-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="8ff22-130">Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="8ff22-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="8ff22-131">Depois que os parâmetros forem definidos</span><span class="sxs-lookup"><span data-stu-id="8ff22-131">After parameters are set</span></span>

<span data-ttu-id="8ff22-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> são chamados:</span><span class="sxs-lookup"><span data-stu-id="8ff22-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="8ff22-133">Quando o componente é inicializado e recebe seu primeiro conjunto de parâmetros a partir de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="8ff22-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="8ff22-134">Quando o componente pai rerenderiza e fornece:</span><span class="sxs-lookup"><span data-stu-id="8ff22-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="8ff22-135">Apenas tipos primitivos imutáveis conhecidos dos quais pelo menos um parâmetro mudou.</span><span class="sxs-lookup"><span data-stu-id="8ff22-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="8ff22-136">Quaisquer parâmetros de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="8ff22-136">Any complex-typed parameters.</span></span> <span data-ttu-id="8ff22-137">A estrutura não pode saber se os valores de um parâmetro de tipo complexo sofreram mutação internamente, por isso trata o parâmetro definido como alterado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="8ff22-138">O trabalho assíncrono ao aplicar parâmetros `OnParametersSetAsync` e valores de propriedade deve ocorrer durante o evento do ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="8ff22-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="8ff22-139">Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação.</span><span class="sxs-lookup"><span data-stu-id="8ff22-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="8ff22-140">Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="8ff22-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="8ff22-141">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="8ff22-141">After component render</span></span>

<span data-ttu-id="8ff22-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> são chamados depois que um componente tiver terminado a renderização.</span><span class="sxs-lookup"><span data-stu-id="8ff22-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="8ff22-143">As referências de elementos e componentes são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="8ff22-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="8ff22-144">Use este estágio para executar etapas adicionais de inicialização usando o conteúdo renderizado, como ativar bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="8ff22-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="8ff22-145">O `firstRender` parâmetro `OnAfterRenderAsync` para `OnAfterRender`e:</span><span class="sxs-lookup"><span data-stu-id="8ff22-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="8ff22-146">É definido `true` como a primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="8ff22-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="8ff22-147">Pode ser usado para garantir que o trabalho de inicialização seja realizado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="8ff22-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="8ff22-148">O trabalho assíncrono imediatamente após `OnAfterRenderAsync` a renderização deve ocorrer durante o evento do ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="8ff22-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="8ff22-149">Mesmo se você <xref:System.Threading.Tasks.Task> `OnAfterRenderAsync`retornar a partir de, a estrutura não agenda um novo ciclo de renderização para o seu componente uma vez que essa tarefa seja concluída.</span><span class="sxs-lookup"><span data-stu-id="8ff22-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="8ff22-150">Isto é para evitar um loop de renderização infinita.</span><span class="sxs-lookup"><span data-stu-id="8ff22-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="8ff22-151">É diferente dos outros métodos do ciclo de vida, que programam um novo ciclo de renderização quando a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="8ff22-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="8ff22-152">`OnAfterRender`e `OnAfterRenderAsync` *não são chamados ao pré-renderização no servidor.*</span><span class="sxs-lookup"><span data-stu-id="8ff22-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="8ff22-153">Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação.</span><span class="sxs-lookup"><span data-stu-id="8ff22-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="8ff22-154">Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="8ff22-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="8ff22-155">Suprimir a iu de iu</span><span class="sxs-lookup"><span data-stu-id="8ff22-155">Suppress UI refreshing</span></span>

<span data-ttu-id="8ff22-156">Anular <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> para suprimir a iu refrescada.</span><span class="sxs-lookup"><span data-stu-id="8ff22-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="8ff22-157">Se a `true`implementação retornar, a ui será atualizada:</span><span class="sxs-lookup"><span data-stu-id="8ff22-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="8ff22-158">`ShouldRender`é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="8ff22-159">Mesmo `ShouldRender` que seja substituído, o componente é sempre renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="8ff22-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="8ff22-160">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="8ff22-160">State changes</span></span>

<span data-ttu-id="8ff22-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>notifica o componente de que seu estado mudou.</span><span class="sxs-lookup"><span data-stu-id="8ff22-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="8ff22-162">Quando aplicável, `StateHasChanged` a chamada faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="8ff22-163">Lidar com ações de sincronia incompletas na renderização</span><span class="sxs-lookup"><span data-stu-id="8ff22-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="8ff22-164">Ações assíncronas realizadas em eventos do ciclo de vida podem não ter sido concluídas antes da renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="8ff22-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="8ff22-165">Os objetos podem estar `null` ou incompletamente preenchidos com dados enquanto o método do ciclo de vida está sendo executado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="8ff22-166">Forneça lógica de renderização para confirmar se os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="8ff22-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="8ff22-167">Renderizar elementos de IU de espaço reservado `null`(por exemplo, uma mensagem de carregamento) enquanto os objetos são .</span><span class="sxs-lookup"><span data-stu-id="8ff22-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="8ff22-168">No `FetchData` componente dos Blazor modelos, `OnInitializedAsync` é substituído para receber asaudamente`forecasts`dados de previsão ().</span><span class="sxs-lookup"><span data-stu-id="8ff22-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="8ff22-169">Quando `forecasts` `null`é , uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="8ff22-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="8ff22-170">Após `Task` o retorno `OnInitializedAsync` por completo, o componente é rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="8ff22-171">*Páginas/FetchData.razor* no Blazor modelo Servidor:</span><span class="sxs-lookup"><span data-stu-id="8ff22-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="8ff22-172">Descarte de componentes com IDescartável</span><span class="sxs-lookup"><span data-stu-id="8ff22-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="8ff22-173">Se um componente <xref:System.IDisposable>for implementado, o [método Descarte](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da ui.</span><span class="sxs-lookup"><span data-stu-id="8ff22-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="8ff22-174">Os seguintes `@implements IDisposable` usos do componente e o `Dispose` método:</span><span class="sxs-lookup"><span data-stu-id="8ff22-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="8ff22-175">Ligar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` não é suportado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="8ff22-176">`StateHasChanged`pode ser invocado como parte de derrubar o renderizador, então solicitar atualizações de iu naquele momento não é suportado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="8ff22-177">Cancelar os manipuladores de eventos de inscrição de eventos .NET.</span><span class="sxs-lookup"><span data-stu-id="8ff22-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="8ff22-178">Os [ Blazor ](xref:blazor/forms-validation) exemplos de formulário a seguir mostram como `Dispose` desengajá-lo no método:</span><span class="sxs-lookup"><span data-stu-id="8ff22-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="8ff22-179">Campo privado e abordagem lambda</span><span class="sxs-lookup"><span data-stu-id="8ff22-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="8ff22-180">Abordagem de método privado</span><span class="sxs-lookup"><span data-stu-id="8ff22-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="8ff22-181">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="8ff22-181">Handle errors</span></span>

<span data-ttu-id="8ff22-182">Para obter informações sobre erros de manuseio <xref:blazor/handle-errors#lifecycle-methods>durante a execução do método do ciclo de vida, consulte .</span><span class="sxs-lookup"><span data-stu-id="8ff22-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="8ff22-183">Reconexão stateful após pré-renderização</span><span class="sxs-lookup"><span data-stu-id="8ff22-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="8ff22-184">Em Blazor um aplicativo `RenderMode` `ServerPrerendered`server quando é , o componente é inicialmente renderizado estáticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="8ff22-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="8ff22-185">Uma vez que o navegador estabelece uma conexão de volta ao servidor, o componente é renderizado *novamente*e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="8ff22-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="8ff22-186">Se o método [onInitialized{Async}](#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes:*</span><span class="sxs-lookup"><span data-stu-id="8ff22-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="8ff22-187">Quando o componente é pré-renderizado estáticamente.</span><span class="sxs-lookup"><span data-stu-id="8ff22-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="8ff22-188">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="8ff22-188">After the server connection has been established.</span></span>

<span data-ttu-id="8ff22-189">Isso pode resultar em uma mudança perceptível nos dados exibidos na ui quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="8ff22-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="8ff22-190">Para evitar o cenário de Blazor dupla renderização em um aplicativo do Servidor:</span><span class="sxs-lookup"><span data-stu-id="8ff22-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="8ff22-191">Passe em um identificador que pode ser usado para armazenar o estado durante a pré-renderização e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8ff22-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="8ff22-192">Use o identificador durante a pré-renderização para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="8ff22-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="8ff22-193">Use o identificador após a pré-renderização para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="8ff22-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="8ff22-194">O código a `WeatherForecastService` seguir demonstra um Blazor atualizado em um aplicativo do Servidor baseado em modelo que evita a dupla renderização:</span><span class="sxs-lookup"><span data-stu-id="8ff22-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="8ff22-195">Para obter mais `RenderMode`informações <xref:blazor/hosting-model-configuration#render-mode>sobre o , consulte .</span><span class="sxs-lookup"><span data-stu-id="8ff22-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="8ff22-196">Detecte quando o aplicativo está pré-renderizando</span><span class="sxs-lookup"><span data-stu-id="8ff22-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
