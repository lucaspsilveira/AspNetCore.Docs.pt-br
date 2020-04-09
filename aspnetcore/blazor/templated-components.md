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
# <a name="aspnet-core-opno-locblazor-templated-components"></a>ASP.NET Blazor componentes modelados do Core

Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)

Os componentes modelados são componentes que aceitam um ou mais modelos de IA como parâmetros, que podem ser usados como parte da lógica de renderização do componente. Os componentes modelados permitem que você seja autor de componentes de nível superior que são mais reutilizáveis do que componentes regulares. Alguns exemplos incluem:

* Um componente de tabela que permite ao usuário especificar modelos para o cabeçalho, linhas e rodapé da tabela.
* Um componente de lista que permite ao usuário especificar um modelo para renderizar itens em uma lista.

## <a name="template-parameters"></a>Parâmetros de modelo

Um componente modelado é definido especificando um `RenderFragment` `RenderFragment<T>`ou mais parâmetros de componentes do tipo ou . Um fragmento de renderização representa um segmento de IU para renderizar. `RenderFragment<T>`leva um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.

`TableTemplate`Componente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Ao usar um componente modelado, os parâmetros do modelo podem ser`TableHeader` `RowTemplate` especificados usando elementos infantis que correspondem aos nomes dos parâmetros (e no exemplo a seguir):

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
> Restrições genéricas do tipo serão suportadas em uma versão futura. Para obter mais informações, consulte [Permitir restrições genéricas de tipo (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Parâmetros de contexto de modelo

Argumentos de `RenderFragment<T>` componentes do tipo passado `context` sao elementos têm um `@context.PetId`parâmetro implícito chamado (por `Context` exemplo, da amostra de código anterior), mas você pode alterar o nome do parâmetro usando o atributo no elemento filho. No exemplo a `RowTemplate` seguir, `Context` o atributo `pet` do elemento especifica o parâmetro:

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

Alternativamente, você pode `Context` especificar o atributo no elemento componente. O atributo especificado `Context` aplica-se a todos os parâmetros de modelo especificados. Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo implícito da criança (sem qualquer elemento criança embrulhador). No exemplo a `Context` seguir, o `TableTemplate` atributo aparece no elemento e se aplica a todos os parâmetros do modelo:

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

## <a name="generic-typed-components"></a>Componentes do tipo genérico

Os componentes modelados são muitas vezes digitados genericamente. Por exemplo, `ListViewTemplate` um componente genérico `IEnumerable<T>` pode ser usado para renderizar valores. Para definir um componente [`@typeparam`](xref:mvc/views/razor#typeparam) genérico, use a diretiva para especificar parâmetros de tipo:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Ao usar componentes digitados por genéricos, o parâmetro de tipo é inferido, se possível:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Caso contrário, o parâmetro de tipo deve ser explicitamente especificado usando um atributo que corresponda ao nome do parâmetro de tipo. No exemplo a `TItem="Pet"` seguir, especifica o tipo:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
