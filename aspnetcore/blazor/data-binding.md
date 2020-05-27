---
<span data-ttu-id="ede81-101">Título: ' ASP.NET Core Blazor Associação de dados ' autor: Descrição: ' saiba mais sobre os recursos de associação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="ede81-101">title: 'ASP.NET Core Blazor data binding' author: description: 'Learn about data binding features for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="ede81-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ede81-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ede81-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ede81-103">'Blazor'</span></span>
- <span data-ttu-id="ede81-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ede81-104">'Identity'</span></span>
- <span data-ttu-id="ede81-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ede81-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ede81-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ede81-106">'Razor'</span></span>
- <span data-ttu-id="ede81-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="ede81-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="ede81-108">ASP.NET Core Blazor Associação de dados</span><span class="sxs-lookup"><span data-stu-id="ede81-108">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="ede81-109">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ede81-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="ede81-110">os componentes fornecem recursos de vinculação de dados por meio de um atributo de elemento HTML chamado [`@bind`](xref:mvc/views/razor#bind) com um campo, propriedade ou Razor valor de expressão.</span><span class="sxs-lookup"><span data-stu-id="ede81-110"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="ede81-111">O exemplo a seguir associa a `CurrentValue` propriedade ao valor da caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="ede81-111">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="ede81-112">Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.</span><span class="sxs-lookup"><span data-stu-id="ede81-112">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="ede81-113">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="ede81-113">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="ede81-114">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="ede81-114">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="ede81-115">[`@bind`](xref:mvc/views/razor#bind)O uso da `CurrentValue` Propriedade ( `<input @bind="CurrentValue" />` ) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="ede81-115">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="ede81-116">Quando o componente é renderizado, o `value` do elemento de entrada vem da `CurrentValue` propriedade.</span><span class="sxs-lookup"><span data-stu-id="ede81-116">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="ede81-117">Quando o usuário digita na caixa de texto e altera o foco do elemento, o `onchange` evento é acionado e a `CurrentValue` propriedade é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="ede81-117">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="ede81-118">Na realidade, a geração de código é mais complexa porque [`@bind`](xref:mvc/views/razor#bind) lida com casos em que conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="ede81-118">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="ede81-119">Em princípio, [`@bind`](xref:mvc/views/razor#bind) associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="ede81-119">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="ede81-120">Associe uma propriedade ou um campo a outros eventos, incluindo também um `@bind:event` atributo com um `event` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="ede81-120">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="ede81-121">O exemplo a seguir associa a `CurrentValue` propriedade no `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="ede81-121">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="ede81-122">Ao contrário de `onchange` , que é acionado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="ede81-122">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="ede81-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` sintaxe para associar atributos de elemento diferentes de `value` .</span><span class="sxs-lookup"><span data-stu-id="ede81-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="ede81-124">No exemplo a seguir, o estilo do parágrafo é atualizado quando o `paragraphStyle` valor é alterado:</span><span class="sxs-lookup"><span data-stu-id="ede81-124">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="ede81-125">A associação de atributo diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ede81-125">Attribute binding is case sensitive.</span></span> <span data-ttu-id="ede81-126">Por exemplo, [`@bind`](xref:mvc/views/razor#bind) é válido e [`@bind`](xref:mvc/views/razor#bind) é inválido.</span><span class="sxs-lookup"><span data-stu-id="ede81-126">For example, [`@bind`](xref:mvc/views/razor#bind) is valid, and [`@bind`](xref:mvc/views/razor#bind) is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="ede81-127">Valores não analisáveis</span><span class="sxs-lookup"><span data-stu-id="ede81-127">Unparsable values</span></span>

<span data-ttu-id="ede81-128">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="ede81-128">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="ede81-129">Considere o cenário a seguir.</span><span class="sxs-lookup"><span data-stu-id="ede81-129">Consider the following scenario:</span></span>

* <span data-ttu-id="ede81-130">Um `<input>` elemento está associado a um `int` tipo com um valor inicial de `123` :</span><span class="sxs-lookup"><span data-stu-id="ede81-130">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="ede81-131">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="ede81-131">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="ede81-132">No cenário anterior, o valor do elemento é revertido para `123` .</span><span class="sxs-lookup"><span data-stu-id="ede81-132">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="ede81-133">Quando o valor `123.45` é rejeitado em favor do valor original de `123` , o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="ede81-133">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="ede81-134">Por padrão, a associação aplica-se ao `onchange` evento do elemento ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="ede81-134">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="ede81-135">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="ede81-135">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="ede81-136">Para o `oninput` evento ( `@bind:event="oninput"` ), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="ede81-136">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="ede81-137">Ao direcionar o `oninput` evento com um `int` tipo associado, um usuário é impedido de digitar um `.` caractere.</span><span class="sxs-lookup"><span data-stu-id="ede81-137">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="ede81-138">Um `.` caractere é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="ede81-138">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="ede81-139">Há cenários em que a reversão do valor no `oninput` evento não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor não analisável `<input>` .</span><span class="sxs-lookup"><span data-stu-id="ede81-139">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="ede81-140">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="ede81-140">Alternatives include:</span></span>

* <span data-ttu-id="ede81-141">Não use o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="ede81-141">Don't use the `oninput` event.</span></span> <span data-ttu-id="ede81-142">Use o `onchange` evento padrão (especifique apenas `@bind="{PROPERTY OR FIELD}"` ), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="ede81-142">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="ede81-143">Associar a um tipo anulável, como `int?` ou `string` , e fornecer lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="ede81-143">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="ede81-144">Use um [componente de validação de formulário](xref:blazor/forms-validation), como <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="ede81-144">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="ede81-145">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="ede81-145">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="ede81-146">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="ede81-146">Form validation components:</span></span>
  * <span data-ttu-id="ede81-147">Permitir que o usuário forneça erros de entrada e de validação inválidos no associado <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="ede81-147">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="ede81-148">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="ede81-148">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="ede81-149">Formatar cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="ede81-149">Format strings</span></span>

<span data-ttu-id="ede81-150">A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="ede81-150">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="ede81-151">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="ede81-151">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="ede81-152">No código anterior, o `<input>` tipo de campo do elemento ( `type` ) usa como padrão `text` .</span><span class="sxs-lookup"><span data-stu-id="ede81-152">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="ede81-153">`@bind:format`tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="ede81-153">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="ede81-154"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="ede81-154"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="ede81-155"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="ede81-155"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="ede81-156">O `@bind:format` atributo especifica o formato de data a ser aplicado ao `value` do `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="ede81-156">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="ede81-157">O formato também é usado para analisar o valor quando `onchange` ocorre um evento.</span><span class="sxs-lookup"><span data-stu-id="ede81-157">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="ede81-158">Não é recomendável especificar um formato para o `date` tipo de campo porque o Blazor tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="ede81-158">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="ede81-159">Apesar da recomendação, use apenas o formato de `yyyy-MM-dd` data para que a associação funcione corretamente se um formato for fornecido com o `date` tipo de campo:</span><span class="sxs-lookup"><span data-stu-id="ede81-159">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="ede81-160">Associação de pai para filho com parâmetros de componente</span><span class="sxs-lookup"><span data-stu-id="ede81-160">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="ede81-161">A associação reconhece os parâmetros do componente, onde `@bind-{PROPERTY}` pode associar um valor de propriedade de um componente pai a um componente filho.</span><span class="sxs-lookup"><span data-stu-id="ede81-161">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="ede81-162">A associação de um filho a um pai é abordada na [Associação filho para pai com a seção de ligação encadeada](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="ede81-162">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="ede81-163">O componente filho a seguir ( `ChildComponent` ) tem um `Year` parâmetro de componente e um retorno de `YearChanged` chamada:</span><span class="sxs-lookup"><span data-stu-id="ede81-163">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="ede81-164"><xref:Microsoft.AspNetCore.Components.EventCallback%601>é explicado em <xref:blazor/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="ede81-164"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="ede81-165">O seguinte componente pai usa:</span><span class="sxs-lookup"><span data-stu-id="ede81-165">The following parent component uses:</span></span>

* <span data-ttu-id="ede81-166">`ChildComponent`e associa o `ParentYear` parâmetro do pai ao `Year` parâmetro no componente filho.</span><span class="sxs-lookup"><span data-stu-id="ede81-166">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="ede81-167">O `onclick` evento é usado para disparar o `ChangeTheYear` método.</span><span class="sxs-lookup"><span data-stu-id="ede81-167">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="ede81-168">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="ede81-168">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="ede81-169">O carregamento do `ParentComponent` produz a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="ede81-169">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="ede81-170">Se o valor da `ParentYear` propriedade for alterado selecionando o botão no `ParentComponent` , a `Year` Propriedade do `ChildComponent` será atualizada.</span><span class="sxs-lookup"><span data-stu-id="ede81-170">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="ede81-171">O novo valor de `Year` é renderizado na interface do usuário quando o `ParentComponent` é rerenderizado:</span><span class="sxs-lookup"><span data-stu-id="ede81-171">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="ede81-172">O `Year` parâmetro é ligável porque tem um `YearChanged` evento complementar que corresponde ao tipo do `Year` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="ede81-172">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="ede81-173">Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="ede81-173">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="ede81-174">Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo um `@bind-{PROPRETY}:event` atributo.</span><span class="sxs-lookup"><span data-stu-id="ede81-174">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="ede81-175">Por exemplo, a propriedade `MyProp` pode ser associada ao `MyEventHandler` uso dos dois atributos a seguir:</span><span class="sxs-lookup"><span data-stu-id="ede81-175">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="ede81-176">Associação de filho para pai com associação encadeada</span><span class="sxs-lookup"><span data-stu-id="ede81-176">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="ede81-177">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="ede81-177">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="ede81-178">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="ede81-178">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="ede81-179">Uma associação encadeada não pode ser implementada com [`@bind`](xref:mvc/views/razor#bind) a sintaxe no elemento da página.</span><span class="sxs-lookup"><span data-stu-id="ede81-179">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="ede81-180">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="ede81-180">The event handler and value must be specified separately.</span></span> <span data-ttu-id="ede81-181">Um componente pai, no entanto, pode usar [`@bind`](xref:mvc/views/razor#bind) a sintaxe com o parâmetro do componente.</span><span class="sxs-lookup"><span data-stu-id="ede81-181">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="ede81-182">O seguinte `PasswordField` componente (*passwordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ede81-182">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="ede81-183">Define o `<input>` valor de um elemento para uma `Password` propriedade.</span><span class="sxs-lookup"><span data-stu-id="ede81-183">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="ede81-184">Expõe as alterações da `Password` propriedade para um componente pai com um [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="ede81-184">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="ede81-185">Usa o `onclick` evento é usado para disparar o `ToggleShowPassword` método.</span><span class="sxs-lookup"><span data-stu-id="ede81-185">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="ede81-186">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="ede81-186">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

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
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="ede81-187">O `PasswordField` componente é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="ede81-187">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="ede81-188">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="ede81-188">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="ede81-189">Crie um campo de backup para `Password` ( `password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="ede81-189">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="ede81-190">Execute os erros de verificação ou interceptação no `Password` setter.</span><span class="sxs-lookup"><span data-stu-id="ede81-190">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="ede81-191">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="ede81-191">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
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
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="ede81-192">Botões de opção</span><span class="sxs-lookup"><span data-stu-id="ede81-192">Radio buttons</span></span>

<span data-ttu-id="ede81-193">Para obter informações sobre a associação a botões de opção em um formulário, consulte <xref:blazor/forms-validation#work-with-radio-buttons> .</span><span class="sxs-lookup"><span data-stu-id="ede81-193">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
