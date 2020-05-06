---
title: Layouts de ASP.NET Core Blazor
author: guardrex
description: Saiba como criar componentes de layout reutilizáveis Blazor para aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5c6771dd7249bfb8280ba20e1ce75967f279971c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771579"
---
# <a name="aspnet-core-blazor-layouts"></a>Layouts de ASP.NET Core Blazor

Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)

Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo. Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem&mdash;eficiente toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado. Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo. Os *layouts* resolvem esse problema.

Tecnicamente, um layout é apenas outro componente. Um layout é definido em um Razor modelo ou em código C# e pode usar [Associação de dados](xref:blazor/data-binding), [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componente.

Para transformar um *componente* em um *layout*, o componente:

* Herda de `LayoutComponentBase`, que define uma `Body` propriedade para o conteúdo renderizado dentro do layout.
* Usa a Razor sintaxe `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.

O exemplo de código a seguir Razor mostra o modelo de um componente de layout, *MainLayout. Razor*. O layout herda `LayoutComponentBase` e define o `@Body` entre a barra de navegação e o rodapé:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Em um aplicativo baseado em um dos modelos Blazor de aplicativo, o `MainLayout` componente (*MainLayout. Razor*) está na pasta *compartilhada* do aplicativo.

## <a name="default-layout"></a>Layout padrão

Especifique o layout do aplicativo padrão no `Router` componente no arquivo *app. Razor* do aplicativo. O componente `Router` a seguir, que é fornecido pelos modelos Blazor padrão, define o layout padrão para o `MainLayout` componente:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Para fornecer um layout padrão para `NotFound` conteúdo, especifique um `LayoutView` para `NotFound` o conteúdo:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Para obter mais informações sobre `Router` o componente, <xref:blazor/routing>consulte.

A especificação do layout como um layout padrão no roteador é uma prática útil, pois pode ser substituída por componente ou por pasta. Prefira usar o roteador para definir o layout padrão do aplicativo porque ele é a técnica mais geral.

## <a name="specify-a-layout-in-a-component"></a>Especificar um layout em um componente

Use a Razor diretiva `@layout` para aplicar um layout a um componente. O compilador converte `@layout` em um `LayoutAttribute`, que é aplicado à classe de componente.

O conteúdo do componente a `MasterList` seguir é inserido no `MasterLayout` na posição de: `@Body`

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Especificar o layout diretamente em um componente substitui um *Layout padrão* definido no roteador ou uma `@layout` diretiva importada de *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Seleção de layout centralizado

Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *_Imports. Razor*. O compilador inclui as diretivas especificadas no arquivo Imports em todos os Razor modelos na mesma pasta e recursivamente em todas as suas subpastas. Portanto, um arquivo *_Imports. Razor* que `@layout MyCoolLayout` contém garante que todos os componentes em uma pasta usem `MyCoolLayout`o. Não há necessidade de adicionar `@layout MyCoolLayout` repetidamente a todos os arquivos *. Razor* dentro da pasta e subpastas. `@using`as diretivas também são aplicadas aos componentes da mesma maneira.

As seguintes importações de arquivo *_Imports. Razor* :

* `MyCoolLayout`.
* Todos Razor os componentes na mesma pasta e em todas as subpastas.
* O namespace `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

O arquivo *_Imports. Razor* é semelhante ao [arquivo _ViewImports. cshtml para Razor exibições e páginas](xref:mvc/views/layout#importing-shared-directives) , mas é aplicado especificamente a arquivos de Razor componente.

A especificação de um layout em *_Imports. Razor* substitui um layout especificado como o *Layout padrão*do roteador.

## <a name="nested-layouts"></a>Layouts aninhados

Os aplicativos podem consistir em layouts aninhados. Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout. Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.

O exemplo a seguir mostra como usar layouts aninhados. O arquivo *EpisodesComponent. Razor* é o componente a ser exibido. O componente faz referência `MasterListLayout`a:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

O arquivo *MasterListLayout. Razor* fornece o `MasterListLayout`. O layout faz referência a outro `MasterLayout`layout,, onde é renderizado. `EpisodesComponent`é renderizado `@Body` onde aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Finalmente, `MasterLayout` no *MasterLayout. Razor* contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé. `MasterListLayout`com o `EpisodesComponent` é renderizado `@Body` onde aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Compartilhar um Razor layout de páginas com componentes integrados

Quando componentes roteáveis são integrados Razor a um aplicativo de páginas, o layout compartilhado do aplicativo pode ser usado com os componentes. Para obter mais informações, consulte <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/layout>
