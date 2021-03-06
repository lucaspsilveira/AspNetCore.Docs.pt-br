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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/templated-components
ms.openlocfilehash: eaa04b3f5a193b00619849d5525ea8d721ace71f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400945"
---
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET Core Blazor componentes modelados

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente. Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais. Alguns exemplos incluem:

* Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.
* Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.

## <a name="template-parameters"></a>Parâmetros de modelo

Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo <xref:Microsoft.AspNetCore.Components.RenderFragment> ou <xref:Microsoft.AspNetCore.Components.RenderFragment%601> . Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado. <xref:Microsoft.AspNetCore.Components.RenderFragment%601>usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.

`TableTemplate`componente

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros ( `TableHeader` e `RowTemplate` no exemplo a seguir):

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
> As restrições de tipo genérico terão suporte em uma versão futura. Para obter mais informações, consulte [permitir restrições de tipo genérico (#8433 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Parâmetros de contexto de modelo

Argumentos de componente do tipo <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passado como elementos têm um parâmetro implícito chamado `context` (por exemplo, do exemplo de código anterior, `@context.PetId` ), mas você pode alterar o nome do parâmetro usando o `Context` atributo no elemento filho. No exemplo a seguir, o `RowTemplate` atributo do elemento `Context` especifica o `pet` parâmetro:

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

Como alternativa, você pode especificar o `Context` atributo no elemento Component. O `Context` atributo especificado se aplica a todos os parâmetros de modelo especificados. Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição). No exemplo a seguir, o `Context` atributo aparece no `TableTemplate` elemento e se aplica a todos os parâmetros de modelo:

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

## <a name="generic-typed-components"></a>Componentes de tipo genérico

Os componentes modelo são geralmente digitados genericamente. Por exemplo, um `ListViewTemplate` componente genérico pode ser usado para renderizar `IEnumerable<T>` valores. Para definir um componente genérico, use a [`@typeparam`](xref:mvc/views/razor#typeparam) diretiva para especificar parâmetros de tipo:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo. No exemplo a seguir, `TItem="Pet"` especifica o tipo:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
