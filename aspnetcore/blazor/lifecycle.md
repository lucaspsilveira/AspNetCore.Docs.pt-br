---
title: Ciclo Blazor de vida ASP.NET Core
author: guardrex
description: Saiba como usar Razor métodos de ciclo de vida do Blazor componente em ASP.NET Core aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 571f14247efe08ac6abbd6d1e2720656f94c213c
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967448"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="6ab20-103">Ciclo Blazor de vida ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ab20-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="6ab20-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6ab20-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="6ab20-105">A Blazor estrutura inclui métodos de ciclo de vida síncronos e assíncronos.</span><span class="sxs-lookup"><span data-stu-id="6ab20-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="6ab20-106">Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="6ab20-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="6ab20-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="6ab20-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="6ab20-108">Métodos de inicialização de componente</span><span class="sxs-lookup"><span data-stu-id="6ab20-108">Component initialization methods</span></span>

<span data-ttu-id="6ab20-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="6ab20-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="6ab20-110">Use `OnInitializedAsync` quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="6ab20-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="6ab20-111">Para uma operação síncrona, substitua `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="6ab20-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="6ab20-112">Para executar uma operação assíncrona, `OnInitializedAsync` substitua e use `await` a palavra-chave na operação:</span><span class="sxs-lookup"><span data-stu-id="6ab20-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="6ab20-113">Aplicativos de servidor que preparam `OnInitializedAsync` [sua chamada de conteúdo](xref:blazor/hosting-model-configuration#render-mode) **_duas vezes_**:</span><span class="sxs-lookup"><span data-stu-id="6ab20-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="6ab20-114">Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="6ab20-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="6ab20-115">Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="6ab20-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="6ab20-116">Para impedir que o código `OnInitializedAsync` do desenvolvedor seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="6ab20-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="6ab20-117">Enquanto um Blazor aplicativo de servidor está sendo renderizado, determinadas ações, como chamar em JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="6ab20-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="6ab20-118">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="6ab20-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="6ab20-119">Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="6ab20-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="6ab20-120">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="6ab20-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ab20-121">Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="6ab20-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="6ab20-122">Antes de os parâmetros serem definidos</span><span class="sxs-lookup"><span data-stu-id="6ab20-122">Before parameters are set</span></span>

<span data-ttu-id="6ab20-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>define os parâmetros fornecidos pelo pai do componente na árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="6ab20-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="6ab20-124"><xref:Microsoft.AspNetCore.Components.ParameterView>contém o conjunto inteiro de valores de parâmetro a `SetParametersAsync` cada vez que é chamado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="6ab20-125">A implementação padrão de `SetParametersAsync` define o valor de cada propriedade com o `[Parameter]` atributo `[CascadingParameter]` ou que tem um valor correspondente no `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="6ab20-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="6ab20-126">Os parâmetros que não têm um valor correspondente `ParameterView` em são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="6ab20-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="6ab20-127">Se `base.SetParametersAync` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="6ab20-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="6ab20-128">Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="6ab20-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="6ab20-129">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="6ab20-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ab20-130">Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="6ab20-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="6ab20-131">Depois que os parâmetros são definidos</span><span class="sxs-lookup"><span data-stu-id="6ab20-131">After parameters are set</span></span>

<span data-ttu-id="6ab20-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> são chamados de:</span><span class="sxs-lookup"><span data-stu-id="6ab20-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="6ab20-133">Quando o componente é inicializado e recebeu seu primeiro conjunto de parâmetros de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="6ab20-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="6ab20-134">Quando o componente pai é renderizado novamente e fornece:</span><span class="sxs-lookup"><span data-stu-id="6ab20-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="6ab20-135">Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="6ab20-136">Qualquer parâmetro de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="6ab20-136">Any complex-typed parameters.</span></span> <span data-ttu-id="6ab20-137">A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="6ab20-138">O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer `OnParametersSetAsync` durante o evento de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="6ab20-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="6ab20-139">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="6ab20-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ab20-140">Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="6ab20-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="6ab20-141">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="6ab20-141">After component render</span></span>

<span data-ttu-id="6ab20-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="6ab20-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="6ab20-143">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="6ab20-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="6ab20-144">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="6ab20-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="6ab20-145">O `firstRender` parâmetro para `OnAfterRenderAsync` e `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="6ab20-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="6ab20-146">É definido como `true` a primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="6ab20-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="6ab20-147">Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="6ab20-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="6ab20-148">O trabalho assíncrono imediatamente após a renderização deve ocorrer `OnAfterRenderAsync` durante o evento do ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="6ab20-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="6ab20-149">Mesmo que você retorne <xref:System.Threading.Tasks.Task> um `OnAfterRenderAsync`do, a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída.</span><span class="sxs-lookup"><span data-stu-id="6ab20-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="6ab20-150">Isso é para evitar um loop de renderização infinito.</span><span class="sxs-lookup"><span data-stu-id="6ab20-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="6ab20-151">Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="6ab20-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="6ab20-152">`OnAfterRender`e `OnAfterRenderAsync` *não são chamados durante o pré-processamento no servidor.*</span><span class="sxs-lookup"><span data-stu-id="6ab20-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="6ab20-153">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="6ab20-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="6ab20-154">Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="6ab20-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="6ab20-155">Suprimir atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="6ab20-155">Suppress UI refreshing</span></span>

<span data-ttu-id="6ab20-156">Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="6ab20-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="6ab20-157">Se a implementação retornar `true`, a interface do usuário será atualizada:</span><span class="sxs-lookup"><span data-stu-id="6ab20-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="6ab20-158">`ShouldRender`é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="6ab20-159">Mesmo se `ShouldRender` for substituído, o componente sempre será renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="6ab20-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="6ab20-160">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="6ab20-160">State changes</span></span>

<span data-ttu-id="6ab20-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Notifica o componente de que seu estado foi alterado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="6ab20-162">Quando aplicável, a `StateHasChanged` chamada faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="6ab20-163">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="6ab20-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="6ab20-164">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="6ab20-165">Os objetos podem `null` ser ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="6ab20-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="6ab20-166">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="6ab20-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="6ab20-167">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma `null`mensagem de carregamento) enquanto objetos são.</span><span class="sxs-lookup"><span data-stu-id="6ab20-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="6ab20-168">No `FetchData` componente dos Blazor modelos, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="6ab20-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="6ab20-169">Quando `forecasts` é `null`, uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="6ab20-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="6ab20-170">Depois que `Task` o retornado `OnInitializedAsync` por for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="6ab20-171">*Páginas/FetchData. Razor* no modelo Blazor de servidor:</span><span class="sxs-lookup"><span data-stu-id="6ab20-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="6ab20-172">Descarte de componentes com IDisposable</span><span class="sxs-lookup"><span data-stu-id="6ab20-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="6ab20-173">Se um componente implementa <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="6ab20-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="6ab20-174">O componente a seguir `@implements IDisposable` usa o `Dispose` e o método:</span><span class="sxs-lookup"><span data-stu-id="6ab20-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="6ab20-175">A <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> chamada `Dispose` no não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="6ab20-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="6ab20-176">`StateHasChanged`pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.</span><span class="sxs-lookup"><span data-stu-id="6ab20-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="6ab20-177">Cancele a assinatura de manipuladores de eventos de eventos .NET.</span><span class="sxs-lookup"><span data-stu-id="6ab20-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="6ab20-178">Os exemplos de [ Blazor formulário](xref:blazor/forms-validation) a seguir mostram como desvincular um manipulador de eventos `Dispose` no método:</span><span class="sxs-lookup"><span data-stu-id="6ab20-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="6ab20-179">Campo privado e abordagem lambda</span><span class="sxs-lookup"><span data-stu-id="6ab20-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="6ab20-180">Abordagem do método privado</span><span class="sxs-lookup"><span data-stu-id="6ab20-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="6ab20-181">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="6ab20-181">Handle errors</span></span>

<span data-ttu-id="6ab20-182">Para obter informações sobre como lidar com erros durante a execução <xref:blazor/handle-errors#lifecycle-methods>do método de ciclo de vida, consulte.</span><span class="sxs-lookup"><span data-stu-id="6ab20-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="6ab20-183">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="6ab20-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="6ab20-184">Em um Blazor aplicativo de servidor `RenderMode` quando `ServerPrerendered`é, o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="6ab20-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="6ab20-185">Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="6ab20-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="6ab20-186">Se o método de ciclo de vida de [{Async} OnInitialized](#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="6ab20-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="6ab20-187">Quando o componente é renderizado estaticamente.</span><span class="sxs-lookup"><span data-stu-id="6ab20-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="6ab20-188">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="6ab20-188">After the server connection has been established.</span></span>

<span data-ttu-id="6ab20-189">Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="6ab20-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="6ab20-190">Para evitar o cenário de processamento duplo em um Blazor aplicativo de servidor:</span><span class="sxs-lookup"><span data-stu-id="6ab20-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="6ab20-191">Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ab20-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="6ab20-192">Use o identificador durante o pré-processamento para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="6ab20-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="6ab20-193">Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="6ab20-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="6ab20-194">O código a seguir demonstra uma `WeatherForecastService` atualização em um aplicativo de Blazor servidor baseado em modelo que evita a renderização dupla:</span><span class="sxs-lookup"><span data-stu-id="6ab20-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
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
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="6ab20-195">Para obter mais informações sobre `RenderMode`o, <xref:blazor/hosting-model-configuration#render-mode>consulte.</span><span class="sxs-lookup"><span data-stu-id="6ab20-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="6ab20-196">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="6ab20-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
