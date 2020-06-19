---
title: ASP.NET Core Blazor valores e parâmetros em cascata
author: guardrex
description: Saiba como fluir dados de um componente ancestral para componentes descendentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 70f379b3b0e48dbb340f319f3346bbbf44588740
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103570"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor valores e parâmetros em cascata

## <a name="cascading-values-and-parameters"></a>Valores e parâmetros em cascata

Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](xref:blazor/components/index#component-parameters), especialmente quando há várias camadas de componente. Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes. Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.

### <a name="theme-example"></a>Exemplo de tema

No exemplo a seguir do aplicativo de exemplo, a `ThemeInfo` classe especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.

*UIThemeClasses/ThemeInfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata. O <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente encapsula uma subárvore da hierarquia do componente e fornece um único valor para todos os componentes dentro dessa subárvore.

Por exemplo, o aplicativo de exemplo especifica informações de tema ( `ThemeInfo` ) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da `@Body` propriedade. `ButtonClass`é atribuído um valor de `btn-success` no componente layout. Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.

`CascadingValuesParametersLayout`componente

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

Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo. Os valores em cascata são associados a parâmetros em cascata por tipo.

No aplicativo de exemplo, o `CascadingValuesParametersTheme` componente associa o `ThemeInfo` valor em cascata a um parâmetro em cascata. O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.

`CascadingValuesParametersTheme`componente

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

Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> cadeia de caracteres exclusiva para cada <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente e seu [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo correspondente. No exemplo a seguir, dois <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componentes em cascata diferentes instâncias de `MyCascadingType` por nome:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
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

Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Exemplo de TabSet

Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente. Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.

O aplicativo de exemplo tem uma `ITab` interface que implementa as guias:

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

O `CascadingValuesParametersTabSet` componente usa o `TabSet` componente, que contém vários `Tab` componentes:

```razor
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
```

Os `Tab` componentes filho não são passados explicitamente como parâmetros para `TabSet` . Em vez disso, os `Tab` componentes filho fazem parte do conteúdo filho do `TabSet` . No entanto, o `TabSet` ainda precisa saber sobre cada `Tab` componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o `TabSet` componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes descendentes `Tab` .

`TabSet`componente

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Os componentes descendentes `Tab` capturam o que contém `TabSet` como um parâmetro em cascata, de modo que os `Tab` componentes se adicionam à `TabSet` coordenada e na qual a guia está ativa.

`Tab`componente

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]