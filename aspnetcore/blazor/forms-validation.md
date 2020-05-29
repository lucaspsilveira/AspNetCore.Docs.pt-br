---
<span data-ttu-id="dd5b4-101">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-103">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-104">'Identity'</span></span>
- <span data-ttu-id="dd5b4-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-106">'Razor'</span></span>
- <span data-ttu-id="dd5b4-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="dd5b4-108">ASP.NET Core Blazor formulários e validação</span><span class="sxs-lookup"><span data-stu-id="dd5b4-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="dd5b4-109">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dd5b4-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dd5b4-110">Há suporte para formulários e validação no Blazor uso de [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="dd5b4-111">O tipo a seguir `ExampleModel` define a lógica de validação usando as anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="dd5b4-112">Um formulário é definido usando o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> componente.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="dd5b4-113">O formulário a seguir demonstra elementos, componentes e código típicos Razor :</span><span class="sxs-lookup"><span data-stu-id="dd5b4-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="dd5b4-114">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-114">In the preceding example:</span></span>

* <span data-ttu-id="dd5b4-115">O formulário valida a entrada do usuário no `name` campo usando a validação definida no `ExampleModel` tipo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="dd5b4-116">O modelo é criado no bloco do componente `@code` e mantido em um campo privado ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="dd5b4-117">O campo é atribuído ao `Model` atributo do `<EditForm>` elemento.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="dd5b4-118">As <xref:Microsoft.AspNetCore.Components.Forms.InputText> ligações do componente `@bind-Value` :</span><span class="sxs-lookup"><span data-stu-id="dd5b4-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="dd5b4-119">A propriedade do modelo ( `exampleModel.Name` ) para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `Value` .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="dd5b4-120">Para obter mais informações sobre associação de propriedades, consulte <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="dd5b4-121">Um delegado de evento de alteração para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="dd5b4-122">O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="dd5b4-123">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="dd5b4-124">`HandleValidSubmit`é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="dd5b4-125">Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="dd5b4-126">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="dd5b4-127">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="dd5b4-128">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="dd5b4-128">Input component</span></span> | <span data-ttu-id="dd5b4-129">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="dd5b4-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="dd5b4-130">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-131">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-132">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-133">'Identity'</span></span>
- <span data-ttu-id="dd5b4-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-135">'Razor'</span></span>
- <span data-ttu-id="dd5b4-136">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-137">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-138">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-139">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-140">'Identity'</span></span>
- <span data-ttu-id="dd5b4-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-142">'Razor'</span></span>
- <span data-ttu-id="dd5b4-143">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-144">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-145">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-146">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-147">'Identity'</span></span>
- <span data-ttu-id="dd5b4-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-149">'Razor'</span></span>
- <span data-ttu-id="dd5b4-150">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-151">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-152">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-153">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-154">'Identity'</span></span>
- <span data-ttu-id="dd5b4-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-156">'Razor'</span></span>
- <span data-ttu-id="dd5b4-157">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-158">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-159">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-160">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-161">'Identity'</span></span>
- <span data-ttu-id="dd5b4-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-163">'Razor'</span></span>
- <span data-ttu-id="dd5b4-164">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-164">'SignalR' uid:</span></span> 

<span data-ttu-id="dd5b4-165">-------- | título do---: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-166">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-167">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-168">'Identity'</span></span>
- <span data-ttu-id="dd5b4-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-170">'Razor'</span></span>
- <span data-ttu-id="dd5b4-171">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-172">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-173">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-174">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-175">'Identity'</span></span>
- <span data-ttu-id="dd5b4-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-177">'Razor'</span></span>
- <span data-ttu-id="dd5b4-178">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-179">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-180">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-181">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-182">'Identity'</span></span>
- <span data-ttu-id="dd5b4-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-184">'Razor'</span></span>
- <span data-ttu-id="dd5b4-185">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-186">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-187">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-188">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-189">'Identity'</span></span>
- <span data-ttu-id="dd5b4-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-191">'Razor'</span></span>
- <span data-ttu-id="dd5b4-192">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-193">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-194">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-195">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-196">'Identity'</span></span>
- <span data-ttu-id="dd5b4-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-198">'Razor'</span></span>
- <span data-ttu-id="dd5b4-199">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-200">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-201">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-202">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-203">'Identity'</span></span>
- <span data-ttu-id="dd5b4-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-205">'Razor'</span></span>
- <span data-ttu-id="dd5b4-206">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dd5b4-207">Título: ' ASP.NET Core Blazor formulários e validação ' autor: Descrição: ' saiba como usar formulários e cenários de validação de campo em Blazor . '</span><span class="sxs-lookup"><span data-stu-id="dd5b4-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="dd5b4-208">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd5b4-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-209">'Blazor'</span></span>
- <span data-ttu-id="dd5b4-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-210">'Identity'</span></span>
- <span data-ttu-id="dd5b4-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd5b4-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd5b4-212">'Razor'</span></span>
- <span data-ttu-id="dd5b4-213">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="dd5b4-213">'SignalR' uid:</span></span> 

<span data-ttu-id="dd5b4-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="dd5b4-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="dd5b4-215">Todos os componentes de entrada, incluindo <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="dd5b4-216">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="dd5b4-217">Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="dd5b4-218">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="dd5b4-219">Por exemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> e <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> manipule valores não analisáveis normalmente registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="dd5b4-220">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="dd5b4-221">O tipo a seguir `Starship` define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que as anteriores `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="dd5b4-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="dd5b4-222">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="dd5b4-223">O formulário a seguir valida a entrada do usuário usando a validação definida no `Starship` modelo:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="dd5b4-224">O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> cria um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="dd5b4-225">O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> também fornece eventos convenientes para envios válidos e inválidos ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="dd5b4-226">Como alternativa, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para disparar a validação e verificar os valores do campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="dd5b4-227">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-227">In the following example:</span></span>

* <span data-ttu-id="dd5b4-228">O `HandleSubmit` método é executado quando o botão **Enviar** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="dd5b4-229">O formulário é validado usando o formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="dd5b4-230">O formulário é validado ainda mais, passando o <xref:Microsoft.AspNetCore.Components.Forms.EditContext> para o `ServerValidate` método que chama um ponto de extremidade da API Web no servidor (*não mostrado*).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="dd5b4-231">O código adicional é executado dependendo do resultado da validação do cliente e do lado do servidor verificando `isValid` .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="dd5b4-232">InputText com base no evento de entrada</span><span class="sxs-lookup"><span data-stu-id="dd5b4-232">InputText based on the input event</span></span>

<span data-ttu-id="dd5b4-233">Use o <xref:Microsoft.AspNetCore.Components.Forms.InputText> componente para criar um componente personalizado que usa o `input` evento em vez do `change` evento.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="dd5b4-234">Crie um componente com a marcação a seguir e use o componente da mesma forma que <xref:Microsoft.AspNetCore.Components.Forms.InputText> é usado:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="dd5b4-235">Trabalhar com botões de opção</span><span class="sxs-lookup"><span data-stu-id="dd5b4-235">Work with radio buttons</span></span>

<span data-ttu-id="dd5b4-236">Ao trabalhar com botões de opção em um formulário, a vinculação de dados é tratada de forma diferente de outros elementos porque os botões de opção são avaliados como um grupo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="dd5b4-237">O valor de cada botão de opção é fixo, mas o valor do grupo de botões de opção é o valor do botão de opção selecionado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="dd5b4-238">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-238">The following example shows how to:</span></span>

* <span data-ttu-id="dd5b4-239">Manipular a vinculação de dados para um grupo de botões de opção.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="dd5b4-240">Suporte à validação usando um `InputRadio` componente personalizado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-240">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="dd5b4-241">O seguinte <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa o `InputRadio` componente anterior para obter e validar uma classificação do usuário:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="dd5b4-242">Suporte à validação</span><span class="sxs-lookup"><span data-stu-id="dd5b4-242">Validation support</span></span>

<span data-ttu-id="dd5b4-243">O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente anexa o suporte à validação usando anotações de dados para o em cascata <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="dd5b4-244">Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="dd5b4-245">Para usar um sistema de validação diferente de anotações de dados, substitua-o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="dd5b4-246">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="dd5b4-247">executa dois tipos de validação:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-247"> performs two types of validation:</span></span>

* <span data-ttu-id="dd5b4-248">A *validação de campo* é executada quando o usuário faz a Tabulação de um campo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="dd5b4-249">Durante a validação de campo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente associa todos os resultados de validação relatados ao campo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="dd5b4-250">A *validação do modelo* é executada quando o usuário envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="dd5b4-251">Durante a validação do modelo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente tenta determinar o campo com base no nome do membro que o resultado da validação relata.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="dd5b4-252">Os resultados de validação que não estão associados a um membro individual são associados ao modelo em vez de um campo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="dd5b4-253">Resumo de validação e componentes de mensagem de validação</span><span class="sxs-lookup"><span data-stu-id="dd5b4-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="dd5b4-254">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="dd5b4-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="dd5b4-255">Mensagens de validação de saída para um modelo específico com o `Model` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="dd5b4-256">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> componente exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="dd5b4-257">Especifique o campo para validação com o `For` atributo e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="dd5b4-258">Os <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componentes e oferecem suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="dd5b4-259">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` elemento gerado ou `<ul>` .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="dd5b4-260">Atributos de validação personalizados</span><span class="sxs-lookup"><span data-stu-id="dd5b4-260">Custom validation attributes</span></span>

<span data-ttu-id="dd5b4-261">Para garantir que um resultado de validação esteja corretamente associado a um campo ao usar um [atributo de validação personalizado](xref:mvc/models/validation#custom-attributes), passe o contexto de validação <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ao criar o <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="dd5b4-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="dd5b4-262">pacote de validação de anotações de dados</span><span class="sxs-lookup"><span data-stu-id="dd5b4-262"> data annotations validation package</span></span>

<span data-ttu-id="dd5b4-263">O [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="dd5b4-264">O pacote está *experimental*no momento.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="dd5b4-265">Atributo [compareproperty]</span><span class="sxs-lookup"><span data-stu-id="dd5b4-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="dd5b4-266">O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente porque não associa o resultado da validação a um membro específico.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="dd5b4-267">Isso pode resultar em comportamento inconsistente entre a validação em nível de campo e quando todo o modelo é validado em um envio.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="dd5b4-268">O pacote *experimental* [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduz um atributo de validação adicional, `ComparePropertyAttribute` , que funciona em relação a essas limitações.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="dd5b4-269">Em um Blazor aplicativo, `[CompareProperty]` é uma substituição direta para o [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="dd5b4-270">Modelos aninhados, tipos de coleção e tipos complexos</span><span class="sxs-lookup"><span data-stu-id="dd5b4-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="dd5b4-271">fornece suporte para validar a entrada de formulário usando anotações de dados com o interno <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="dd5b4-272">No entanto, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="dd5b4-273">Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo pacote *experimental* [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="dd5b4-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="dd5b4-274">Anote as propriedades do modelo com `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="dd5b4-275">Nas classes de modelo a seguir, a `ShipDescription` classe contém anotações de dados adicionais para validar quando o modelo está associado ao formulário:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="dd5b4-276">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-276">*Starship.cs*:</span></span>

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

<span data-ttu-id="dd5b4-277">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-277">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="dd5b4-278">Habilitar o botão enviar com base na validação do formulário</span><span class="sxs-lookup"><span data-stu-id="dd5b4-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="dd5b4-279">Para habilitar e desabilitar o botão enviar com base na validação do formulário:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="dd5b4-280">Use o formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> para atribuir o modelo quando o componente for inicializado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="dd5b4-281">Valide o formulário no retorno de chamada do contexto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> para habilitar e desabilitar o botão enviar.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="dd5b4-282">Desvincule o manipulador de eventos no `Dispose` método.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="dd5b4-283">Para obter mais informações, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="dd5b4-284">No exemplo anterior, defina `formInvalid` como `false` se:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="dd5b4-285">O formulário é pré-carregado com valores padrão válidos.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="dd5b4-286">Você deseja que o botão enviar seja habilitado quando o formulário é carregado.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="dd5b4-287">Um efeito colateral da abordagem anterior é que um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente é preenchido com campos inválidos depois que o usuário interage com um campo.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="dd5b4-288">Esse cenário pode ser resolvido de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="dd5b4-289">Não use um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente no formulário.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="dd5b4-290">Torne o <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente visível quando o botão enviar estiver selecionado (por exemplo, em um `HandleValidSubmit` método).</span><span class="sxs-lookup"><span data-stu-id="dd5b4-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="dd5b4-291">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="dd5b4-291">Troubleshoot</span></span>

> <span data-ttu-id="dd5b4-292">InvalidOperationException: EditForm requer um parâmetro de modelo ou um parâmetro EditContext, mas não ambos.</span><span class="sxs-lookup"><span data-stu-id="dd5b4-292">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="dd5b4-293">Confirme se o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tem um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ou <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="dd5b4-293">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="dd5b4-294">Ao atribuir um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ao formulário, confirme se o tipo de modelo é instanciado, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="dd5b4-294">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
