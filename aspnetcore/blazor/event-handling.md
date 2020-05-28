---
<span data-ttu-id="d84c8-101">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-103">'Blazor'</span></span>
- <span data-ttu-id="d84c8-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-104">'Identity'</span></span>
- <span data-ttu-id="d84c8-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-106">'Razor'</span></span>
- <span data-ttu-id="d84c8-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="d84c8-108">Manipulação de eventos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d84c8-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="d84c8-109">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d84c8-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="d84c8-110">os componentes fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="d84c8-110"> components provide event handling features.</span></span> <span data-ttu-id="d84c8-111">Para um atributo de elemento HTML chamado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por exemplo, `@onclick` ) com um valor de tipo delegado, um Razor componente trata o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="d84c8-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="d84c8-112">O código a seguir chama o `UpdateHeading` método quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="d84c8-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="d84c8-113">O código a seguir chama o `CheckChanged` método quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="d84c8-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="d84c8-114">Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="d84c8-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="d84c8-115">Não é necessário chamar [StateHasChanged](xref:blazor/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="d84c8-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="d84c8-116">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="d84c8-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="d84c8-117">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="d84c8-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="d84c8-118">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="d84c8-118">Event argument types</span></span>

<span data-ttu-id="d84c8-119">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="d84c8-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="d84c8-120">A especificação de um tipo de evento na chamada do método só será necessária se o tipo de evento for usado no método.</span><span class="sxs-lookup"><span data-stu-id="d84c8-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="d84c8-121">Com suporte, <xref:System.EventArgs> são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d84c8-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="d84c8-122">Evento</span><span class="sxs-lookup"><span data-stu-id="d84c8-122">Event</span></span>            | <span data-ttu-id="d84c8-123">Classe</span><span class="sxs-lookup"><span data-stu-id="d84c8-123">Class</span></span>                | <span data-ttu-id="d84c8-124">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="d84c8-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="d84c8-125">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-126">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-127">'Blazor'</span></span>
- <span data-ttu-id="d84c8-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-128">'Identity'</span></span>
- <span data-ttu-id="d84c8-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-130">'Razor'</span></span>
- <span data-ttu-id="d84c8-131">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-132">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-133">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-134">'Blazor'</span></span>
- <span data-ttu-id="d84c8-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-135">'Identity'</span></span>
- <span data-ttu-id="d84c8-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-137">'Razor'</span></span>
- <span data-ttu-id="d84c8-138">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-139">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-140">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-141">'Blazor'</span></span>
- <span data-ttu-id="d84c8-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-142">'Identity'</span></span>
- <span data-ttu-id="d84c8-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-144">'Razor'</span></span>
- <span data-ttu-id="d84c8-145">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-146">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-147">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-148">'Blazor'</span></span>
- <span data-ttu-id="d84c8-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-149">'Identity'</span></span>
- <span data-ttu-id="d84c8-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-151">'Razor'</span></span>
- <span data-ttu-id="d84c8-152">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-153">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-154">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-155">'Blazor'</span></span>
- <span data-ttu-id="d84c8-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-156">'Identity'</span></span>
- <span data-ttu-id="d84c8-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-158">'Razor'</span></span>
- <span data-ttu-id="d84c8-159">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-160">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-161">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-162">'Blazor'</span></span>
- <span data-ttu-id="d84c8-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-163">'Identity'</span></span>
- <span data-ttu-id="d84c8-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-165">'Razor'</span></span>
- <span data-ttu-id="d84c8-166">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-166">'SignalR' uid:</span></span> 

<span data-ttu-id="d84c8-167">-------- | ---Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-168">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-169">'Blazor'</span></span>
- <span data-ttu-id="d84c8-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-170">'Identity'</span></span>
- <span data-ttu-id="d84c8-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-172">'Razor'</span></span>
- <span data-ttu-id="d84c8-173">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-174">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-175">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-176">'Blazor'</span></span>
- <span data-ttu-id="d84c8-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-177">'Identity'</span></span>
- <span data-ttu-id="d84c8-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-179">'Razor'</span></span>
- <span data-ttu-id="d84c8-180">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-181">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-182">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-183">'Blazor'</span></span>
- <span data-ttu-id="d84c8-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-184">'Identity'</span></span>
- <span data-ttu-id="d84c8-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-186">'Razor'</span></span>
- <span data-ttu-id="d84c8-187">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-188">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-189">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-190">'Blazor'</span></span>
- <span data-ttu-id="d84c8-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-191">'Identity'</span></span>
- <span data-ttu-id="d84c8-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-193">'Razor'</span></span>
- <span data-ttu-id="d84c8-194">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-195">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-196">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-197">'Blazor'</span></span>
- <span data-ttu-id="d84c8-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-198">'Identity'</span></span>
- <span data-ttu-id="d84c8-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-200">'Razor'</span></span>
- <span data-ttu-id="d84c8-201">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-202">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-203">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-204">'Blazor'</span></span>
- <span data-ttu-id="d84c8-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-205">'Identity'</span></span>
- <span data-ttu-id="d84c8-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-207">'Razor'</span></span>
- <span data-ttu-id="d84c8-208">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-209">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-210">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-211">'Blazor'</span></span>
- <span data-ttu-id="d84c8-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-212">'Identity'</span></span>
- <span data-ttu-id="d84c8-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-214">'Razor'</span></span>
- <span data-ttu-id="d84c8-215">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-216">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-217">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-218">'Blazor'</span></span>
- <span data-ttu-id="d84c8-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-219">'Identity'</span></span>
- <span data-ttu-id="d84c8-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-221">'Razor'</span></span>
- <span data-ttu-id="d84c8-222">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-222">'SignalR' uid:</span></span> 

<span data-ttu-id="d84c8-223">---------- | ---Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-224">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-225">'Blazor'</span></span>
- <span data-ttu-id="d84c8-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-226">'Identity'</span></span>
- <span data-ttu-id="d84c8-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-228">'Razor'</span></span>
- <span data-ttu-id="d84c8-229">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-230">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-231">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-232">'Blazor'</span></span>
- <span data-ttu-id="d84c8-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-233">'Identity'</span></span>
- <span data-ttu-id="d84c8-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-235">'Razor'</span></span>
- <span data-ttu-id="d84c8-236">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-237">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-238">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-239">'Blazor'</span></span>
- <span data-ttu-id="d84c8-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-240">'Identity'</span></span>
- <span data-ttu-id="d84c8-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-242">'Razor'</span></span>
- <span data-ttu-id="d84c8-243">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-244">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-245">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-246">'Blazor'</span></span>
- <span data-ttu-id="d84c8-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-247">'Identity'</span></span>
- <span data-ttu-id="d84c8-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-249">'Razor'</span></span>
- <span data-ttu-id="d84c8-250">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-251">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-252">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-253">'Blazor'</span></span>
- <span data-ttu-id="d84c8-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-254">'Identity'</span></span>
- <span data-ttu-id="d84c8-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-256">'Razor'</span></span>
- <span data-ttu-id="d84c8-257">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-258">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-259">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-260">'Blazor'</span></span>
- <span data-ttu-id="d84c8-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-261">'Identity'</span></span>
- <span data-ttu-id="d84c8-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-263">'Razor'</span></span>
- <span data-ttu-id="d84c8-264">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-265">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-266">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-267">'Blazor'</span></span>
- <span data-ttu-id="d84c8-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-268">'Identity'</span></span>
- <span data-ttu-id="d84c8-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-270">'Razor'</span></span>
- <span data-ttu-id="d84c8-271">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d84c8-272">Título: ' ASP.NET Core Blazor manipulação de eventos ' autor: Descrição: ' saiba mais sobre os Blazor recursos de manipulação de eventos, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão. '</span><span class="sxs-lookup"><span data-stu-id="d84c8-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="d84c8-273">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d84c8-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d84c8-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-274">'Blazor'</span></span>
- <span data-ttu-id="d84c8-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d84c8-275">'Identity'</span></span>
- <span data-ttu-id="d84c8-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d84c8-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="d84c8-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d84c8-277">'Razor'</span></span>
- <span data-ttu-id="d84c8-278">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d84c8-278">'SignalR' uid:</span></span> 

<span data-ttu-id="d84c8-279">---------- | | Área de transferência | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Arrastar | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`</span><span class="sxs-lookup"><span data-stu-id="d84c8-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="d84c8-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> mantenha arrastado os dados do item.</span><span class="sxs-lookup"><span data-stu-id="d84c8-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="d84c8-281">| | Erro | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Evento | <xref:System.EventArgs>  |  *Geral*</span><span class="sxs-lookup"><span data-stu-id="d84c8-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="d84c8-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="d84c8-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="d84c8-283">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="d84c8-283">*Clipboard*</span></span><br><span data-ttu-id="d84c8-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="d84c8-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="d84c8-285">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="d84c8-285">*Input*</span></span><br><span data-ttu-id="d84c8-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="d84c8-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="d84c8-287">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="d84c8-287">*Media*</span></span><br><span data-ttu-id="d84c8-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="d84c8-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="d84c8-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>mantém atributos para configurar os mapeamentos entre os nomes de evento e os tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="d84c8-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="d84c8-290">| | Foco | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="d84c8-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="d84c8-291">Não inclui suporte para `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="d84c8-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="d84c8-292">| | Entrada | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Teclado | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Ponteiro do mouse | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Roda do mouse | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progresso | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Toque | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="d84c8-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="d84c8-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="d84c8-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="d84c8-294">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d84c8-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="d84c8-295">[Classes EventArgs no ASP.NET Core fonte de referência (dotNet/aspnetcore versão/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="d84c8-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="d84c8-296">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.</span><span class="sxs-lookup"><span data-stu-id="d84c8-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="d84c8-297">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="d84c8-297">Lambda expressions</span></span>

<span data-ttu-id="d84c8-298">As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="d84c8-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="d84c8-299">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="d84c8-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="d84c8-300">O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e seu número de botão ( `buttonNumber` ) quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="d84c8-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="d84c8-301">**Não** use a variável de loop ( `i` ) em um `for` loop diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="d84c8-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="d84c8-302">Caso contrário, a mesma variável é usada por todas as expressões lambda, fazendo `i` com que o valor seja o mesmo em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="d84c8-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="d84c8-303">Sempre Capture seu valor em uma variável local ( `buttonNumber` no exemplo anterior) e use-o.</span><span class="sxs-lookup"><span data-stu-id="d84c8-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="d84c8-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="d84c8-304">EventCallback</span></span>

<span data-ttu-id="d84c8-305">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho.</span><span class="sxs-lookup"><span data-stu-id="d84c8-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="d84c8-306">Um `onclick` evento que ocorre no componente filho é um caso de uso comum.</span><span class="sxs-lookup"><span data-stu-id="d84c8-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="d84c8-307">Para expor eventos entre componentes, use um <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="d84c8-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="d84c8-308">Um componente pai pode atribuir um método de retorno de chamada a um componente filho <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="d84c8-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="d84c8-309">O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o manipulador de um botão `onclick` é configurado para receber um <xref:Microsoft.AspNetCore.Components.EventCallback> delegado de exemplo `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="d84c8-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="d84c8-310">O <xref:Microsoft.AspNetCore.Components.EventCallback> é digitado com `MouseEventArgs` , que é apropriado para um `onclick` evento de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="d84c8-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="d84c8-311">O `ParentComponent` define o filho <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) para seu `ShowMessage` método.</span><span class="sxs-lookup"><span data-stu-id="d84c8-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="d84c8-312">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d84c8-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="d84c8-313">Quando o botão estiver selecionado no `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="d84c8-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="d84c8-314">O `ParentComponent` `ShowMessage` método é chamado.</span><span class="sxs-lookup"><span data-stu-id="d84c8-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="d84c8-315">`messageText`é atualizado e exibido no `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="d84c8-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="d84c8-316">Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no método do retorno de chamada ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="d84c8-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="d84c8-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado automaticamente para renderizar novamente o `ParentComponent` , assim como eventos filho, rerenderização de componente em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="d84c8-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="d84c8-318"><xref:Microsoft.AspNetCore.Components.EventCallback>e <xref:Microsoft.AspNetCore.Components.EventCallback%601> permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="d84c8-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="d84c8-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601>é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="d84c8-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="d84c8-320"><xref:Microsoft.AspNetCore.Components.EventCallback>está com tipo fraco e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="d84c8-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="d84c8-321">Invocar um <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> com <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e aguardar <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="d84c8-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="d84c8-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="d84c8-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="d84c8-323">Prefira o tipo fortemente tipado <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="d84c8-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="d84c8-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="d84c8-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="d84c8-325">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="d84c8-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="d84c8-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="d84c8-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="d84c8-327">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="d84c8-327">Prevent default actions</span></span>

<span data-ttu-id="d84c8-328">Use o [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributo diretiva para impedir a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="d84c8-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="d84c8-329">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="d84c8-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="d84c8-330">No exemplo a seguir, o comportamento padrão é impedido pela especificação do `@onkeypress:preventDefault` atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="d84c8-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="d84c8-331">O contador é incrementado e a **+** chave não é capturada no `<input>` valor do elemento:</span><span class="sxs-lookup"><span data-stu-id="d84c8-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="d84c8-332">Especificar o `@on{EVENT}:preventDefault` atributo sem um valor é equivalente a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="d84c8-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="d84c8-333">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="d84c8-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="d84c8-334">No exemplo a seguir, `shouldPreventDefault` é um `bool` conjunto de campos como `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="d84c8-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="d84c8-335">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="d84c8-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="d84c8-336">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="d84c8-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="d84c8-337">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="d84c8-337">Stop event propagation</span></span>

<span data-ttu-id="d84c8-338">Use o [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributo diretiva para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="d84c8-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="d84c8-339">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` se propaguem para o pai `<div>` :</span><span class="sxs-lookup"><span data-stu-id="d84c8-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
