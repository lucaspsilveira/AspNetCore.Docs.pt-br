---
title: ASP.NET Core Blazor valores e parâmetros em cascata
author: guardrex
description: Saiba como fluir dados de um componente ancestral para componentes descendentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: c426be21b520520c6745ada95be35816f7365c21
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059923"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="8fe61-103">ASP.NET Core Blazor valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="8fe61-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="8fe61-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8fe61-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8fe61-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8fe61-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8fe61-106">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](xref:blazor/components/index#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="8fe61-106">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="8fe61-107">Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="8fe61-107">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="8fe61-108">Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.</span><span class="sxs-lookup"><span data-stu-id="8fe61-108">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="8fe61-109">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="8fe61-109">Theme example</span></span>

<span data-ttu-id="8fe61-110">No exemplo a seguir do aplicativo de exemplo, a `ThemeInfo` classe especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="8fe61-110">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="8fe61-111">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="8fe61-111">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="8fe61-112">Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="8fe61-112">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="8fe61-113">O <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente encapsula uma subárvore da hierarquia do componente e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="8fe61-113">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="8fe61-114">Por exemplo, o aplicativo de exemplo especifica informações de tema ( `ThemeInfo` ) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da `@Body` propriedade.</span><span class="sxs-lookup"><span data-stu-id="8fe61-114">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="8fe61-115">`ButtonClass`é atribuído um valor de `btn-success` no componente layout.</span><span class="sxs-lookup"><span data-stu-id="8fe61-115">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="8fe61-116">Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="8fe61-116">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="8fe61-117">`CascadingValuesParametersLayout`componente</span><span class="sxs-lookup"><span data-stu-id="8fe61-117">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="8fe61-118">Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="8fe61-118">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="8fe61-119">Os valores em cascata são associados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="8fe61-119">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="8fe61-120">No aplicativo de exemplo, o `CascadingValuesParametersTheme` componente associa o `ThemeInfo` valor em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="8fe61-120">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="8fe61-121">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="8fe61-121">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="8fe61-122">`CascadingValuesParametersTheme`componente</span><span class="sxs-lookup"><span data-stu-id="8fe61-122">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="8fe61-123">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> cadeia de caracteres exclusiva para cada <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente e seu [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo correspondente.</span><span class="sxs-lookup"><span data-stu-id="8fe61-123">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="8fe61-124">No exemplo a seguir, dois <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componentes em cascata diferentes instâncias de `MyCascadingType` por nome:</span><span class="sxs-lookup"><span data-stu-id="8fe61-124">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="8fe61-125">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="8fe61-125">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="8fe61-126">Exemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="8fe61-126">TabSet example</span></span>

<span data-ttu-id="8fe61-127">Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="8fe61-127">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="8fe61-128">Por exemplo, considere o exemplo a seguir `TabSet` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8fe61-128">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="8fe61-129">O aplicativo de exemplo tem uma `ITab` interface que implementa as guias:</span><span class="sxs-lookup"><span data-stu-id="8fe61-129">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="8fe61-130">O `CascadingValuesParametersTabSet` componente usa o `TabSet` componente, que contém vários `Tab` componentes:</span><span class="sxs-lookup"><span data-stu-id="8fe61-130">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```

<span data-ttu-id="8fe61-131">Os `Tab` componentes filho não são passados explicitamente como parâmetros para `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="8fe61-131">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="8fe61-132">Em vez disso, os `Tab` componentes filho fazem parte do conteúdo filho do `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="8fe61-132">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="8fe61-133">No entanto, o `TabSet` ainda precisa saber sobre cada `Tab` componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o `TabSet` componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes descendentes `Tab` .</span><span class="sxs-lookup"><span data-stu-id="8fe61-133">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="8fe61-134">`TabSet`componente</span><span class="sxs-lookup"><span data-stu-id="8fe61-134">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="8fe61-135">Os componentes descendentes `Tab` capturam o que contém `TabSet` como um parâmetro em cascata, de modo que os `Tab` componentes se adicionam à `TabSet` coordenada e na qual a guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="8fe61-135">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="8fe61-136">`Tab`componente</span><span class="sxs-lookup"><span data-stu-id="8fe61-136">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
