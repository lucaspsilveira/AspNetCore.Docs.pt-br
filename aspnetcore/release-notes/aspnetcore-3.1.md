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
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="1885d-103">O que há de novo no ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="1885d-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="1885d-104">Este artigo destaca as mudanças mais significativas no ASP.NET Núcleo 3.1 com links para documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="1885d-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="1885d-105">Suporte parcial da classe para componentes razor</span><span class="sxs-lookup"><span data-stu-id="1885d-105">Partial class support for Razor components</span></span>

<span data-ttu-id="1885d-106">Os componentes da navalha são agora gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="1885d-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="1885d-107">O código para um componente Razor pode ser escrito usando um arquivo por trás do código definido como uma classe parcial, em vez de definir todo o código para o componente em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="1885d-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="1885d-108">Para obter mais informações, consulte [Suporte parcial à classe](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="1885d-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="1885d-109">Ajuda de tag componente e passar parâmetros para componentes de nível superior</span><span class="sxs-lookup"><span data-stu-id="1885d-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="1885d-110">Com Blazor ASP.NET Core 3.0, os componentes foram renderizados em`Html.RenderComponentAsync`páginas e visualizações usando um HTML Helper ( ).</span><span class="sxs-lookup"><span data-stu-id="1885d-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="1885d-111">Em ASP.NET Núcleo 3.1, renderize um componente de uma página ou exibição com o novo Ajudante de Tag componente:</span><span class="sxs-lookup"><span data-stu-id="1885d-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="1885d-112">O Ajudador HTML permanece suportado em ASP.NET Núcleo 3.1, mas o Component Tag Helper é recomendado.</span><span class="sxs-lookup"><span data-stu-id="1885d-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="1885d-113">Os aplicativos do servidor agora podem passar parâmetros para componentes de nível superior durante a renderização inicial.</span><span class="sxs-lookup"><span data-stu-id="1885d-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="1885d-114">Anteriormente, você só podia passar parâmetros para um componente de nível superior com [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="1885d-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="1885d-115">Com esta versão, tanto [renderMode.server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) quanto [renderModel.serverPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) são suportados.</span><span class="sxs-lookup"><span data-stu-id="1885d-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="1885d-116">Quaisquer valores de parâmetro especificados são serializados como JSON e incluídos na resposta inicial.</span><span class="sxs-lookup"><span data-stu-id="1885d-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="1885d-117">Por exemplo, pré-renderizar um `Counter` `IncrementAmount`componente com uma quantidade de incremento ( ):</span><span class="sxs-lookup"><span data-stu-id="1885d-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="1885d-118">Para obter mais informações, consulte [Integrar componentes em páginas de barbear e aplicativos MVC](xref:blazor/integrate-components).</span><span class="sxs-lookup"><span data-stu-id="1885d-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="1885d-119">Suporte para filas compartilhadas em HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1885d-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="1885d-120">[HTTP.sys](xref:fundamentals/servers/httpsys) suporta a criação de filas de solicitação anônimas.</span><span class="sxs-lookup"><span data-stu-id="1885d-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="1885d-121">Em ASP.NET Núcleo 3.1, adicionamos a capacidade de criar ou anexar a uma fila de solicitação existente chamada HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1885d-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="1885d-122">Criar ou anexar a uma fila de solicitação existente chamada HTTP.sys habilita cenários em que o processo de controlador HTTP.sys que possui a fila é independente do processo de ouvinte.</span><span class="sxs-lookup"><span data-stu-id="1885d-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="1885d-123">Essa independência torna possível preservar as conexões existentes e as solicitações enfileiradas entre as reinicializações do processo de ouvinte:</span><span class="sxs-lookup"><span data-stu-id="1885d-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="1885d-124">Quebrando alterações para cookies do SameSite</span><span class="sxs-lookup"><span data-stu-id="1885d-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="1885d-125">O comportamento dos cookies do SameSite mudou para refletir as próximas alterações do navegador.</span><span class="sxs-lookup"><span data-stu-id="1885d-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="1885d-126">Isso pode afetar cenários de autenticação como AzureAd, OpenIdConnect ou WsFederation.</span><span class="sxs-lookup"><span data-stu-id="1885d-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="1885d-127">Para obter mais informações, consulte <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="1885d-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="1885d-128">Evite ações padrão Blazor para eventos em aplicativos</span><span class="sxs-lookup"><span data-stu-id="1885d-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="1885d-129">Use `@on{EVENT}:preventDefault` o atributo diretiva para impedir a ação padrão de um evento.</span><span class="sxs-lookup"><span data-stu-id="1885d-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="1885d-130">No exemplo a seguir, a ação padrão de exibir o caractere da chave na caixa de texto é impedida:</span><span class="sxs-lookup"><span data-stu-id="1885d-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="1885d-131">Para obter mais informações, consulte [Prevenir ações padrão](xref:blazor/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="1885d-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="1885d-132">Parar a propagação de eventos em Blazor aplicativos</span><span class="sxs-lookup"><span data-stu-id="1885d-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="1885d-133">Use `@on{EVENT}:stopPropagation` o atributo diretiva para interromper a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="1885d-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="1885d-134">No exemplo a seguir, selecionar a caixa de `<div>` seleção impede `<div>`que eventos de clique da criança se prossigam para o pai :</span><span class="sxs-lookup"><span data-stu-id="1885d-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="1885d-135">Para obter mais informações, consulte [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="1885d-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="1885d-136">Erros detalhados durante o Blazor desenvolvimento do aplicativo</span><span class="sxs-lookup"><span data-stu-id="1885d-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="1885d-137">Quando Blazor um aplicativo não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema.</span><span class="sxs-lookup"><span data-stu-id="1885d-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="1885d-138">Quando ocorre um Blazor erro, os aplicativos exibem uma barra de ouro na parte inferior da tela:</span><span class="sxs-lookup"><span data-stu-id="1885d-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="1885d-139">Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.</span><span class="sxs-lookup"><span data-stu-id="1885d-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="1885d-140">Na produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.</span><span class="sxs-lookup"><span data-stu-id="1885d-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="1885d-141">Para obter mais informações, consulte [Erros detalhados durante o desenvolvimento](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="1885d-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
