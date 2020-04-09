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
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Blazor vinculação de dados do Núcleo

Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)

Os componentes da navalha fornecem recursos [`@bind`](xref:mvc/views/razor#bind) de vinculação de dados através de um atributo de elemento HTML nomeado com um valor de expressão de campo, propriedade ou Razor.

O exemplo a `CurrentValue` seguir vincula a propriedade ao valor da caixa de texto:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.

A caixa de texto é atualizada na ui somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade. Uma vez que os componentes se tornam após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na ui imediatamente após um manipulador de eventos ser acionado.

O `@bind` uso `CurrentValue` com`<input @bind="CurrentValue" />`a propriedade ( ) é essencialmente equivalente ao seguinte:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Quando o componente é `value` renderizado, o elemento `CurrentValue` de entrada vem da propriedade. Quando o usuário digita na caixa de `onchange` texto e `CurrentValue` altera o foco do elemento, o evento é acionado e a propriedade é definida como o valor alterado. Na realidade, a geração de `@bind` código é mais complexa porque lida com casos em que conversões de tipo são realizadas. Em princípio, `@bind` associa o valor atual `value` de uma expressão a um atributo e lida com alterações usando o manipulador registrado.

Vincule uma propriedade ou campo em `@bind:event` outros `event` eventos, incluindo também um atributo com um parâmetro. O exemplo a `CurrentValue` seguir vincula a propriedade no `oninput` evento:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Ao `onchange`contrário , que dispara `oninput` quando o elemento perde o foco, é acionado quando o valor da caixa de texto muda.

Use `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` com sintaxe para vincular `value`atributos de elemento que não sejam . No exemplo a seguir, o estilo do `_paragraphStyle` parágrafo é atualizado quando o valor muda:

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

Vinculação de atributos é sensível ao caso. Por exemplo, `@bind` é `@Bind` válido e é inválido.

## <a name="unparsable-values"></a>Valores inparáveis

Quando um usuário fornece um valor inparável a um elemento vinculado a dados, o valor inparável é automaticamente revertido para o valor anterior quando o evento de vinculação é acionado.

Considere o cenário a seguir.

* Um `<input>` elemento está `int` vinculado a um `123`tipo com um valor inicial de:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* O usuário atualiza o valor `123.45` do elemento para a página e altera o foco do elemento.

No cenário anterior, o valor do elemento `123`é revertido para . Quando o `123.45` valor é rejeitado em favor `123`do valor original de , o usuário entende que seu valor não foi aceito.

Por padrão, a vinculação se `onchange` aplica`@bind="{PROPERTY OR FIELD}"`ao evento do elemento ( ). Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para ativar a ligação em um evento diferente. Para `oninput` o`@bind:event="oninput"`evento (), a reversão ocorre após qualquer tecla que introduza um valor inparável. Ao direcionar `oninput` o evento `int`com um tipo vinculado, um `.` usuário é impedido de digitar um caractere. Um `.` caractere é imediatamente removido, de modo que o usuário recebe feedback imediato de que apenas números inteiros são permitidos. Há cenários em que reverter `oninput` o valor do evento não é o ideal, como quando `<input>` o usuário deve ser autorizado a limpar um valor inparável. As alternativas incluem:

* Não use o `oninput` evento. Use o `onchange` evento padrão `@bind="{PROPERTY OR FIELD}"`(apenas especifique), quando um valor inválido não for revertido até que o elemento perca o foco.
* Vincule-se a um `int?` tipo `string`anulado, como ou , e forneça lógica personalizada para lidar com entradas inválidas.
* Use um componente de `InputNumber` validação `InputDate` [de formulário,](xref:blazor/forms-validation)como ou . Os componentes de validação de formulário têm suporte integrado para gerenciar entradas inválidas. Componentes de validação de formulários:
  * Permitir que o usuário forneça entrada inválida `EditContext`e receba erros de validação no associado .
  * Exibir erros de validação na ui sem interferir com o usuário inserindo dados adicionais da Webform.

## <a name="format-strings"></a>Strings de formato

A vinculação <xref:System.DateTime> de dados [`@bind:format`](xref:mvc/views/razor#bind)funciona com strings de formato usando . Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

No código anterior, `<input>` o tipo de`type`campo do `text`elemento ( ) é padrão para . `@bind:format`é suportado para vincular os seguintes tipos .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

O `@bind:format` atributo especifica o formato `value` de `<input>` data a ser aplicado ao do elemento. O formato também é usado para `onchange` analisar o valor quando um evento ocorre.

A especificação de `date` um formato para o Blazor tipo de campo não é recomendada porque tem suporte incorporado para formatar datas. Apesar da recomendação, `yyyy-MM-dd` use apenas o formato de data para vinculação para funcionar corretamente se um formato for fornecido com o `date` tipo de campo:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Vinculação pai-filho com parâmetros de componentes

A vinculação reconhece `@bind-{PROPERTY}` parâmetros de componentes, onde pode vincular um valor de propriedade de um componente pai a um componente filho. A vinculação de uma criança a um pai é coberta na vinculação de filho para pai com seção [de vinculação acorrentada.](#child-to-parent-binding-with-chained-bind)

O seguinte componente`ChildComponent`filho `Year` ( ) `YearChanged` tem um parâmetro de componente e retorno de chamada:

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

`EventCallback<T>`é explicado <xref:blazor/event-handling#eventcallback>em .

O seguinte componente pai usa:

* `ChildComponent`e liga `ParentYear` o parâmetro do pai `Year` ao parâmetro no componente da criança.
* O `onclick` evento é usado `ChangeTheYear` para ativar o método. Para obter mais informações, consulte <xref:blazor/event-handling>.

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

Carregar `ParentComponent` os produtos a seguinte marcação:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Se o valor `ParentYear` da propriedade for alterado selecionando `Year` o `ChildComponent` botão no `ParentComponent`, a propriedade do é atualizado. O novo `Year` valor é renderizado na `ParentComponent` ui quando a rerenderização é rerenderizada:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

O `Year` parâmetro é vinculável porque `YearChanged` tem um evento companheiro `Year` que corresponde ao tipo do parâmetro.

Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente à escrita:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Em geral, uma propriedade pode ser vinculada a `@bind-{PROPRETY}:event` um manipulador de eventos correspondente, incluindo um atributo. Por exemplo, `MyProp` a propriedade `MyEventHandler` pode ser vinculada ao uso dos dois atributos a seguir:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Vinculação de filho para pai com vínculo acorrentado

Um cenário comum é acorrentar um parâmetro vinculado a dados a um elemento de página na saída do componente. Este cenário é chamado de *vínculo acorrentado* porque vários níveis de ligação ocorrem simultaneamente.

Uma ligação encadeada `@bind` não pode ser implementada com sintaxe no elemento da página. O manipulador de eventos e o valor devem ser especificados separadamente. Um componente pai, no `@bind` entanto, pode usar sintaxe com o parâmetro do componente.

O `PasswordField` seguinte componente *(PasswordField.razor):*

* Define `<input>` o valor de `Password` um elemento para uma propriedade.
* Expõe alterações `Password` da propriedade a um componente pai com um [EventCallback](xref:blazor/event-handling#eventcallback).
* O `onclick` uso do evento `ToggleShowPassword` é usado para ativar o método. Para obter mais informações, consulte <xref:blazor/event-handling>.

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

O `PasswordField` componente é usado em outro componente:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Para executar verificações ou prender erros na senha no exemplo anterior:

* Crie um campo `Password` `_password` de apoio para (no código de exemplo a seguir).
* Execute as verificações ou `Password` erros de armadilha no setter.

O exemplo a seguir fornece feedback imediato ao usuário se um espaço for usado no valor da senha:

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

## <a name="radio-buttons"></a>Botões de opção

Para obter informações sobre a vinculação <xref:blazor/forms-validation#work-with-radio-buttons>a botões de rádio em um formulário, consulte .
