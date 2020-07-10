---
title: ASP.NET Core Blazor formulários e validação
author: guardrex
description: Saiba como usar formatos e cenários de validação de campo no Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: b57e2a34f79691f7f2b1ed69cfad25de00c5ca13
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176214"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="74b8b-103">ASP.NET Core Blazor formulários e validação</span><span class="sxs-lookup"><span data-stu-id="74b8b-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="74b8b-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="74b8b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="74b8b-105">Há suporte para formulários e validação no Blazor uso de [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="74b8b-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="74b8b-106">O tipo a seguir `ExampleModel` define a lógica de validação usando as anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="74b8b-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="74b8b-107">Um formulário é definido usando o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> componente.</span><span class="sxs-lookup"><span data-stu-id="74b8b-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="74b8b-108">O formulário a seguir demonstra elementos, componentes e código típicos Razor :</span><span class="sxs-lookup"><span data-stu-id="74b8b-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="74b8b-109">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="74b8b-109">In the preceding example:</span></span>

* <span data-ttu-id="74b8b-110">O formulário valida a entrada do usuário no `name` campo usando a validação definida no `ExampleModel` tipo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="74b8b-111">O modelo é criado no bloco do componente `@code` e mantido em um campo privado ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="74b8b-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="74b8b-112">O campo é atribuído ao `Model` atributo do `<EditForm>` elemento.</span><span class="sxs-lookup"><span data-stu-id="74b8b-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="74b8b-113">As <xref:Microsoft.AspNetCore.Components.Forms.InputText> ligações do componente `@bind-Value` :</span><span class="sxs-lookup"><span data-stu-id="74b8b-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="74b8b-114">A propriedade do modelo ( `exampleModel.Name` ) para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `Value` .</span><span class="sxs-lookup"><span data-stu-id="74b8b-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="74b8b-115">Para obter mais informações sobre associação de propriedades, consulte <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="74b8b-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="74b8b-116">Um delegado de evento de alteração para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="74b8b-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="74b8b-117">O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="74b8b-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="74b8b-118">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="74b8b-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="74b8b-119">`HandleValidSubmit`é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="74b8b-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="74b8b-120">Componentes de formulários internos</span><span class="sxs-lookup"><span data-stu-id="74b8b-120">Built-in forms components</span></span>

<span data-ttu-id="74b8b-121">Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="74b8b-121">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="74b8b-122">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="74b8b-123">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="74b8b-123">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="74b8b-124">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="74b8b-124">Input component</span></span> | <span data-ttu-id="74b8b-125">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="74b8b-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="74b8b-126">Todos os componentes de entrada, incluindo <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="74b8b-126">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="74b8b-127">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-127">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="74b8b-128">Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-128">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="74b8b-129">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="74b8b-129">Some components include useful parsing logic.</span></span> <span data-ttu-id="74b8b-130">Por exemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> e <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> manipule valores não analisáveis normalmente registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="74b8b-130">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="74b8b-131">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="74b8b-131">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="74b8b-132">O tipo a seguir `Starship` define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que as anteriores `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="74b8b-132">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="74b8b-133">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="74b8b-133">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="74b8b-134">O formulário a seguir valida a entrada do usuário usando a validação definida no `Starship` modelo:</span><span class="sxs-lookup"><span data-stu-id="74b8b-134">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
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
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="74b8b-135">O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> cria um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como um [valor em cascata](xref:blazor/components/cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="74b8b-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="74b8b-136">O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> também fornece eventos convenientes para envios válidos e inválidos ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="74b8b-136">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="74b8b-137">Como alternativa, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para disparar a validação e verificar os valores do campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-137">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="74b8b-138">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="74b8b-138">In the following example:</span></span>

* <span data-ttu-id="74b8b-139">O `HandleSubmit` método é executado quando o **`Submit`** botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-139">The `HandleSubmit` method runs when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="74b8b-140">O formulário é validado usando o formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="74b8b-140">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="74b8b-141">O formulário é validado ainda mais, passando o <xref:Microsoft.AspNetCore.Components.Forms.EditContext> para o `ServerValidate` método que chama um ponto de extremidade da API Web no servidor (*não mostrado*).</span><span class="sxs-lookup"><span data-stu-id="74b8b-141">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="74b8b-142">O código adicional é executado dependendo do resultado da validação do cliente e do lado do servidor verificando `isValid` .</span><span class="sxs-lookup"><span data-stu-id="74b8b-142">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="74b8b-143">InputText com base no evento de entrada</span><span class="sxs-lookup"><span data-stu-id="74b8b-143">InputText based on the input event</span></span>

<span data-ttu-id="74b8b-144">Use o <xref:Microsoft.AspNetCore.Components.Forms.InputText> componente para criar um componente personalizado que usa o `input` evento em vez do `change` evento.</span><span class="sxs-lookup"><span data-stu-id="74b8b-144">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="74b8b-145">No exemplo a seguir, o `CustomInputText` componente herda o componente da estrutura `InputText` e define a associação de evento ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) para o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="74b8b-145">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="74b8b-146">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="74b8b-146">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="74b8b-147">O `CustomInputText` componente pode ser usado em qualquer lugar que <xref:Microsoft.AspNetCore.Components.Forms.InputText> for usado:</span><span class="sxs-lookup"><span data-stu-id="74b8b-147">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="74b8b-148">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="74b8b-148">`Pages/TestForm.razor`:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="74b8b-149">Botões de opção</span><span class="sxs-lookup"><span data-stu-id="74b8b-149">Radio buttons</span></span>

<span data-ttu-id="74b8b-150">Ao trabalhar com botões de opção em um formulário, a vinculação de dados é tratada de forma diferente de outros elementos porque os botões de opção são avaliados como um grupo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-150">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="74b8b-151">O valor de cada botão de opção é fixo, mas o valor do grupo de botões de opção é o valor do botão de opção selecionado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-151">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="74b8b-152">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="74b8b-152">The following example shows how to:</span></span>

* <span data-ttu-id="74b8b-153">Manipular a vinculação de dados para um grupo de botões de opção.</span><span class="sxs-lookup"><span data-stu-id="74b8b-153">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="74b8b-154">Suporte à validação usando um `InputRadio` componente personalizado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-154">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="74b8b-155">O seguinte <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa o `InputRadio` componente anterior para obter e validar uma classificação do usuário:</span><span class="sxs-lookup"><span data-stu-id="74b8b-155">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

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

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="74b8b-156">Associando `<select>` Opções de elemento a valores de objeto C# `null`</span><span class="sxs-lookup"><span data-stu-id="74b8b-156">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="74b8b-157">Não há uma maneira sensata de representar um `<select>` valor de opção de elemento como um valor de objeto C# `null` , porque:</span><span class="sxs-lookup"><span data-stu-id="74b8b-157">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="74b8b-158">Atributos HTML não podem ter `null` valores.</span><span class="sxs-lookup"><span data-stu-id="74b8b-158">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="74b8b-159">O equivalente mais próximo de `null` em HTML é a ausência do `value` atributo HTML do `<option>` elemento.</span><span class="sxs-lookup"><span data-stu-id="74b8b-159">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="74b8b-160">Ao selecionar um `<option>` sem `value` atributo, o navegador trata o valor como o *conteúdo de texto* do `<option>` elemento.</span><span class="sxs-lookup"><span data-stu-id="74b8b-160">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="74b8b-161">A Blazor estrutura não tenta suprimir o comportamento padrão porque ele envolveria:</span><span class="sxs-lookup"><span data-stu-id="74b8b-161">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="74b8b-162">Criar uma cadeia de soluções alternativas de caso especial no Framework.</span><span class="sxs-lookup"><span data-stu-id="74b8b-162">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="74b8b-163">Alterações recentes no comportamento da estrutura atual.</span><span class="sxs-lookup"><span data-stu-id="74b8b-163">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="74b8b-164">O equivalente mais plausível `null` em HTML é uma *cadeia de caracteres vazia* `value` .</span><span class="sxs-lookup"><span data-stu-id="74b8b-164">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="74b8b-165">A Blazor estrutura manipula `null` conversões de cadeia de caracteres vazias para a associação bidirecional a um `<select>` valor de.</span><span class="sxs-lookup"><span data-stu-id="74b8b-165">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="74b8b-166">A Blazor estrutura não lida automaticamente `null` com conversões de cadeia de caracteres vazias ao tentar fazer a ligação bidirecional com um `<select>` valor de.</span><span class="sxs-lookup"><span data-stu-id="74b8b-166">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="74b8b-167">Para obter mais informações, consulte [corrigir Associação `<select>` a um valor nulo (dotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="74b8b-167">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="74b8b-168">Suporte à validação</span><span class="sxs-lookup"><span data-stu-id="74b8b-168">Validation support</span></span>

<span data-ttu-id="74b8b-169">O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente anexa o suporte à validação usando anotações de dados para o em cascata <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="74b8b-169">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="74b8b-170">Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito.</span><span class="sxs-lookup"><span data-stu-id="74b8b-170">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="74b8b-171">Para usar um sistema de validação diferente de anotações de dados, substitua-o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="74b8b-171">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="74b8b-172">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="74b8b-172">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="74b8b-173">Os links anteriores para a fonte de referência fornecem o código do `master` Branch do repositório, que representa o desenvolvimento atual da unidade do produto para a próxima versão do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="74b8b-173">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="74b8b-174">Para selecionar a ramificação de uma versão diferente, use o seletor de ramificações do GitHub (por exemplo, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="74b8b-174">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

Blazor<span data-ttu-id="74b8b-175">executa dois tipos de validação:</span><span class="sxs-lookup"><span data-stu-id="74b8b-175"> performs two types of validation:</span></span>

* <span data-ttu-id="74b8b-176">A *validação de campo* é executada quando o usuário faz a Tabulação de um campo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-176">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="74b8b-177">Durante a validação de campo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente associa todos os resultados de validação relatados ao campo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-177">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="74b8b-178">A *validação do modelo* é executada quando o usuário envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="74b8b-178">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="74b8b-179">Durante a validação do modelo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente tenta determinar o campo com base no nome do membro que o resultado da validação relata.</span><span class="sxs-lookup"><span data-stu-id="74b8b-179">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="74b8b-180">Os resultados de validação que não estão associados a um membro individual são associados ao modelo em vez de um campo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-180">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="74b8b-181">Resumo de validação e componentes de mensagem de validação</span><span class="sxs-lookup"><span data-stu-id="74b8b-181">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="74b8b-182">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="74b8b-182">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="74b8b-183">Mensagens de validação de saída para um modelo específico com o `Model` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="74b8b-183">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="74b8b-184">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> componente exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="74b8b-184">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="74b8b-185">Especifique o campo para validação com o `For` atributo e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="74b8b-185">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="74b8b-186">Os <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componentes e oferecem suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="74b8b-186">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="74b8b-187">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` elemento gerado ou `<ul>` .</span><span class="sxs-lookup"><span data-stu-id="74b8b-187">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="74b8b-188">Controle o estilo das mensagens de validação na folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="74b8b-188">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="74b8b-189">A `validation-message` classe padrão define a cor do texto das mensagens de validação como vermelho:</span><span class="sxs-lookup"><span data-stu-id="74b8b-189">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="74b8b-190">Atributos de validação personalizados</span><span class="sxs-lookup"><span data-stu-id="74b8b-190">Custom validation attributes</span></span>

<span data-ttu-id="74b8b-191">Para garantir que um resultado de validação esteja corretamente associado a um campo ao usar um [atributo de validação personalizado](xref:mvc/models/validation#custom-attributes), passe o contexto de validação <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ao criar o <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="74b8b-191">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
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

> [!NOTE]
> <span data-ttu-id="74b8b-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> é `null`.</span><span class="sxs-lookup"><span data-stu-id="74b8b-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="74b8b-193">Não há suporte para injetar serviços para validação no `IsValid` método.</span><span class="sxs-lookup"><span data-stu-id="74b8b-193">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="74b8b-194">pacote de validação de anotações de dados</span><span class="sxs-lookup"><span data-stu-id="74b8b-194"> data annotations validation package</span></span>

<span data-ttu-id="74b8b-195">O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="74b8b-195">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="74b8b-196">O pacote está *experimental*no momento.</span><span class="sxs-lookup"><span data-stu-id="74b8b-196">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="74b8b-197">Atributo [compareproperty]</span><span class="sxs-lookup"><span data-stu-id="74b8b-197">[CompareProperty] attribute</span></span>

<span data-ttu-id="74b8b-198">O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente porque não associa o resultado da validação a um membro específico.</span><span class="sxs-lookup"><span data-stu-id="74b8b-198">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="74b8b-199">Isso pode resultar em comportamento inconsistente entre a validação em nível de campo e quando todo o modelo é validado em um envio.</span><span class="sxs-lookup"><span data-stu-id="74b8b-199">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="74b8b-200">O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) pacote *experimental* introduz um atributo de validação adicional, `ComparePropertyAttribute` que funciona em relação a essas limitações.</span><span class="sxs-lookup"><span data-stu-id="74b8b-200">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="74b8b-201">Em um Blazor aplicativo, `[CompareProperty]` é uma substituição direta para o [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-201">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="74b8b-202">Modelos aninhados, tipos de coleção e tipos complexos</span><span class="sxs-lookup"><span data-stu-id="74b8b-202">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="74b8b-203">fornece suporte para validar a entrada de formulário usando anotações de dados com o interno <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="74b8b-203"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="74b8b-204">No entanto, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.</span><span class="sxs-lookup"><span data-stu-id="74b8b-204">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="74b8b-205">Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo pacote *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="74b8b-205">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="74b8b-206">Anote as propriedades do modelo com `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="74b8b-206">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="74b8b-207">Nas classes de modelo a seguir, a `ShipDescription` classe contém anotações de dados adicionais para validar quando o modelo está associado ao formulário:</span><span class="sxs-lookup"><span data-stu-id="74b8b-207">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="74b8b-208">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="74b8b-208">`Starship.cs`:</span></span>

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

<span data-ttu-id="74b8b-209">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="74b8b-209">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="74b8b-210">Habilitar o botão enviar com base na validação do formulário</span><span class="sxs-lookup"><span data-stu-id="74b8b-210">Enable the submit button based on form validation</span></span>

<span data-ttu-id="74b8b-211">Para habilitar e desabilitar o botão enviar com base na validação do formulário:</span><span class="sxs-lookup"><span data-stu-id="74b8b-211">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="74b8b-212">Use o formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> para atribuir o modelo quando o componente for inicializado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-212">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="74b8b-213">Valide o formulário no retorno de chamada do contexto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> para habilitar e desabilitar o botão enviar.</span><span class="sxs-lookup"><span data-stu-id="74b8b-213">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="74b8b-214">Desvincule o manipulador de eventos no `Dispose` método.</span><span class="sxs-lookup"><span data-stu-id="74b8b-214">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="74b8b-215">Para obter mais informações, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="74b8b-215">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="74b8b-216">No exemplo anterior, defina `formInvalid` como `false` se:</span><span class="sxs-lookup"><span data-stu-id="74b8b-216">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="74b8b-217">O formulário é pré-carregado com valores padrão válidos.</span><span class="sxs-lookup"><span data-stu-id="74b8b-217">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="74b8b-218">Você deseja que o botão enviar seja habilitado quando o formulário é carregado.</span><span class="sxs-lookup"><span data-stu-id="74b8b-218">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="74b8b-219">Um efeito colateral da abordagem anterior é que um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente é preenchido com campos inválidos depois que o usuário interage com um campo.</span><span class="sxs-lookup"><span data-stu-id="74b8b-219">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="74b8b-220">Esse cenário pode ser resolvido de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="74b8b-220">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="74b8b-221">Não use um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente no formulário.</span><span class="sxs-lookup"><span data-stu-id="74b8b-221">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="74b8b-222">Torne o <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente visível quando o botão enviar estiver selecionado (por exemplo, em um `HandleValidSubmit` método).</span><span class="sxs-lookup"><span data-stu-id="74b8b-222">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="74b8b-223">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="74b8b-223">Troubleshoot</span></span>

> <span data-ttu-id="74b8b-224">InvalidOperationException: EditForm requer um parâmetro de modelo ou um parâmetro EditContext, mas não ambos.</span><span class="sxs-lookup"><span data-stu-id="74b8b-224">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="74b8b-225">Confirme se o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tem um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ou <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="74b8b-225">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="74b8b-226">Ao atribuir um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ao formulário, confirme se o tipo de modelo é instanciado, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="74b8b-226">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
