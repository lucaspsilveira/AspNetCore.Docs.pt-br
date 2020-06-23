---
title: BlazorCiclo de vida ASP.NET Core
author: guardrex
description: Saiba como usar Razor métodos de ciclo de vida do componente em ASP.NET Core Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/lifecycle
ms.openlocfilehash: 61c1dc383728f42c5dac6742fd19d1d22c988913
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242687"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="4def3-103">BlazorCiclo de vida ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4def3-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="4def3-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4def3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4def3-105">A Blazor estrutura inclui métodos de ciclo de vida síncronos e assíncronos.</span><span class="sxs-lookup"><span data-stu-id="4def3-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="4def3-106">Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="4def3-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="4def3-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="4def3-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="4def3-108">Antes de os parâmetros serem definidos</span><span class="sxs-lookup"><span data-stu-id="4def3-108">Before parameters are set</span></span>

<span data-ttu-id="4def3-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>define os parâmetros fornecidos pelo pai do componente na árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="4def3-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="4def3-110"><xref:Microsoft.AspNetCore.Components.ParameterView>contém o conjunto inteiro de valores de parâmetro a cada vez que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> é chamado.</span><span class="sxs-lookup"><span data-stu-id="4def3-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="4def3-111">A implementação padrão de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> define o valor de cada propriedade com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo ou que tem um valor correspondente no <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="4def3-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="4def3-112">Os parâmetros que não têm um valor correspondente em <xref:Microsoft.AspNetCore.Components.ParameterView> são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="4def3-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="4def3-113">Se [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="4def3-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="4def3-114">Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="4def3-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="4def3-115">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="4def3-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4def3-116">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4def3-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="4def3-117">Métodos de inicialização de componente</span><span class="sxs-lookup"><span data-stu-id="4def3-117">Component initialization methods</span></span>

<span data-ttu-id="4def3-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai no <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="4def3-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="4def3-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="4def3-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="4def3-120">Para uma operação síncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="4def3-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="4def3-121">Para executar uma operação assíncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e use o [`await`](/dotnet/csharp/language-reference/operators/await) operador na operação:</span><span class="sxs-lookup"><span data-stu-id="4def3-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="4def3-122">Aplicativos de servidor que preparam [sua chamada de conteúdo](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_duas vezes_**:</span><span class="sxs-lookup"><span data-stu-id="4def3-122"> Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="4def3-123">Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="4def3-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="4def3-124">Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="4def3-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="4def3-125">Para impedir que o código <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> do desenvolvedor seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="4def3-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="4def3-126">Enquanto um Blazor aplicativo de servidor está sendo renderizado, determinadas ações, como chamar em JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="4def3-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="4def3-127">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="4def3-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="4def3-128">Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="4def3-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="4def3-129">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="4def3-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4def3-130">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4def3-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="4def3-131">Depois que os parâmetros são definidos</span><span class="sxs-lookup"><span data-stu-id="4def3-131">After parameters are set</span></span>

<span data-ttu-id="4def3-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> são chamados:</span><span class="sxs-lookup"><span data-stu-id="4def3-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="4def3-133">Depois que o componente é inicializado no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ou no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> .</span><span class="sxs-lookup"><span data-stu-id="4def3-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>.</span></span>
* <span data-ttu-id="4def3-134">Quando o componente pai é renderizado novamente e fornece:</span><span class="sxs-lookup"><span data-stu-id="4def3-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="4def3-135">Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.</span><span class="sxs-lookup"><span data-stu-id="4def3-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="4def3-136">Qualquer parâmetro de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="4def3-136">Any complex-typed parameters.</span></span> <span data-ttu-id="4def3-137">A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.</span><span class="sxs-lookup"><span data-stu-id="4def3-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="4def3-138">O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> evento de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="4def3-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="4def3-139">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="4def3-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4def3-140">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4def3-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="4def3-141">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="4def3-141">After component render</span></span>

<span data-ttu-id="4def3-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="4def3-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="4def3-143">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="4def3-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="4def3-144">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="4def3-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="4def3-145">O `firstRender` parâmetro para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="4def3-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="4def3-146">É definido como `true` a primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="4def3-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="4def3-147">Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="4def3-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="4def3-148">O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> evento do ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="4def3-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="4def3-149">Mesmo que você retorne um <xref:System.Threading.Tasks.Task> do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída.</span><span class="sxs-lookup"><span data-stu-id="4def3-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="4def3-150">Isso é para evitar um loop de renderização infinito.</span><span class="sxs-lookup"><span data-stu-id="4def3-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="4def3-151">Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="4def3-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="4def3-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *não são chamados durante o pré-processamento no servidor.*</span><span class="sxs-lookup"><span data-stu-id="4def3-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="4def3-153">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="4def3-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4def3-154">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4def3-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="4def3-155">Suprimir atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="4def3-155">Suppress UI refreshing</span></span>

<span data-ttu-id="4def3-156">Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="4def3-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="4def3-157">Se a implementação retornar `true` , a interface do usuário será atualizada:</span><span class="sxs-lookup"><span data-stu-id="4def3-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="4def3-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="4def3-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="4def3-159">Mesmo se <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> for substituído, o componente sempre será renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="4def3-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="4def3-160">Para obter mais informações, consulte <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="4def3-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="4def3-161">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="4def3-161">State changes</span></span>

<span data-ttu-id="4def3-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Notifica o componente de que seu estado foi alterado.</span><span class="sxs-lookup"><span data-stu-id="4def3-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="4def3-163">Quando aplicável, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> a chamada faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="4def3-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="4def3-164">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="4def3-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="4def3-165">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="4def3-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="4def3-166">Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="4def3-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="4def3-167">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="4def3-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="4def3-168">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto objetos são `null` .</span><span class="sxs-lookup"><span data-stu-id="4def3-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="4def3-169">No `FetchData` componente dos Blazor modelos, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é substituído para Asychronously receber dados de previsão ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="4def3-169">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="4def3-170">Quando `forecasts` é `null` , uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="4def3-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="4def3-171">Depois que o `Task` retornado por <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="4def3-171">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="4def3-172">`Pages/FetchData.razor`no Blazor modelo de servidor:</span><span class="sxs-lookup"><span data-stu-id="4def3-172">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="4def3-173">Descarte de componentes com IDisposable</span><span class="sxs-lookup"><span data-stu-id="4def3-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="4def3-174">Se um componente implementa <xref:System.IDisposable> , o [ `Dispose` método](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="4def3-174">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="4def3-175">O componente a seguir usa `@implements IDisposable` o e o `Dispose` método:</span><span class="sxs-lookup"><span data-stu-id="4def3-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="4def3-176">A chamada <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> no `Dispose` não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="4def3-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="4def3-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.</span><span class="sxs-lookup"><span data-stu-id="4def3-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="4def3-178">Cancele a assinatura de manipuladores de eventos de eventos .NET.</span><span class="sxs-lookup"><span data-stu-id="4def3-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="4def3-179">Os exemplos de [ Blazor formulário](xref:blazor/forms-validation) a seguir mostram como desvincular um manipulador de eventos no `Dispose` método:</span><span class="sxs-lookup"><span data-stu-id="4def3-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="4def3-180">Campo privado e abordagem lambda</span><span class="sxs-lookup"><span data-stu-id="4def3-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="4def3-181">Abordagem do método privado</span><span class="sxs-lookup"><span data-stu-id="4def3-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="4def3-182">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="4def3-182">Handle errors</span></span>

<span data-ttu-id="4def3-183">Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="4def3-183">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="4def3-184">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="4def3-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="4def3-185">Em um Blazor aplicativo de servidor quando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> é <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="4def3-185">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="4def3-186">Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="4def3-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="4def3-187">Se o [`OnInitialized{Async}`](#component-initialization-methods) método de ciclo de vida para inicializar o componente estiver presente, o método será executado *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="4def3-187">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="4def3-188">Quando o componente é renderizado estaticamente.</span><span class="sxs-lookup"><span data-stu-id="4def3-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="4def3-189">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="4def3-189">After the server connection has been established.</span></span>

<span data-ttu-id="4def3-190">Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="4def3-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="4def3-191">Para evitar o cenário de processamento duplo em um Blazor aplicativo de servidor:</span><span class="sxs-lookup"><span data-stu-id="4def3-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="4def3-192">Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4def3-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="4def3-193">Use o identificador durante o pré-processamento para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="4def3-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="4def3-194">Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="4def3-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="4def3-195">O código a seguir demonstra uma atualização `WeatherForecastService` em um aplicativo de servidor baseado em modelo Blazor que evita a renderização dupla:</span><span class="sxs-lookup"><span data-stu-id="4def3-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="4def3-196">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consulte <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="4def3-196">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="4def3-197">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="4def3-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="4def3-198">Trabalho em segundo plano cancelável</span><span class="sxs-lookup"><span data-stu-id="4def3-198">Cancelable background work</span></span>

<span data-ttu-id="4def3-199">Os componentes geralmente executam trabalho em segundo plano de longa execução, como fazer chamadas de rede ( <xref:System.Net.Http.HttpClient> ) e interagir com bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="4def3-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="4def3-200">É desejável interromper o trabalho em segundo plano para conservar recursos do sistema em várias situações.</span><span class="sxs-lookup"><span data-stu-id="4def3-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="4def3-201">Por exemplo, as operações assíncronas em segundo plano não são interrompidas automaticamente quando um usuário sai de um componente.</span><span class="sxs-lookup"><span data-stu-id="4def3-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="4def3-202">Outros motivos pelos quais os itens de trabalho em segundo plano podem exigir cancelamento incluem:</span><span class="sxs-lookup"><span data-stu-id="4def3-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="4def3-203">Uma tarefa em execução em segundo plano foi iniciada com dados de entrada ou parâmetros de processamento com falha.</span><span class="sxs-lookup"><span data-stu-id="4def3-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="4def3-204">O conjunto atual de itens de trabalho em segundo plano em execução deve ser substituído por um novo conjunto de itens de trabalho.</span><span class="sxs-lookup"><span data-stu-id="4def3-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="4def3-205">A prioridade das tarefas em execução no momento deve ser alterada.</span><span class="sxs-lookup"><span data-stu-id="4def3-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="4def3-206">O aplicativo deve ser desligado para reimplantá-lo no servidor.</span><span class="sxs-lookup"><span data-stu-id="4def3-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="4def3-207">Os recursos do servidor se tornam limitados, exigindo o reagendamento de itens de trabalho em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="4def3-207">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="4def3-208">Para implementar um padrão de trabalho de segundo plano cancelável em um componente:</span><span class="sxs-lookup"><span data-stu-id="4def3-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="4def3-209">Use um <xref:System.Threading.CancellationTokenSource> e <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="4def3-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="4def3-210">Na [alienação do componente](#component-disposal-with-idisposable) e a qualquer cancelamento do ponto, é desejável cancelar manualmente o token, chamar [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para sinalizar que o trabalho em segundo plano deve ser cancelado.</span><span class="sxs-lookup"><span data-stu-id="4def3-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="4def3-211">Após a chamada assíncrona retornar, chame <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> no token.</span><span class="sxs-lookup"><span data-stu-id="4def3-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="4def3-212">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4def3-212">In the following example:</span></span>

* <span data-ttu-id="4def3-213">`await Task.Delay(5000, cts.Token);`representa o trabalho em segundo plano assíncrono de execução longa.</span><span class="sxs-lookup"><span data-stu-id="4def3-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="4def3-214">`BackgroundResourceMethod`representa um método de plano de fundo de execução longa que não deve iniciar se o `Resource` for descartado antes de o método ser chamado.</span><span class="sxs-lookup"><span data-stu-id="4def3-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
