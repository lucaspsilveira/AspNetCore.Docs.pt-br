---
title: ASP.NET Blazor Formulários e validação do Núcleo
author: guardrex
description: Aprenda a usar formulários e Blazorcenários de validação de campo em .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218954"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET formulários e validação do Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Formulários e validação são suportados em Blazor usando [anotações de dados](xref:mvc/models/validation).

O `ExampleModel` seguinte tipo define a lógica de validação usando anotações de dados:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Um formulário é `EditForm` definido usando o componente. O formulário a seguir demonstra elementos típicos, componentes e código de navalha:

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

No exemplo anterior:

* O formulário valida a `name` entrada do usuário `ExampleModel` no campo usando a validação definida no tipo. O modelo é criado no `@code` bloco do componente e`_exampleModel`mantido em um campo privado ( ). O campo é atribuído `Model` ao `<EditForm>` atributo do elemento.
* As `InputText` ligas `@bind-Value` do componente:
  * A propriedade`_exampleModel.Name`modelo ( `InputText` ) `Value` para propriedade do componente.
  * Um delegado de `InputText` evento de `ValueChanged` alteração para a propriedade do componente.
* O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados.
* O `ValidationSummary` componente resume as mensagens de validação.
* `HandleValidSubmit`é acionado quando o formulário é submetido com sucesso (passa validação).

Um conjunto de componentes de entrada incorporados estão disponíveis para receber e validar a entrada do usuário. As entradas são validadas quando são alteradas e quando um formulário é enviado. Os componentes de entrada disponíveis são mostrados na tabela a seguir.

| Componente de entrada | Prestado si ma&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Todos os componentes de `EditForm`entrada, incluindo , suportam atributos arbitrários. Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.

Os componentes de entrada fornecem comportamento padrão para validar na edição e alterar sua classe CSS para refletir o estado de campo. Alguns componentes incluem lógica de análise útil. Por `InputDate` exemplo, `InputNumber` e manuseie valores inparáveis graciosamente registrando-os como erros de validação. Tipos que podem aceitar valores nulos também suportam `int?`a nulidade do campo de destino (por exemplo, ).

O `Starship` tipo a seguir define a lógica de validação usando um `ExampleModel`conjunto maior de propriedades e anotações de dados do que o anterior:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

No exemplo anterior, `Description` é opcional porque não há anotações de dados.

O formulário a seguir valida a `Starship` entrada do usuário usando a validação definida no modelo:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

O `EditForm` cria `EditContext` um valor em [cascata](xref:blazor/components#cascading-values-and-parameters) que rastreia metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais. O `EditForm` também fornece eventos convenientes para`OnValidSubmit`envios válidos e inválidos ( , `OnInvalidSubmit`). Alternativamente, `OnSubmit` use para ativar a validação e verificar os valores de campo com código de validação personalizado.

No exemplo a seguir:

* O `HandleSubmit` método é executado quando o botão **Enviar** é selecionado.
* O formulário é validado `EditContext`usando o formulário.
* O formulário é ainda `EditContext` validado passando o `ServerValidate` método que chama um ponto final de API web no servidor *(não mostrado).*
* O código adicional é executado dependendo do resultado da validação `isValid`do lado do cliente e do servidor, verificando .

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>InputText baseado no evento de entrada

Use `InputText` o componente para criar um `input` componente personalizado `change` que use o evento em vez do evento.

Crie um componente com a seguinte marcação `InputText` e use o componente da seguinte vez:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Trabalhe com botões de rádio

Ao trabalhar com botões de rádio em um formulário, a vinculação de dados é tratada de forma diferente de outros elementos porque os botões de rádio são avaliados como um grupo. O valor de cada botão de rádio é fixo, mas o valor do grupo do botão de rádio é o valor do botão de rádio selecionado. O exemplo a seguir mostra como:

* Manuseie a vinculação de dados para um grupo de botões de rádio.
* Validação de suporte `InputRadio` usando um componente personalizado.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

O `EditForm` seguinte usa `InputRadio` o componente anterior para obter e validar uma classificação do usuário:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Suporte de validação

O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados à cascata `EditContext`. Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito. Para usar um sistema de validação diferente das `DataAnnotationsValidator` anotações de dados, substitua-o por uma implementação personalizada. A implementação do ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotaçõesValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazorrealiza dois tipos de validação:

* *A validação de* campo é realizada quando o usuário faz guias fora de um campo. Durante a validação `DataAnnotationsValidator` de campo, todos os componentes relataram resultados de validação com o campo.
* *A validação do modelo* é realizada quando o usuário envia o formulário. Durante a validação `DataAnnotationsValidator` do modelo, o componente tenta determinar o campo com base no nome do membro que o resultado da validação relata. Os resultados de validação que não estão associados a um membro individual estão associados ao modelo e não a um campo.

### <a name="validation-summary-and-validation-message-components"></a>Resumo de validação e componentes da mensagem de validação

O `ValidationSummary` componente resume todas as mensagens de validação, que são semelhantes ao [Sumário de validação :](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)

```razor
<ValidationSummary />
```

Mensagens de validação de saída `Model` para um modelo específico com o parâmetro:
  
```razor
<ValidationSummary Model="@_starship" />
```

O `ValidationMessage` componente exibe mensagens de validação para um campo específico, semelhante ao [Helper de tag de marcação de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique o `For` campo para validação com o atributo e uma expressão lambda nomeando a propriedade modelo:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Os `ValidationMessage` `ValidationSummary` componentes suportam atributos arbitrários. Qualquer atributo que não corresponda a um `<div>` parâmetro `<ul>` de componente é adicionado ao elemento gerado ou gerado.

### <a name="custom-validation-attributes"></a>Atributos de validação personalizados

Para garantir que um resultado de validação esteja corretamente associado a um campo <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ao <xref:System.ComponentModel.DataAnnotations.ValidationResult>usar um [atributo de validação personalizado,](xref:mvc/models/validation#custom-attributes)passe o contexto de validação ao criar o :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazorpacote de validação de anotações de dados

O [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) é um pacote que preenche `DataAnnotationsValidator` lacunas de experiência de validação usando o componente. O pacote é atualmente *experimental*.

### <a name="compareproperty-attribute"></a>Atributo [CompareProperty]

O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem `DataAnnotationsValidator` com o componente porque não associa o resultado de validação a um membro específico. Isso pode resultar em um comportamento inconsistente entre a validação em nível de campo e quando todo o modelo é validado em um envio. O pacote experimental [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* introduz um `ComparePropertyAttribute`atributo de validação adicional, que funciona em torno dessas limitações. Em Blazor um `[CompareProperty]` aplicativo, é uma `[Compare]` substituição direta para o atributo.

### <a name="nested-models-collection-types-and-complex-types"></a>Modelos aninhados, tipos de coleção e tipos complexos

Blazorfornece suporte para validar a entrada de formulário usando `DataAnnotationsValidator`anotações de dados com o embutido . No entanto, a `DataAnnotationsValidator` única valida propriedades de nível superior do modelo vinculadas à forma que não são propriedades de tipo de coleta ou de tipo complexo.

Para validar todo o gráfico de objetos do modelo vinculado, `ObjectGraphDataAnnotationsValidator` incluindo propriedades de coleta e tipo complexo, use o fornecido pelo [Microsoft.AspNetCore.Components.DataAnnotations.Pacote de validação:](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental*

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Anotar propriedades do `[ValidateComplexType]`modelo com . Nas classes de modelo `ShipDescription` a seguir, a classe contém anotações adicionais de dados para validar quando o modelo está vinculado ao formulário:

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs:*

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Habilite o botão enviar com base na validação do formulário

Para ativar e desativar o botão enviar com base na validação do formulário:

* Use o formulário `EditContext` para atribuir o modelo quando o componente for inicializado.
* Valide o formulário no `OnFieldChanged` retorno de chamada do contexto para ativar e desativar o botão enviar.
* Desengaque o `Dispose` manipulador de eventos no método. Para obter mais informações, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

No exemplo anterior, `_formInvalid` `false` definido para se:

* O formulário é pré-carregado com valores padrão válidos.
* Você deseja ativar o botão enviar quando o formulário for carregado.

Um efeito colateral da abordagem `ValidationSummary` anterior é que um componente é preenchido com campos inválidos depois que o usuário interage com qualquer campo. Este cenário pode ser abordado de qualquer uma das seguintes maneiras:

* Não use um `ValidationSummary` componente no formulário.
* Tornar `ValidationSummary` o componente visível quando o botão de `HandleValidSubmit` envio é selecionado (por exemplo, em um método).

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
