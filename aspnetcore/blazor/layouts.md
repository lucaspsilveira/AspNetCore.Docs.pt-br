---
title: layouts Blazor do Núcleo ASP.NET
author: guardrex
description: Aprenda a criar componentes de Blazor layout reutilizáveis para aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660408"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>layouts Blazor do Núcleo ASP.NET

Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)

Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente fazem parte do layout geral do aplicativo e são usados por todos os componentes do aplicativo. Copiar o código desses elementos em todos os componentes de&mdash;um aplicativo não é uma abordagem eficiente toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado. Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo. *Layouts* resolvem esse problema.

Tecnicamente, um layout é apenas outro componente. Um layout é definido em um modelo de Navalha ou em código C# e pode usar [vinculação de dados,](xref:blazor/data-binding) [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componentes.

Para transformar um *componente* em um *layout,* o componente:

* Herda `LayoutComponentBase`, que define `Body` uma propriedade para o conteúdo renderizado dentro do layout.
* Usa a sintaxe `@Body` Razor para especificar o local na marcação do layout onde o conteúdo é renderizado.

A amostra de código a seguir mostra o modelo Razor de um componente de layout, *MainLayout.razor*. O layout `LayoutComponentBase` herda `@Body` e define entre a barra de navegação e o rodapé:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Em um aplicativo baseado Blazor em um dos `MainLayout` modelos de aplicativo, o componente (*MainLayout.razor*) está na pasta *Compartilhada* do aplicativo.

## <a name="default-layout"></a>Layout padrão

Especifique o `Router` layout padrão do aplicativo no componente no arquivo *App.razor* do aplicativo. O `Router` componente a seguir, fornecido Blazor pelos modelos padrão, define `MainLayout` o layout padrão para o componente:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Para fornecer um `NotFound` layout padrão `LayoutView` para `NotFound` conteúdo, especifique um para conteúdo:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Para obter mais `Router` informações <xref:blazor/routing>sobre o componente, consulte .

Especificar o layout como um layout padrão no roteador é uma prática útil porque ele pode ser substituído por componente ou por pasta. Prefira usar o roteador para definir o layout padrão do aplicativo porque é a técnica mais geral.

## <a name="specify-a-layout-in-a-component"></a>Especifique um layout em um componente

Use a `@layout` diretiva Razor para aplicar um layout a um componente. O compilador converte-se em um `@layout` `LayoutAttribute`, que é aplicado à classe de componentes.

O conteúdo do `MasterList` seguinte componente é `MasterLayout` inserido na `@Body`posição de :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Especificar o layout diretamente em um componente substitui um layout `@layout` *padrão* definido no roteador ou uma diretiva importada de *_Imports.razor*.

## <a name="centralized-layout-selection"></a>Seleção centralizada de layout

Cada pasta de um aplicativo pode conter opcionalmente um arquivo de modelo chamado *_Imports.razor*. O compilador inclui as diretivas especificadas no arquivo de importações em todos os modelos de Barbear na mesma pasta e recursivamente em todas as suas subpastas. Portanto, um *arquivo _Imports.razor* contendo `@layout MyCoolLayout` garante que todos os componentes em uma pasta utilizem `MyCoolLayout`. Não há necessidade de adicionar `@layout MyCoolLayout` repetidamente a todos os arquivos *.razor* dentro da pasta e subpastas. `@using`diretivas também são aplicadas aos componentes da mesma forma.

As seguintes importações de arquivos *_Imports.razor:*

* `MyCoolLayout`.
* Todos os componentes razor na mesma pasta e quaisquer subpastas.
* O namespace `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

O arquivo *_Imports.razor* é semelhante ao [arquivo _ViewImports.cshtml para visualizações e páginas de Razor,](xref:mvc/views/layout#importing-shared-directives) mas aplicado especificamente aos arquivos de componentes razor.

Especificar um layout em *_Imports.razor* substitui um layout especificado como layout padrão do *roteador*.

## <a name="nested-layouts"></a>Layouts aninhados

Os aplicativos podem consistir em layouts aninhados. Um componente pode referenciar um layout que, por sua vez, faz referência a outro layout. Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.

O exemplo a seguir mostra como usar layouts aninhados. O arquivo *EpisodesComponent.razor* é o componente a ser exibido. O componente faz `MasterListLayout`referência ao :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

O arquivo *MasterListLayout.razor* fornece o `MasterListLayout`. O layout faz referência `MasterLayout`a outro layout, onde é renderizado. `EpisodesComponent`é renderizado `@Body` onde aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Finalmente, `MasterLayout` em *MasterLayout.razor* contém os elementos de layout de nível superior, como o cabeçalho, menu principal e rodapé. `MasterListLayout`com `EpisodesComponent` o é `@Body` renderizado onde aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Compartilhe um layout de Páginas de Navalha com componentes integrados

Quando os componentes rotáveis são integrados em um aplicativo Razor Pages, o layout compartilhado do aplicativo pode ser usado com os componentes. Para obter mais informações, consulte <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/layout>
