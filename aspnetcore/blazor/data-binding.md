---
title: ASP.NET Blazor vinculação de dados do Núcleo
author: guardrex
description: Saiba mais sobre os recursos de vinculação de dados para componentes e elementos DOM em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320960"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="eeafc-103">ASP.NET Blazor vinculação de dados do Núcleo</span><span class="sxs-lookup"><span data-stu-id="eeafc-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="eeafc-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="eeafc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="eeafc-105">Os componentes da navalha fornecem recursos [`@bind`](xref:mvc/views/razor#bind) de vinculação de dados através de um atributo de elemento HTML nomeado com um valor de expressão de campo, propriedade ou Razor.</span><span class="sxs-lookup"><span data-stu-id="eeafc-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="eeafc-106">O exemplo a `CurrentValue` seguir vincula a propriedade ao valor da caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="eeafc-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="eeafc-107">Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.</span><span class="sxs-lookup"><span data-stu-id="eeafc-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="eeafc-108">A caixa de texto é atualizada na ui somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="eeafc-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="eeafc-109">Uma vez que os componentes se tornam após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na ui imediatamente após um manipulador de eventos ser acionado.</span><span class="sxs-lookup"><span data-stu-id="eeafc-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="eeafc-110">O `@bind` uso `CurrentValue` com`<input @bind="CurrentValue" />`a propriedade ( ) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="eeafc-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="eeafc-111">Quando o componente é `value` renderizado, o elemento `CurrentValue` de entrada vem da propriedade.</span><span class="sxs-lookup"><span data-stu-id="eeafc-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="eeafc-112">Quando o usuário digita na caixa de `onchange` texto e `CurrentValue` altera o foco do elemento, o evento é acionado e a propriedade é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="eeafc-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="eeafc-113">Na realidade, a geração de `@bind` código é mais complexa porque lida com casos em que conversões de tipo são realizadas.</span><span class="sxs-lookup"><span data-stu-id="eeafc-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="eeafc-114">Em princípio, `@bind` associa o valor atual `value` de uma expressão a um atributo e lida com alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="eeafc-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="eeafc-115">Vincule uma propriedade ou campo em `@bind:event` outros `event` eventos, incluindo também um atributo com um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="eeafc-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="eeafc-116">O exemplo a `CurrentValue` seguir vincula a propriedade no `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="eeafc-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="eeafc-117">Ao `onchange`contrário , que dispara `oninput` quando o elemento perde o foco, é acionado quando o valor da caixa de texto muda.</span><span class="sxs-lookup"><span data-stu-id="eeafc-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="eeafc-118">Use `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` com sintaxe para vincular `value`atributos de elemento que não sejam .</span><span class="sxs-lookup"><span data-stu-id="eeafc-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="eeafc-119">No exemplo a seguir, o estilo do `_paragraphStyle` parágrafo é atualizado quando o valor muda:</span><span class="sxs-lookup"><span data-stu-id="eeafc-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="eeafc-120">Vinculação de atributos é sensível ao caso.</span><span class="sxs-lookup"><span data-stu-id="eeafc-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="eeafc-121">Por exemplo, `@bind` é `@Bind` válido e é inválido.</span><span class="sxs-lookup"><span data-stu-id="eeafc-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="eeafc-122">Valores inparáveis</span><span class="sxs-lookup"><span data-stu-id="eeafc-122">Unparsable values</span></span>

<span data-ttu-id="eeafc-123">Quando um usuário fornece um valor inparável a um elemento vinculado a dados, o valor inparável é automaticamente revertido para o valor anterior quando o evento de vinculação é acionado.</span><span class="sxs-lookup"><span data-stu-id="eeafc-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="eeafc-124">Considere o cenário a seguir.</span><span class="sxs-lookup"><span data-stu-id="eeafc-124">Consider the following scenario:</span></span>

* <span data-ttu-id="eeafc-125">Um `<input>` elemento está `int` vinculado a um `123`tipo com um valor inicial de:</span><span class="sxs-lookup"><span data-stu-id="eeafc-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="eeafc-126">O usuário atualiza o valor `123.45` do elemento para a página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="eeafc-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="eeafc-127">No cenário anterior, o valor do elemento `123`é revertido para .</span><span class="sxs-lookup"><span data-stu-id="eeafc-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="eeafc-128">Quando o `123.45` valor é rejeitado em favor `123`do valor original de , o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="eeafc-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="eeafc-129">Por padrão, a vinculação se `onchange` aplica`@bind="{PROPERTY OR FIELD}"`ao evento do elemento ( ).</span><span class="sxs-lookup"><span data-stu-id="eeafc-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="eeafc-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para ativar a ligação em um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="eeafc-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="eeafc-131">Para `oninput` o`@bind:event="oninput"`evento (), a reversão ocorre após qualquer tecla que introduza um valor inparável.</span><span class="sxs-lookup"><span data-stu-id="eeafc-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="eeafc-132">Ao direcionar `oninput` o evento `int`com um tipo vinculado, um `.` usuário é impedido de digitar um caractere.</span><span class="sxs-lookup"><span data-stu-id="eeafc-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="eeafc-133">Um `.` caractere é imediatamente removido, de modo que o usuário recebe feedback imediato de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="eeafc-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="eeafc-134">Há cenários em que reverter `oninput` o valor do evento não é o ideal, como quando `<input>` o usuário deve ser autorizado a limpar um valor inparável.</span><span class="sxs-lookup"><span data-stu-id="eeafc-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="eeafc-135">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="eeafc-135">Alternatives include:</span></span>

* <span data-ttu-id="eeafc-136">Não use o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="eeafc-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="eeafc-137">Use o `onchange` evento padrão `@bind="{PROPERTY OR FIELD}"`(apenas especifique), quando um valor inválido não for revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="eeafc-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="eeafc-138">Vincule-se a um `int?` tipo `string`anulado, como ou , e forneça lógica personalizada para lidar com entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="eeafc-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="eeafc-139">Use um componente de `InputNumber` validação `InputDate` [de formulário,](xref:blazor/forms-validation)como ou .</span><span class="sxs-lookup"><span data-stu-id="eeafc-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="eeafc-140">Os componentes de validação de formulário têm suporte integrado para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="eeafc-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="eeafc-141">Componentes de validação de formulários:</span><span class="sxs-lookup"><span data-stu-id="eeafc-141">Form validation components:</span></span>
  * <span data-ttu-id="eeafc-142">Permitir que o usuário forneça entrada inválida `EditContext`e receba erros de validação no associado .</span><span class="sxs-lookup"><span data-stu-id="eeafc-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="eeafc-143">Exibir erros de validação na ui sem interferir com o usuário inserindo dados adicionais da Webform.</span><span class="sxs-lookup"><span data-stu-id="eeafc-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="eeafc-144">Strings de formato</span><span class="sxs-lookup"><span data-stu-id="eeafc-144">Format strings</span></span>

<span data-ttu-id="eeafc-145">A vinculação <xref:System.DateTime> de dados [`@bind:format`](xref:mvc/views/razor#bind)funciona com strings de formato usando .</span><span class="sxs-lookup"><span data-stu-id="eeafc-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="eeafc-146">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="eeafc-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="eeafc-147">No código anterior, `<input>` o tipo de`type`campo do `text`elemento ( ) é padrão para .</span><span class="sxs-lookup"><span data-stu-id="eeafc-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="eeafc-148">`@bind:format`é suportado para vincular os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="eeafc-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="eeafc-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="eeafc-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="eeafc-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="eeafc-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="eeafc-151">O `@bind:format` atributo especifica o formato `value` de `<input>` data a ser aplicado ao do elemento.</span><span class="sxs-lookup"><span data-stu-id="eeafc-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="eeafc-152">O formato também é usado para `onchange` analisar o valor quando um evento ocorre.</span><span class="sxs-lookup"><span data-stu-id="eeafc-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="eeafc-153">A especificação de `date` um formato para o Blazor tipo de campo não é recomendada porque tem suporte incorporado para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="eeafc-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="eeafc-154">Apesar da recomendação, `yyyy-MM-dd` use apenas o formato de data para vinculação para funcionar corretamente se um formato for fornecido com o `date` tipo de campo:</span><span class="sxs-lookup"><span data-stu-id="eeafc-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="eeafc-155">Vinculação pai-filho com parâmetros de componentes</span><span class="sxs-lookup"><span data-stu-id="eeafc-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="eeafc-156">A vinculação reconhece `@bind-{PROPERTY}` parâmetros de componentes, onde pode vincular um valor de propriedade de um componente pai a um componente filho.</span><span class="sxs-lookup"><span data-stu-id="eeafc-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="eeafc-157">A vinculação de uma criança a um pai é coberta na vinculação de filho para pai com seção [de vinculação acorrentada.](#child-to-parent-binding-with-chained-bind)</span><span class="sxs-lookup"><span data-stu-id="eeafc-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="eeafc-158">O seguinte componente`ChildComponent`filho `Year` ( ) `YearChanged` tem um parâmetro de componente e retorno de chamada:</span><span class="sxs-lookup"><span data-stu-id="eeafc-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="eeafc-159">`EventCallback<T>`é explicado <xref:blazor/event-handling#eventcallback>em .</span><span class="sxs-lookup"><span data-stu-id="eeafc-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="eeafc-160">O seguinte componente pai usa:</span><span class="sxs-lookup"><span data-stu-id="eeafc-160">The following parent component uses:</span></span>

* <span data-ttu-id="eeafc-161">`ChildComponent`e liga `ParentYear` o parâmetro do pai `Year` ao parâmetro no componente da criança.</span><span class="sxs-lookup"><span data-stu-id="eeafc-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="eeafc-162">O `onclick` evento é usado `ChangeTheYear` para ativar o método.</span><span class="sxs-lookup"><span data-stu-id="eeafc-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="eeafc-163">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="eeafc-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="eeafc-164">Carregar `ParentComponent` os produtos a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="eeafc-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="eeafc-165">Se o valor `ParentYear` da propriedade for alterado selecionando `Year` o `ChildComponent` botão no `ParentComponent`, a propriedade do é atualizado.</span><span class="sxs-lookup"><span data-stu-id="eeafc-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="eeafc-166">O novo `Year` valor é renderizado na `ParentComponent` ui quando a rerenderização é rerenderizada:</span><span class="sxs-lookup"><span data-stu-id="eeafc-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="eeafc-167">O `Year` parâmetro é vinculável porque `YearChanged` tem um evento companheiro `Year` que corresponde ao tipo do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="eeafc-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="eeafc-168">Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente à escrita:</span><span class="sxs-lookup"><span data-stu-id="eeafc-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="eeafc-169">Em geral, uma propriedade pode ser vinculada a `@bind-{PROPRETY}:event` um manipulador de eventos correspondente, incluindo um atributo.</span><span class="sxs-lookup"><span data-stu-id="eeafc-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="eeafc-170">Por exemplo, `MyProp` a propriedade `MyEventHandler` pode ser vinculada ao uso dos dois atributos a seguir:</span><span class="sxs-lookup"><span data-stu-id="eeafc-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="eeafc-171">Vinculação de filho para pai com vínculo acorrentado</span><span class="sxs-lookup"><span data-stu-id="eeafc-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="eeafc-172">Um cenário comum é acorrentar um parâmetro vinculado a dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="eeafc-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="eeafc-173">Este cenário é chamado de *vínculo acorrentado* porque vários níveis de ligação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="eeafc-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="eeafc-174">Uma ligação encadeada `@bind` não pode ser implementada com sintaxe no elemento da página.</span><span class="sxs-lookup"><span data-stu-id="eeafc-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="eeafc-175">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="eeafc-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="eeafc-176">Um componente pai, no `@bind` entanto, pode usar sintaxe com o parâmetro do componente.</span><span class="sxs-lookup"><span data-stu-id="eeafc-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="eeafc-177">O `PasswordField` seguinte componente *(PasswordField.razor):*</span><span class="sxs-lookup"><span data-stu-id="eeafc-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="eeafc-178">Define `<input>` o valor de `Password` um elemento para uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="eeafc-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="eeafc-179">Expõe alterações `Password` da propriedade a um componente pai com um [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="eeafc-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="eeafc-180">O `onclick` uso do evento `ToggleShowPassword` é usado para ativar o método.</span><span class="sxs-lookup"><span data-stu-id="eeafc-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="eeafc-181">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="eeafc-181">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="eeafc-182">O `PasswordField` componente é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="eeafc-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="eeafc-183">Para executar verificações ou prender erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="eeafc-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="eeafc-184">Crie um campo `Password` `_password` de apoio para (no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="eeafc-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="eeafc-185">Execute as verificações ou `Password` erros de armadilha no setter.</span><span class="sxs-lookup"><span data-stu-id="eeafc-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="eeafc-186">O exemplo a seguir fornece feedback imediato ao usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="eeafc-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="eeafc-187">Botões de opção</span><span class="sxs-lookup"><span data-stu-id="eeafc-187">Radio buttons</span></span>

<span data-ttu-id="eeafc-188">Para obter informações sobre a vinculação <xref:blazor/forms-validation#work-with-radio-buttons>a botões de rádio em um formulário, consulte .</span><span class="sxs-lookup"><span data-stu-id="eeafc-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
