---
title: Ajudante de tag componente no núcleo ASP.NET
author: guardrex
ms.author: riande
description: Aprenda a usar o ASP.NET Core Component Tag Helper para renderizar componentes razor em páginas e visualizações.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123422"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="4e9d1-103">Ajudante de tag componente no núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="4e9d1-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="4e9d1-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4e9d1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4e9d1-105">Para renderizar um componente de uma página ou exibição, use o [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="4e9d1-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4e9d1-106">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4e9d1-106">Prerequisites</span></span>

<span data-ttu-id="4e9d1-107">Siga as orientações no *Prepare o aplicativo para usar componentes na* seção páginas e visualizações do <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> artigo.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="4e9d1-108">Ajudante de tag componente</span><span class="sxs-lookup"><span data-stu-id="4e9d1-108">Component Tag Helper</span></span>

<span data-ttu-id="4e9d1-109">O seguinte Component Tag Helper renderiza o `Counter` componente em uma página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="4e9d1-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="4e9d1-110">O exemplo anterior assume `Counter` que o componente está na pasta *Páginas* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="4e9d1-111">O Component Tag Helper também pode passar parâmetros para os componentes.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="4e9d1-112">Considere o `ColorfulCheckbox` seguinte componente que define a cor e o tamanho da etiqueta da caixa de seleção:</span><span class="sxs-lookup"><span data-stu-id="4e9d1-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="4e9d1-113">Os `Size` `int`parâmetros `Color` `string`dos componentes ( ) e ( ) podem ser [definidos](xref:blazor/components#component-parameters) pelo Component Tag Helper:</span><span class="sxs-lookup"><span data-stu-id="4e9d1-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="4e9d1-114">O exemplo anterior assume `ColorfulCheckbox` que o componente está na pasta *Compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="4e9d1-115">O HTML a seguir é renderizado na página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="4e9d1-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="4e9d1-116">Passar uma seqüência citada requer uma `param-Color` expressão de [Navalha explícita,](xref:mvc/views/razor#explicit-razor-expressions)como mostrado no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="4e9d1-117">O comportamento de análise `string` de Lâminas para um `param-*` valor de tipo `object` não se aplica a um atributo porque o atributo é um tipo.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="4e9d1-118">O tipo de parâmetro deve ser serializável JSON, o que normalmente significa que o tipo deve ter um construtor padrão e propriedades definidas.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="4e9d1-119">Por exemplo, você pode `Size` especificar um `Color` valor para `Size` e `Color` no exemplo`int` `string`anterior porque os tipos de e são tipos primitivos (e ), que são suportados pelo serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="4e9d1-120">No exemplo a seguir, um objeto de classe é passado para o componente:</span><span class="sxs-lookup"><span data-stu-id="4e9d1-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="4e9d1-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e9d1-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="4e9d1-122">**A classe deve ter um construtor público sem parâmetros.**</span><span class="sxs-lookup"><span data-stu-id="4e9d1-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="4e9d1-123">*Compartilhado/MyComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4e9d1-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="4e9d1-124">*Páginas/MyPage.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="4e9d1-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="4e9d1-125">O exemplo anterior assume `MyComponent` que o componente está na pasta *Compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="4e9d1-126">`MyClass`está no namespace do`{APP ASSEMBLY}`aplicativo ().</span><span class="sxs-lookup"><span data-stu-id="4e9d1-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="4e9d1-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="4e9d1-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="4e9d1-128">É pré-renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="4e9d1-129">É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="4e9d1-130">Modo renderização</span><span class="sxs-lookup"><span data-stu-id="4e9d1-130">Render Mode</span></span> | <span data-ttu-id="4e9d1-131">Descrição</span><span class="sxs-lookup"><span data-stu-id="4e9d1-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4e9d1-132">Renderiza o componente em HTML estático e Blazor inclui um marcador para um aplicativo Server.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4e9d1-133">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4e9d1-134">Renderiza um marcador Blazor para um aplicativo do Servidor.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4e9d1-135">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-135">Output from the component isn't included.</span></span> <span data-ttu-id="4e9d1-136">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4e9d1-137">Torna o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4e9d1-138">Embora páginas e visualizações possam usar componentes, o inverso não é verdade.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="4e9d1-139">Os componentes não podem usar recursos específicos de exibição e página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="4e9d1-140">Para usar a lógica a partir de uma visão parcial em um componente, fatorie a lógica de visualização parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="4e9d1-141">A renderização de componentes do servidor de uma página HTML estática não é suportada.</span><span class="sxs-lookup"><span data-stu-id="4e9d1-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e9d1-142">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4e9d1-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
