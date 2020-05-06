---
title: Auxiliar de marca de componente no ASP.NET Core
author: guardrex
ms.author: riande
description: Saiba como usar o auxiliar de marca de componente ASP.NET Core para Razor renderizar componentes em páginas e exibições.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773923"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="7a4e9-103">Auxiliar de marca de componente no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a4e9-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="7a4e9-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7a4e9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7a4e9-105">Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="7a4e9-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a4e9-106">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7a4e9-106">Prerequisites</span></span>

<span data-ttu-id="7a4e9-107">Siga as orientações na seção *preparar o aplicativo para usar componentes em páginas e exibições* do <xref:blazor/integrate-components#prepare-the-app> artigo.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="7a4e9-108">Auxiliar de marca de componente</span><span class="sxs-lookup"><span data-stu-id="7a4e9-108">Component Tag Helper</span></span>

<span data-ttu-id="7a4e9-109">O auxiliar de marca de componente a seguir `Counter` renderiza o componente em uma página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="7a4e9-110">O exemplo anterior pressupõe que o `Counter` componente está na pasta *páginas* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="7a4e9-111">O auxiliar de marca de componente também pode passar parâmetros para componentes.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="7a4e9-112">Considere o seguinte `ColorfulCheckbox` componente que define a cor e o tamanho do rótulo da caixa de seleção:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="7a4e9-113">Os `Size` parâmetros`int`de `Color` [componente](xref:blazor/components#component-parameters) (`string`) e () podem ser definidos pelo auxiliar de marca do componente:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="7a4e9-114">O exemplo anterior pressupõe que o `ColorfulCheckbox` componente está na pasta *compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="7a4e9-115">O HTML a seguir é renderizado na página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="7a4e9-116">A passagem de uma cadeia de caracteres entre aspas requer uma [expressão explícita do Razor](xref:mvc/views/razor#explicit-razor-expressions), conforme mostrado `param-Color` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="7a4e9-117">O comportamento de análise do Razor para `string` um valor de tipo não se `param-*` aplica a um atributo porque o `object` atributo é um tipo.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="7a4e9-118">O tipo de parâmetro deve ser serializável em JSON, o que normalmente significa que o tipo deve ter propriedades de construtor e settable padrão.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="7a4e9-119">Por exemplo, você pode especificar um valor para `Size` e `Color` no exemplo anterior, porque os tipos de `Size` e `Color` são tipos primitivos`int` ( `string`e), que são suportados pelo serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="7a4e9-120">No exemplo a seguir, um objeto de classe é passado para o componente:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="7a4e9-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="7a4e9-122">**A classe deve ter um construtor público sem parâmetros.**</span><span class="sxs-lookup"><span data-stu-id="7a4e9-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="7a4e9-123">*Compartilhado/myComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="7a4e9-124">*Páginas/mypage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="7a4e9-125">O exemplo anterior pressupõe que o `MyComponent` componente está na pasta *compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="7a4e9-126">`MyClass`está no namespace do aplicativo (`{APP ASSEMBLY}`).</span><span class="sxs-lookup"><span data-stu-id="7a4e9-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="7a4e9-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="7a4e9-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="7a4e9-128">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="7a4e9-129">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="7a4e9-130">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="7a4e9-130">Render Mode</span></span> | <span data-ttu-id="7a4e9-131">Descrição</span><span class="sxs-lookup"><span data-stu-id="7a4e9-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="7a4e9-132">Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="7a4e9-133">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="7a4e9-134">Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="7a4e9-135">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-135">Output from the component isn't included.</span></span> <span data-ttu-id="7a4e9-136">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="7a4e9-137">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="7a4e9-138">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="7a4e9-139">Os componentes não podem usar recursos de exibição e específicos de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="7a4e9-140">Para usar a lógica de uma exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="7a4e9-141">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="7a4e9-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a4e9-142">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7a4e9-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
