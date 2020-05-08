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
ms.openlocfilehash: b4951c5eb712b15db3a7c1ccd57ae01c530a23ef
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967162"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor Associação de dados

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razoros componentes fornecem recursos de vinculação de dados por meio de [`@bind`](xref:mvc/views/razor#bind) um atributo de elemento HTML chamado com Razor um campo, propriedade ou valor de expressão.

O exemplo a seguir associa a `CurrentValue` Propriedade ao valor da caixa de texto:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.

A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade. Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.

O `@bind` uso da `CurrentValue` Propriedade (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Quando o componente é renderizado, `value` o do elemento de entrada vem da `CurrentValue` propriedade. Quando o usuário digita na caixa de texto e altera o foco do elemento `onchange` , o evento é acionado e a `CurrentValue` propriedade é definida como o valor alterado. Na realidade, a geração de código é mais complexa `@bind` porque lida com casos em que conversões de tipo são executadas. Em princípio, `@bind` associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.

Associe uma propriedade ou um campo a outros eventos, incluindo também `@bind:event` um atributo com `event` um parâmetro. O exemplo a seguir associa a `CurrentValue` Propriedade no `oninput` evento:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Ao `onchange`contrário de, que é acionado quando o `oninput` elemento perde o foco, é acionado quando o valor da caixa de texto é alterado.

Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` sintaxe para associar atributos de elemento diferentes `value`de. No exemplo a seguir, o estilo do parágrafo é atualizado quando o `paragraphStyle` valor é alterado:

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

A associação de atributo diferencia maiúsculas de minúsculas. Por exemplo, `@bind` é válido e `@Bind` é inválido.

## <a name="unparsable-values"></a>Valores não analisáveis

Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.

Considere o cenário a seguir.

* Um `<input>` elemento está associado a um `int` tipo com um valor inicial de `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.

No cenário anterior, o valor do elemento é revertido para `123`. Quando o valor `123.45` é rejeitado em favor do valor original de `123`, o usuário entende que seu valor não foi aceito.

Por padrão, a associação aplica-se ao `onchange` evento do`@bind="{PROPERTY OR FIELD}"`elemento (). Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente. Para o `oninput` evento (`@bind:event="oninput"`), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável. Ao direcionar `oninput` o evento com `int`um tipo associado, um usuário é impedido de digitar `.` um caractere. Um `.` caractere é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos. Há cenários em que a reversão do valor no `oninput` evento não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um `<input>` valor não analisável. As alternativas incluem:

* Não use o `oninput` evento. Use o evento `onchange` padrão (especifique `@bind="{PROPERTY OR FIELD}"`apenas), em que um valor inválido não é revertido até que o elemento perca o foco.
* Associar a um tipo anulável, como `int?` ou `string`, e fornecer lógica personalizada para manipular entradas inválidas.
* Use um [componente de validação de formulário](xref:blazor/forms-validation), `InputNumber` como `InputDate`ou. Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas. Componentes de validação de formulário:
  * Permitir que o usuário forneça erros de entrada e de validação inválidos `EditContext`no associado.
  * Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.

## <a name="format-strings"></a>Formatar cadeias de caracteres

A vinculação de dados <xref:System.DateTime> funciona com cadeias de caracteres de formato usando [`@bind:format`](xref:mvc/views/razor#bind). Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

No código anterior, o `<input>` tipo de campo do elemento (`type`) usa como `text`padrão. `@bind:format`tem suporte para ligar os seguintes tipos .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

O `@bind:format` atributo especifica o formato de data a ser aplicado `value` ao do `<input>` elemento. O formato também é usado para analisar o valor quando ocorre `onchange` um evento.

Não é recomendável especificar `date` um formato para o tipo Blazor de campo porque o tem suporte interno para formatar datas. Apesar da recomendação, use apenas o `yyyy-MM-dd` formato de data para que a associação funcione corretamente se um formato for fornecido com o `date` tipo de campo:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Associação de pai para filho com parâmetros de componente

A associação reconhece os parâmetros do `@bind-{PROPERTY}` componente, onde pode associar um valor de propriedade de um componente pai a um componente filho. A associação de um filho a um pai é abordada na [Associação filho para pai com a seção de ligação encadeada](#child-to-parent-binding-with-chained-bind) .

O componente filho a seguir`ChildComponent`() tem `Year` um parâmetro de `YearChanged` componente e um retorno de chamada:

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

`EventCallback<T>`é explicado <xref:blazor/event-handling#eventcallback>em.

O seguinte componente pai usa:

* `ChildComponent`e associa o `ParentYear` parâmetro do pai ao `Year` parâmetro no componente filho.
* O `onclick` evento é usado para disparar o `ChangeTheYear` método. Para obter mais informações, consulte <xref:blazor/event-handling>.

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

O carregamento `ParentComponent` do produz a seguinte marcação:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Se o valor `ParentYear` da propriedade for alterado selecionando o botão no `ParentComponent`, a `Year` Propriedade do `ChildComponent` será atualizada. O novo valor de `Year` é renderizado na interface do usuário `ParentComponent` quando o é rerenderizado:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

O `Year` parâmetro é ligável porque tem um evento `YearChanged` complementar que corresponde ao tipo do `Year` parâmetro.

Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo `@bind-{PROPRETY}:event` um atributo. Por exemplo, a propriedade `MyProp` pode ser associada ao `MyEventHandler` uso dos dois atributos a seguir:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Associação de filho para pai com associação encadeada

Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente. Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.

Uma associação encadeada não pode ser implementada com `@bind` a sintaxe no elemento da página. O manipulador de eventos e o valor devem ser especificados separadamente. Um componente pai, no entanto, `@bind` pode usar a sintaxe com o parâmetro do componente.

O seguinte `PasswordField` componente (*passwordField. Razor*):

* Define o `<input>` valor de um elemento para `Password` uma propriedade.
* Expõe as alterações da `Password` propriedade para um componente pai com um [EventCallback](xref:blazor/event-handling#eventcallback).
* Usa o `onclick` evento é usado para disparar o `ToggleShowPassword` método. Para obter mais informações, consulte <xref:blazor/event-handling>.

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

O `PasswordField` componente é usado em outro componente:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Para executar verificações ou interceptar erros na senha no exemplo anterior:

* Crie um campo de backup para `Password` (`password` no código de exemplo a seguir).
* Execute os erros de verificação ou interceptação `Password` no setter.

O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:

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

## <a name="radio-buttons"></a>Botões de opção

Para obter informações sobre a associação a botões de opção em um <xref:blazor/forms-validation#work-with-radio-buttons>formulário, consulte.
