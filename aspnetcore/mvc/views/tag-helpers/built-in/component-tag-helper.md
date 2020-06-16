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
ms.openlocfilehash: b6e3890e13ef5ad20098d3907b6895046087aeca
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776495"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Auxiliar de marca de componente no ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Pré-requisitos

Siga as orientações na seção *preparar o aplicativo para usar componentes em páginas e exibições* do <xref:blazor/integrate-components#prepare-the-app> artigo.

## <a name="component-tag-helper"></a>Auxiliar de marca de componente

O auxiliar de marca de componente a seguir renderiza o `Counter` componente em uma página ou exibição:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

O exemplo anterior pressupõe que o `Counter` componente está na pasta *páginas* do aplicativo. O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample.Pages` ).

O auxiliar de marca de componente também pode passar parâmetros para componentes. Considere o seguinte `ColorfulCheckbox` componente que define a cor e o tamanho do rótulo da caixa de seleção:

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

Os `Size` `int` parâmetros de componente () e `Color` ( `string` ) podem ser definidos pelo auxiliar de marca do componente: [component parameters](xref:blazor/components#component-parameters)

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

O exemplo anterior pressupõe que o `ColorfulCheckbox` componente está na pasta *compartilhada* do aplicativo. O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample.Shared` ).

O HTML a seguir é renderizado na página ou exibição:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

A passagem de uma cadeia de caracteres entre aspas requer uma [ Razor expressão explícita](xref:mvc/views/razor#explicit-razor-expressions), conforme mostrado `param-Color` no exemplo anterior. O Razor comportamento de análise para um `string` valor de tipo não se aplica a um `param-*` atributo porque o atributo é um `object` tipo.

O tipo de parâmetro deve ser serializável em JSON, o que normalmente significa que o tipo deve ter propriedades de construtor e settable padrão. Por exemplo, você pode especificar um valor para `Size` e `Color` no exemplo anterior, porque os tipos de `Size` e `Color` são tipos primitivos ( `int` e `string` ), que são suportados pelo serializador JSON.

No exemplo a seguir, um objeto de classe é passado para o componente:

*MyClass.cs*:

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

**A classe deve ter um construtor público sem parâmetros.**

*Compartilhado/myComponent. Razor*:

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

*Páginas/mypage. cshtml*:

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

O exemplo anterior pressupõe que o `MyComponent` componente está na pasta *compartilhada* do aplicativo. O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `@using BlazorSample` e `@using BlazorSample.Shared` ). `MyClass`está no namespace do aplicativo.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.

| Modo de renderização | Descrição |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor aplicativo de servidor. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o componente em HTML estático. |

Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro. Os componentes não podem usar recursos de exibição e específicos de página, como exibições parciais e seções. Para usar a lógica de uma exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
