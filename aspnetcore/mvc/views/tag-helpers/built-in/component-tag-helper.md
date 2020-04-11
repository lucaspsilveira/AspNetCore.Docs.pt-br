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
# <a name="component-tag-helper-in-aspnet-core"></a>Ajudante de tag componente no núcleo ASP.NET

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Para renderizar um componente de uma página ou exibição, use o [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Pré-requisitos

Siga as orientações no *Prepare o aplicativo para usar componentes na* seção páginas e visualizações do <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> artigo.

## <a name="component-tag-helper"></a>Ajudante de tag componente

O seguinte Component Tag Helper renderiza o `Counter` componente em uma página ou exibição:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

O exemplo anterior assume `Counter` que o componente está na pasta *Páginas* do aplicativo.

O Component Tag Helper também pode passar parâmetros para os componentes. Considere o `ColorfulCheckbox` seguinte componente que define a cor e o tamanho da etiqueta da caixa de seleção:

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

Os `Size` `int`parâmetros `Color` `string`dos componentes ( ) e ( ) podem ser [definidos](xref:blazor/components#component-parameters) pelo Component Tag Helper:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

O exemplo anterior assume `ColorfulCheckbox` que o componente está na pasta *Compartilhada* do aplicativo.

O HTML a seguir é renderizado na página ou exibição:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Passar uma seqüência citada requer uma `param-Color` expressão de [Navalha explícita,](xref:mvc/views/razor#explicit-razor-expressions)como mostrado no exemplo anterior. O comportamento de análise `string` de Lâminas para um `param-*` valor de tipo `object` não se aplica a um atributo porque o atributo é um tipo.

O tipo de parâmetro deve ser serializável JSON, o que normalmente significa que o tipo deve ter um construtor padrão e propriedades definidas. Por exemplo, você pode `Size` especificar um `Color` valor para `Size` e `Color` no exemplo`int` `string`anterior porque os tipos de e são tipos primitivos (e ), que são suportados pelo serializador JSON.

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

*Compartilhado/MyComponent.razor:*

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

*Páginas/MyPage.cshtml:*

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

O exemplo anterior assume `MyComponent` que o componente está na pasta *Compartilhada* do aplicativo. `MyClass`está no namespace do`{APP ASSEMBLY}`aplicativo ().

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o componente:

* É pré-renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.

| Modo renderização | Descrição |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o componente em HTML estático e Blazor inclui um marcador para um aplicativo Server. Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador Blazor para um aplicativo do Servidor. A saída do componente não está incluída. Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Torna o componente em HTML estático. |

Embora páginas e visualizações possam usar componentes, o inverso não é verdade. Os componentes não podem usar recursos específicos de exibição e página, como exibições parciais e seções. Para usar a lógica a partir de uma visão parcial em um componente, fatorie a lógica de visualização parcial em um componente.

A renderização de componentes do servidor de uma página HTML estática não é suportada.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
