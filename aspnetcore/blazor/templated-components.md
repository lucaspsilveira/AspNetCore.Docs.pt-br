---
title: ASP.NET Blazor componentes modelados do Core
author: guardrex
description: Saiba como os componentes modelados podem aceitar um ou mais modelos de IA como parâmetros, que podem ser usados como parte da lógica de renderização do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989497"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="3b7c4-103">ASP.NET Blazor componentes modelados do Core</span><span class="sxs-lookup"><span data-stu-id="3b7c4-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="3b7c4-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3b7c4-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3b7c4-105">Os componentes modelados são componentes que aceitam um ou mais modelos de IA como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="3b7c4-106">Os componentes modelados permitem que você seja autor de componentes de nível superior que são mais reutilizáveis do que componentes regulares.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="3b7c4-107">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="3b7c4-107">A couple of examples include:</span></span>

* <span data-ttu-id="3b7c4-108">Um componente de tabela que permite ao usuário especificar modelos para o cabeçalho, linhas e rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="3b7c4-109">Um componente de lista que permite ao usuário especificar um modelo para renderizar itens em uma lista.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="3b7c4-110">Parâmetros de modelo</span><span class="sxs-lookup"><span data-stu-id="3b7c4-110">Template parameters</span></span>

<span data-ttu-id="3b7c4-111">Um componente modelado é definido especificando um `RenderFragment` `RenderFragment<T>`ou mais parâmetros de componentes do tipo ou .</span><span class="sxs-lookup"><span data-stu-id="3b7c4-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="3b7c4-112">Um fragmento de renderização representa um segmento de IU para renderizar.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="3b7c4-113">`RenderFragment<T>`leva um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="3b7c4-114">`TableTemplate`Componente:</span><span class="sxs-lookup"><span data-stu-id="3b7c4-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="3b7c4-115">Ao usar um componente modelado, os parâmetros do modelo podem ser`TableHeader` `RowTemplate` especificados usando elementos infantis que correspondem aos nomes dos parâmetros (e no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="3b7c4-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="3b7c4-116">Restrições genéricas do tipo serão suportadas em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="3b7c4-117">Para obter mais informações, consulte [Permitir restrições genéricas de tipo (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="3b7c4-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="3b7c4-118">Parâmetros de contexto de modelo</span><span class="sxs-lookup"><span data-stu-id="3b7c4-118">Template context parameters</span></span>

<span data-ttu-id="3b7c4-119">Argumentos de `RenderFragment<T>` componentes do tipo passado `context` sao elementos têm um `@context.PetId`parâmetro implícito chamado (por `Context` exemplo, da amostra de código anterior), mas você pode alterar o nome do parâmetro usando o atributo no elemento filho.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="3b7c4-120">No exemplo a `RowTemplate` seguir, `Context` o atributo `pet` do elemento especifica o parâmetro:</span><span class="sxs-lookup"><span data-stu-id="3b7c4-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="3b7c4-121">Alternativamente, você pode `Context` especificar o atributo no elemento componente.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="3b7c4-122">O atributo especificado `Context` aplica-se a todos os parâmetros de modelo especificados.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="3b7c4-123">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo implícito da criança (sem qualquer elemento criança embrulhador).</span><span class="sxs-lookup"><span data-stu-id="3b7c4-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="3b7c4-124">No exemplo a `Context` seguir, o `TableTemplate` atributo aparece no elemento e se aplica a todos os parâmetros do modelo:</span><span class="sxs-lookup"><span data-stu-id="3b7c4-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="3b7c4-125">Componentes do tipo genérico</span><span class="sxs-lookup"><span data-stu-id="3b7c4-125">Generic-typed components</span></span>

<span data-ttu-id="3b7c4-126">Os componentes modelados são muitas vezes digitados genericamente.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-126">Templated components are often generically typed.</span></span> <span data-ttu-id="3b7c4-127">Por exemplo, `ListViewTemplate` um componente genérico `IEnumerable<T>` pode ser usado para renderizar valores.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="3b7c4-128">Para definir um componente [`@typeparam`](xref:mvc/views/razor#typeparam) genérico, use a diretiva para especificar parâmetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="3b7c4-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="3b7c4-129">Ao usar componentes digitados por genéricos, o parâmetro de tipo é inferido, se possível:</span><span class="sxs-lookup"><span data-stu-id="3b7c4-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="3b7c4-130">Caso contrário, o parâmetro de tipo deve ser explicitamente especificado usando um atributo que corresponda ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="3b7c4-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="3b7c4-131">No exemplo a `TItem="Pet"` seguir, especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="3b7c4-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
