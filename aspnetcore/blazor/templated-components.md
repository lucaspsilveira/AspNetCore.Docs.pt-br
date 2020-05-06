---
title: ASP.NET Core Blazor componentes modelados
author: guardrex
description: Saiba como os componentes modelados podem aceitar um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: de603d3520c124b278312e5167a2f8bad14cf6e9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771061"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="21d55-103">ASP.NET Core Blazor componentes modelados</span><span class="sxs-lookup"><span data-stu-id="21d55-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="21d55-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="21d55-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="21d55-105">Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="21d55-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="21d55-106">Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais.</span><span class="sxs-lookup"><span data-stu-id="21d55-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="21d55-107">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="21d55-107">A couple of examples include:</span></span>

* <span data-ttu-id="21d55-108">Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="21d55-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="21d55-109">Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.</span><span class="sxs-lookup"><span data-stu-id="21d55-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="21d55-110">Parâmetros de modelo</span><span class="sxs-lookup"><span data-stu-id="21d55-110">Template parameters</span></span>

<span data-ttu-id="21d55-111">Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo `RenderFragment` ou. `RenderFragment<T>`</span><span class="sxs-lookup"><span data-stu-id="21d55-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="21d55-112">Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="21d55-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="21d55-113">`RenderFragment<T>`usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="21d55-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="21d55-114">`TableTemplate`componente</span><span class="sxs-lookup"><span data-stu-id="21d55-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="21d55-115">Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="21d55-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="21d55-116">As restrições de tipo genérico terão suporte em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="21d55-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="21d55-117">Para obter mais informações, consulte [permitir restrições de tipo genérico (#8433 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="21d55-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="21d55-118">Parâmetros de contexto de modelo</span><span class="sxs-lookup"><span data-stu-id="21d55-118">Template context parameters</span></span>

<span data-ttu-id="21d55-119">Argumentos de componente do `RenderFragment<T>` tipo passado como elementos têm um parâmetro implícito `context` chamado (por exemplo, do exemplo de código `@context.PetId`anterior,), mas você pode alterar o nome do `Context` parâmetro usando o atributo no elemento filho.</span><span class="sxs-lookup"><span data-stu-id="21d55-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="21d55-120">No exemplo a seguir, o `RowTemplate` atributo do `Context` elemento Especifica o `pet` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="21d55-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="21d55-121">Como alternativa, você pode especificar o `Context` atributo no elemento Component.</span><span class="sxs-lookup"><span data-stu-id="21d55-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="21d55-122">O atributo `Context` especificado se aplica a todos os parâmetros de modelo especificados.</span><span class="sxs-lookup"><span data-stu-id="21d55-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="21d55-123">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição).</span><span class="sxs-lookup"><span data-stu-id="21d55-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="21d55-124">No exemplo a seguir, o `Context` atributo aparece no `TableTemplate` elemento e se aplica a todos os parâmetros de modelo:</span><span class="sxs-lookup"><span data-stu-id="21d55-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="21d55-125">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="21d55-125">Generic-typed components</span></span>

<span data-ttu-id="21d55-126">Os componentes modelo são geralmente digitados genericamente.</span><span class="sxs-lookup"><span data-stu-id="21d55-126">Templated components are often generically typed.</span></span> <span data-ttu-id="21d55-127">Por exemplo, um componente `ListViewTemplate` genérico pode ser usado para renderizar `IEnumerable<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="21d55-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="21d55-128">Para definir um componente genérico, use a [`@typeparam`](xref:mvc/views/razor#typeparam) diretiva para especificar parâmetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="21d55-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="21d55-129">Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:</span><span class="sxs-lookup"><span data-stu-id="21d55-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="21d55-130">Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="21d55-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="21d55-131">No exemplo a seguir, `TItem="Pet"` especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="21d55-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
