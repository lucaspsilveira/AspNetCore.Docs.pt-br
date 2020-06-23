---
title: BlazorLayouts de ASP.NET Core
author: guardrex
description: Saiba como criar componentes de layout reutilizáveis para Blazor aplicativos.
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
ms.openlocfilehash: 6103a2e8173ccbb78372e01bd799d1bb47da4fa2
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243051"
---
# <a name="aspnet-core-blazor-layouts"></a>BlazorLayouts de ASP.NET Core

Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)

Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo. Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem eficiente. Toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado. Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo. Os *layouts* resolvem esse problema.

Tecnicamente, um layout é apenas outro componente. Um layout é definido em um Razor modelo ou em código C# e pode usar [Associação de dados](xref:blazor/components/data-binding), [injeção de dependência](xref:blazor/fundamentals/dependency-injection)e outros cenários de componente.

Para transformar um *componente* em um *layout*, o componente:

* Herda de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , que define uma <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriedade para o conteúdo renderizado dentro do layout.
* Usa a Razor sintaxe `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.

O exemplo de código a seguir mostra o Razor modelo de um componente de layout, `MainLayout.razor` . O layout herda <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> e define o `@Body` entre a barra de navegação e o rodapé:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Em um aplicativo baseado em um dos Blazor modelos de aplicativo, o `MainLayout` componente ( `MainLayout.razor` ) está na pasta do aplicativo `Shared` .

## <a name="default-layout"></a>Layout padrão

Especifique o layout do aplicativo padrão no <xref:Microsoft.AspNetCore.Components.Routing.Router> componente no arquivo do aplicativo `App.razor` . O componente a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router> , que é fornecido pelos Blazor modelos padrão, define o layout padrão para o `MainLayout` componente:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Para fornecer um layout padrão para <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, especifique um <xref:Microsoft.AspNetCore.Components.LayoutView> para o <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, consulte <xref:blazor/fundamentals/routing> .

A especificação do layout como um layout padrão no roteador é uma prática útil, pois pode ser substituída por componente ou por pasta. Prefira usar o roteador para definir o layout padrão do aplicativo porque ele é a técnica mais geral.

## <a name="specify-a-layout-in-a-component"></a>Especificar um layout em um componente

Use a Razor diretiva `@layout` para aplicar um layout a um componente. O compilador converte `@layout` em um <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , que é aplicado à classe de componente.

O conteúdo do componente a seguir `MasterList` é inserido no `MasterLayout` na posição de `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Especificar o layout diretamente em um componente substitui um *Layout padrão* definido no roteador ou uma `@layout` diretiva importada do `_Imports.razor` .

## <a name="centralized-layout-selection"></a>Seleção de layout centralizado

Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado `_Imports.razor` . O compilador inclui as diretivas especificadas no arquivo Imports em todos os Razor modelos na mesma pasta e recursivamente em todas as suas subpastas. Portanto, um `_Imports.razor` arquivo que contém `@layout MyCoolLayout` garante que todos os componentes em uma pasta usem o `MyCoolLayout` . Não há necessidade de adicionar repetidamente `@layout MyCoolLayout` a todos os `.razor` arquivos dentro da pasta e subpastas. `@using`as diretivas também são aplicadas aos componentes da mesma maneira.

As seguintes `_Imports.razor` importações de arquivo:

* `MyCoolLayout`.
* Todos os Razor componentes na mesma pasta e em todas as subpastas.
* O namespace `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

O `_Imports.razor` arquivo é semelhante ao [arquivo _ViewImports. cshtml para Razor exibições e páginas](xref:mvc/views/layout#importing-shared-directives) , mas é aplicado especificamente a Razor arquivos de componente.

A especificação de um layout em `_Imports.razor` substitui um layout especificado como o *Layout padrão*do roteador.

## <a name="nested-layouts"></a>Layouts aninhados

Os aplicativos podem consistir em layouts aninhados. Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout. Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.

O exemplo a seguir mostra como usar layouts aninhados. O `EpisodesComponent.razor` arquivo é o componente a ser exibido. O componente faz referência a `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

O `MasterListLayout.razor` arquivo fornece o `MasterListLayout` . O layout faz referência a outro layout, `MasterLayout` , onde é renderizado. `EpisodesComponent`é renderizado onde `@Body` aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Por fim, `MasterLayout` no `MasterLayout.razor` contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé. `MasterListLayout`com o `EpisodesComponent` é renderizado onde `@Body` aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Compartilhar um Razor layout de páginas com componentes integrados

Quando componentes roteáveis são integrados a um Razor aplicativo de páginas, o layout compartilhado do aplicativo pode ser usado com os componentes. Para obter mais informações, consulte <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/layout>
