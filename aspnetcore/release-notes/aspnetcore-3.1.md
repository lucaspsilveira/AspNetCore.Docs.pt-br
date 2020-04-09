---
title: O que há de novo no ASP.NET Core 3.1
author: rick-anderson
description: Conheça os novos recursos do ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662704"
---
# <a name="whats-new-in-aspnet-core-31"></a>O que há de novo no ASP.NET Core 3.1

Este artigo destaca as mudanças mais significativas no ASP.NET Núcleo 3.1 com links para documentação relevante.

## <a name="partial-class-support-for-razor-components"></a>Suporte parcial da classe para componentes razor

Os componentes da navalha são agora gerados como classes parciais. O código para um componente Razor pode ser escrito usando um arquivo por trás do código definido como uma classe parcial, em vez de definir todo o código para o componente em um único arquivo. Para obter mais informações, consulte [Suporte parcial à classe](xref:blazor/components#partial-class-support).

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorAjuda de tag componente e passar parâmetros para componentes de nível superior

Com Blazor ASP.NET Core 3.0, os componentes foram renderizados em`Html.RenderComponentAsync`páginas e visualizações usando um HTML Helper ( ). Em ASP.NET Núcleo 3.1, renderize um componente de uma página ou exibição com o novo Ajudante de Tag componente:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

O Ajudador HTML permanece suportado em ASP.NET Núcleo 3.1, mas o Component Tag Helper é recomendado.

BlazorOs aplicativos do servidor agora podem passar parâmetros para componentes de nível superior durante a renderização inicial. Anteriormente, você só podia passar parâmetros para um componente de nível superior com [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Com esta versão, tanto [renderMode.server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) quanto [renderModel.serverPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) são suportados. Quaisquer valores de parâmetro especificados são serializados como JSON e incluídos na resposta inicial.

Por exemplo, pré-renderizar um `Counter` `IncrementAmount`componente com uma quantidade de incremento ( ):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Para obter mais informações, consulte [Integrar componentes em páginas de barbear e aplicativos MVC](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>Suporte para filas compartilhadas em HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) suporta a criação de filas de solicitação anônimas. Em ASP.NET Núcleo 3.1, adicionamos a capacidade de criar ou anexar a uma fila de solicitação existente chamada HTTP.sys. Criar ou anexar a uma fila de solicitação existente chamada HTTP.sys habilita cenários em que o processo de controlador HTTP.sys que possui a fila é independente do processo de ouvinte. Essa independência torna possível preservar as conexões existentes e as solicitações enfileiradas entre as reinicializações do processo de ouvinte:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Quebrando alterações para cookies do SameSite

O comportamento dos cookies do SameSite mudou para refletir as próximas alterações do navegador. Isso pode afetar cenários de autenticação como AzureAd, OpenIdConnect ou WsFederation. Para obter mais informações, consulte <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Evite ações padrão Blazor para eventos em aplicativos

Use `@on{EVENT}:preventDefault` o atributo diretiva para impedir a ação padrão de um evento. No exemplo a seguir, a ação padrão de exibir o caractere da chave na caixa de texto é impedida:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Para obter mais informações, consulte [Prevenir ações padrão](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Parar a propagação de eventos em Blazor aplicativos

Use `@on{EVENT}:stopPropagation` o atributo diretiva para interromper a propagação do evento. No exemplo a seguir, selecionar a caixa de `<div>` seleção impede `<div>`que eventos de clique da criança se prossigam para o pai :

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Para obter mais informações, consulte [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Erros detalhados durante o Blazor desenvolvimento do aplicativo

Quando Blazor um aplicativo não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema. Quando ocorre um Blazor erro, os aplicativos exibem uma barra de ouro na parte inferior da tela:

* Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.
* Na produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.

Para obter mais informações, consulte [Erros detalhados durante o desenvolvimento](xref:blazor/handle-errors#detailed-errors-during-development).
