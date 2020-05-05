---
title: ASP.NET Core Blazor Associação de dados
author: guardrex
description: Saiba mais sobre os recursos de associação de dados para componentes Blazor e elementos DOM em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 73e73869d58e4a22e9dbee059f69fd15605ce2ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767545"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="382db-103">ASP.NET Core Blazor Associação de dados</span><span class="sxs-lookup"><span data-stu-id="382db-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="382db-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="382db-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="382db-105">os componentes fornecem recursos de vinculação de dados por meio de [`@bind`](xref:mvc/views/razor#bind) um atributo de elemento HTML chamado com Razor um campo, propriedade ou valor de expressão.</span><span class="sxs-lookup"><span data-stu-id="382db-105"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="382db-106">O exemplo a seguir associa a `CurrentValue` Propriedade ao valor da caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="382db-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="382db-107">Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.</span><span class="sxs-lookup"><span data-stu-id="382db-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="382db-108">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="382db-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="382db-109">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="382db-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="382db-110">O `@bind` uso da `CurrentValue` Propriedade (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="382db-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="382db-111">Quando o componente é renderizado, `value` o do elemento de entrada vem da `CurrentValue` propriedade.</span><span class="sxs-lookup"><span data-stu-id="382db-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="382db-112">Quando o usuário digita na caixa de texto e altera o foco do elemento `onchange` , o evento é acionado e a `CurrentValue` propriedade é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="382db-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="382db-113">Na realidade, a geração de código é mais complexa `@bind` porque lida com casos em que conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="382db-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="382db-114">Em princípio, `@bind` associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="382db-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="382db-115">Associe uma propriedade ou um campo a outros eventos, incluindo também `@bind:event` um atributo com `event` um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="382db-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="382db-116">O exemplo a seguir associa a `CurrentValue` Propriedade no `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="382db-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="382db-117">Ao `onchange`contrário de, que é acionado quando o `oninput` elemento perde o foco, é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="382db-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="382db-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` sintaxe para associar atributos de elemento diferentes `value`de.</span><span class="sxs-lookup"><span data-stu-id="382db-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="382db-119">No exemplo a seguir, o estilo do parágrafo é atualizado quando o `_paragraphStyle` valor é alterado:</span><span class="sxs-lookup"><span data-stu-id="382db-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

<span data-ttu-id="382db-120">A associação de atributo diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="382db-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="382db-121">Por exemplo, `@bind` é válido e `@Bind` é inválido.</span><span class="sxs-lookup"><span data-stu-id="382db-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="382db-122">Valores não analisáveis</span><span class="sxs-lookup"><span data-stu-id="382db-122">Unparsable values</span></span>

<span data-ttu-id="382db-123">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="382db-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="382db-124">Considere o cenário a seguir.</span><span class="sxs-lookup"><span data-stu-id="382db-124">Consider the following scenario:</span></span>

* <span data-ttu-id="382db-125">Um `<input>` elemento está associado a um `int` tipo com um valor inicial de `123`:</span><span class="sxs-lookup"><span data-stu-id="382db-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="382db-126">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="382db-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="382db-127">No cenário anterior, o valor do elemento é revertido para `123`.</span><span class="sxs-lookup"><span data-stu-id="382db-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="382db-128">Quando o valor `123.45` é rejeitado em favor do valor original de `123`, o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="382db-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="382db-129">Por padrão, a associação aplica-se ao `onchange` evento do`@bind="{PROPERTY OR FIELD}"`elemento ().</span><span class="sxs-lookup"><span data-stu-id="382db-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="382db-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="382db-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="382db-131">Para o `oninput` evento (`@bind:event="oninput"`), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="382db-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="382db-132">Ao direcionar `oninput` o evento com `int`um tipo associado, um usuário é impedido de digitar `.` um caractere.</span><span class="sxs-lookup"><span data-stu-id="382db-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="382db-133">Um `.` caractere é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="382db-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="382db-134">Há cenários em que a reversão do valor no `oninput` evento não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um `<input>` valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="382db-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="382db-135">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="382db-135">Alternatives include:</span></span>

* <span data-ttu-id="382db-136">Não use o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="382db-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="382db-137">Use o evento `onchange` padrão (especifique `@bind="{PROPERTY OR FIELD}"`apenas), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="382db-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="382db-138">Associar a um tipo anulável, como `int?` ou `string`, e fornecer lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="382db-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="382db-139">Use um [componente de validação de formulário](xref:blazor/forms-validation), `InputNumber` como `InputDate`ou.</span><span class="sxs-lookup"><span data-stu-id="382db-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="382db-140">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="382db-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="382db-141">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="382db-141">Form validation components:</span></span>
  * <span data-ttu-id="382db-142">Permitir que o usuário forneça erros de entrada e de validação inválidos `EditContext`no associado.</span><span class="sxs-lookup"><span data-stu-id="382db-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="382db-143">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="382db-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="382db-144">Formatar cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="382db-144">Format strings</span></span>

<span data-ttu-id="382db-145">A vinculação de dados <xref:System.DateTime> funciona com cadeias de caracteres de formato usando [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="382db-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="382db-146">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="382db-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="382db-147">No código anterior, o `<input>` tipo de campo do elemento (`type`) usa como `text`padrão.</span><span class="sxs-lookup"><span data-stu-id="382db-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="382db-148">`@bind:format`tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="382db-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="382db-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="382db-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="382db-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="382db-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="382db-151">O `@bind:format` atributo especifica o formato de data a ser aplicado `value` ao do `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="382db-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="382db-152">O formato também é usado para analisar o valor quando ocorre `onchange` um evento.</span><span class="sxs-lookup"><span data-stu-id="382db-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="382db-153">Não é recomendável especificar `date` um formato para o tipo Blazor de campo porque o tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="382db-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="382db-154">Apesar da recomendação, use apenas o `yyyy-MM-dd` formato de data para que a associação funcione corretamente se um formato for fornecido com o `date` tipo de campo:</span><span class="sxs-lookup"><span data-stu-id="382db-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="382db-155">Associação de pai para filho com parâmetros de componente</span><span class="sxs-lookup"><span data-stu-id="382db-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="382db-156">A associação reconhece os parâmetros do `@bind-{PROPERTY}` componente, onde pode associar um valor de propriedade de um componente pai a um componente filho.</span><span class="sxs-lookup"><span data-stu-id="382db-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="382db-157">A associação de um filho a um pai é abordada na [Associação filho para pai com a seção de ligação encadeada](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="382db-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="382db-158">O componente filho a seguir`ChildComponent`() tem `Year` um parâmetro de `YearChanged` componente e um retorno de chamada:</span><span class="sxs-lookup"><span data-stu-id="382db-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="382db-159">`EventCallback<T>`é explicado <xref:blazor/event-handling#eventcallback>em.</span><span class="sxs-lookup"><span data-stu-id="382db-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="382db-160">O seguinte componente pai usa:</span><span class="sxs-lookup"><span data-stu-id="382db-160">The following parent component uses:</span></span>

* <span data-ttu-id="382db-161">`ChildComponent`e associa o `ParentYear` parâmetro do pai ao `Year` parâmetro no componente filho.</span><span class="sxs-lookup"><span data-stu-id="382db-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="382db-162">O `onclick` evento é usado para disparar o `ChangeTheYear` método.</span><span class="sxs-lookup"><span data-stu-id="382db-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="382db-163">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="382db-163">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="382db-164">O carregamento `ParentComponent` do produz a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="382db-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="382db-165">Se o valor `ParentYear` da propriedade for alterado selecionando o botão no `ParentComponent`, a `Year` Propriedade do `ChildComponent` será atualizada.</span><span class="sxs-lookup"><span data-stu-id="382db-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="382db-166">O novo valor de `Year` é renderizado na interface do usuário `ParentComponent` quando o é rerenderizado:</span><span class="sxs-lookup"><span data-stu-id="382db-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="382db-167">O `Year` parâmetro é ligável porque tem um evento `YearChanged` complementar que corresponde ao tipo do `Year` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="382db-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="382db-168">Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="382db-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="382db-169">Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo `@bind-{PROPRETY}:event` um atributo.</span><span class="sxs-lookup"><span data-stu-id="382db-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="382db-170">Por exemplo, a propriedade `MyProp` pode ser associada ao `MyEventHandler` uso dos dois atributos a seguir:</span><span class="sxs-lookup"><span data-stu-id="382db-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="382db-171">Associação de filho para pai com associação encadeada</span><span class="sxs-lookup"><span data-stu-id="382db-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="382db-172">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="382db-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="382db-173">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="382db-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="382db-174">Uma associação encadeada não pode ser implementada com `@bind` a sintaxe no elemento da página.</span><span class="sxs-lookup"><span data-stu-id="382db-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="382db-175">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="382db-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="382db-176">Um componente pai, no entanto, `@bind` pode usar a sintaxe com o parâmetro do componente.</span><span class="sxs-lookup"><span data-stu-id="382db-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="382db-177">O seguinte `PasswordField` componente (*passwordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="382db-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="382db-178">Define o `<input>` valor de um elemento para `Password` uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="382db-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="382db-179">Expõe as alterações da `Password` propriedade para um componente pai com um [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="382db-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="382db-180">Usa o `onclick` evento é usado para disparar o `ToggleShowPassword` método.</span><span class="sxs-lookup"><span data-stu-id="382db-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="382db-181">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="382db-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="382db-182">O `PasswordField` componente é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="382db-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="382db-183">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="382db-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="382db-184">Crie um campo de backup para `Password` (`_password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="382db-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="382db-185">Execute os erros de verificação ou interceptação `Password` no setter.</span><span class="sxs-lookup"><span data-stu-id="382db-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="382db-186">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="382db-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="382db-187">Botões de opção</span><span class="sxs-lookup"><span data-stu-id="382db-187">Radio buttons</span></span>

<span data-ttu-id="382db-188">Para obter informações sobre a associação a botões de opção em um <xref:blazor/forms-validation#work-with-radio-buttons>formulário, consulte.</span><span class="sxs-lookup"><span data-stu-id="382db-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
