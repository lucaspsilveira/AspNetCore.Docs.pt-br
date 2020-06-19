---
title: Auxiliar de marca de componente no ASP.NET Core
author: guardrex
ms.author: riande
description: Saiba como usar o auxiliar de marca de componente ASP.NET Core para renderizar Razor componentes em páginas e exibições.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: df978d49201ba1010ddf13b1b9a63ae27116616e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103090"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="544a4-103">Auxiliar de marca de componente no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="544a4-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="544a4-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="544a4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="544a4-105">Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="544a4-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="544a4-106">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="544a4-106">Prerequisites</span></span>

<span data-ttu-id="544a4-107">Siga as orientações na seção *preparar o aplicativo para usar componentes em páginas e exibições* do <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> artigo.</span><span class="sxs-lookup"><span data-stu-id="544a4-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="544a4-108">Auxiliar de marca de componente</span><span class="sxs-lookup"><span data-stu-id="544a4-108">Component Tag Helper</span></span>

<span data-ttu-id="544a4-109">O auxiliar de marca de componente a seguir renderiza o `Counter` componente em uma página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="544a4-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="544a4-110">O exemplo anterior pressupõe que o `Counter` componente está na pasta *páginas* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="544a4-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="544a4-111">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="544a4-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages`).</span></span>

<span data-ttu-id="544a4-112">O auxiliar de marca de componente também pode passar parâmetros para componentes.</span><span class="sxs-lookup"><span data-stu-id="544a4-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="544a4-113">Considere o seguinte `ColorfulCheckbox` componente que define a cor e o tamanho do rótulo da caixa de seleção:</span><span class="sxs-lookup"><span data-stu-id="544a4-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="544a4-114">Os `Size` `int` parâmetros de componente () e `Color` ( `string` ) podem ser definidos pelo auxiliar de marca do componente: [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="544a4-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="544a4-115">O exemplo anterior pressupõe que o `ColorfulCheckbox` componente está na pasta *compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="544a4-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="544a4-116">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="544a4-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="544a4-117">O HTML a seguir é renderizado na página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="544a4-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="544a4-118">A passagem de uma cadeia de caracteres entre aspas requer uma [ Razor expressão explícita](xref:mvc/views/razor#explicit-razor-expressions), conforme mostrado `param-Color` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="544a4-118">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="544a4-119">O Razor comportamento de análise para um `string` valor de tipo não se aplica a um `param-*` atributo porque o atributo é um `object` tipo.</span><span class="sxs-lookup"><span data-stu-id="544a4-119">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="544a4-120">O tipo de parâmetro deve ser serializável em JSON, o que normalmente significa que o tipo deve ter propriedades de construtor e settable padrão.</span><span class="sxs-lookup"><span data-stu-id="544a4-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="544a4-121">Por exemplo, você pode especificar um valor para `Size` e `Color` no exemplo anterior, porque os tipos de `Size` e `Color` são tipos primitivos ( `int` e `string` ), que são suportados pelo serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="544a4-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="544a4-122">No exemplo a seguir, um objeto de classe é passado para o componente:</span><span class="sxs-lookup"><span data-stu-id="544a4-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="544a4-123">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="544a4-123">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="544a4-124">**A classe deve ter um construtor público sem parâmetros.**</span><span class="sxs-lookup"><span data-stu-id="544a4-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="544a4-125">*Compartilhado/myComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="544a4-125">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="544a4-126">*Páginas/mypage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="544a4-126">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="544a4-127">O exemplo anterior pressupõe que o `MyComponent` componente está na pasta *compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="544a4-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="544a4-128">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample` e `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="544a4-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="544a4-129">`MyClass`está no namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="544a4-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="544a4-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="544a4-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="544a4-131">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="544a4-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="544a4-132">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="544a4-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="544a4-133">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="544a4-133">Render Mode</span></span> | <span data-ttu-id="544a4-134">Descrição</span><span class="sxs-lookup"><span data-stu-id="544a4-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="544a4-135">Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="544a4-135">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="544a4-136">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="544a4-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="544a4-137">Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="544a4-137">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="544a4-138">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="544a4-138">Output from the component isn't included.</span></span> <span data-ttu-id="544a4-139">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="544a4-139">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="544a4-140">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="544a4-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="544a4-141">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="544a4-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="544a4-142">Os componentes não podem usar recursos de exibição e específicos de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="544a4-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="544a4-143">Para usar a lógica de uma exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="544a4-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="544a4-144">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="544a4-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="544a4-145">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="544a4-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
