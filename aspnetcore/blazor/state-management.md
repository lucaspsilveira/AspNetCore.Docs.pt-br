---
title: ASP.NET Blazor Core gestão estadual
author: guardrex
description: Aprenda a persistir Blazor o estado em aplicativos do Servidor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218863"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="8f9c7-103">ASP.NET Blazor Core gestão estadual</span><span class="sxs-lookup"><span data-stu-id="8f9c7-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="8f9c7-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="8f9c7-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="8f9c7-105">Server é uma estrutura de aplicativo imponente.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="8f9c7-106">Na maioria das vezes, o aplicativo mantém uma conexão contínua com o servidor.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="8f9c7-107">O estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="8f9c7-108">Exemplos de estado mantidos para o circuito de um usuário incluem:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="8f9c7-109">A ui&mdash;renderizada a hierarquia de instâncias componentes e sua saída de renderização mais recente.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="8f9c7-110">Os valores de quaisquer campos e propriedades em instâncias componentes.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="8f9c7-111">Dados mantidos em instâncias de serviço [de injeção de dependência (DI)](xref:fundamentals/dependency-injection) que são escopo para o circuito.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="8f9c7-112">Este artigo aborda a Blazor persistência do estado em aplicativos do Servidor.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="8f9c7-113">Os aplicativos WebAssembly podem tirar proveito da persistência do estado do lado do [cliente no navegador,](#client-side-in-the-browser) mas exigem soluções personalizadas ou pacotes de terceiros além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="8f9c7-114">Circuitos</span><span class="sxs-lookup"><span data-stu-id="8f9c7-114"> circuits</span></span>

<span data-ttu-id="8f9c7-115">Se um usuário tiver uma Blazor perda temporária de conexão de rede, tente reconectar o usuário ao seu circuito original para que ele possa continuar a usar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="8f9c7-116">No entanto, nem sempre é possível reconectar um usuário ao seu circuito original na memória do servidor:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="8f9c7-117">O servidor não pode reter um circuito desconectado para sempre.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="8f9c7-118">O servidor deve liberar um circuito desconectado após um intervalo ou quando o servidor estiver sob pressão de memória.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="8f9c7-119">Em ambientes de implantação multiservidor e balanceados de carga, quaisquer solicitações de processamento de servidor podem ficar indisponíveis a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="8f9c7-120">Servidores individuais podem falhar ou ser removidos automaticamente quando não forem mais necessários para lidar com o volume total de solicitações.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="8f9c7-121">O servidor original pode não estar disponível quando o usuário tentar se reconectar.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="8f9c7-122">O usuário pode fechar e reabrir seu navegador ou recarregar a página, que remove qualquer estado mantido na memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="8f9c7-123">Por exemplo, os valores definidos através de chamadas interop JavaScript são perdidos.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="8f9c7-124">Quando um usuário não pode ser reconectado ao seu circuito original, o usuário recebe um novo circuito com um estado vazio.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="8f9c7-125">Isso equivale a fechar e reabrir um aplicativo de desktop.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="8f9c7-126">Preservar o estado em circuitos</span><span class="sxs-lookup"><span data-stu-id="8f9c7-126">Preserve state across circuits</span></span>

<span data-ttu-id="8f9c7-127">Em alguns cenários, preservar o estado entre circuitos é desejável.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="8f9c7-128">Um aplicativo pode reter dados importantes para um usuário se:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="8f9c7-129">O servidor web fica indisponível.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="8f9c7-130">O navegador do usuário é forçado a iniciar um novo circuito com um novo servidor web.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="8f9c7-131">Em geral, manter o estado entre circuitos se aplica a cenários onde os usuários estão criando dados ativamente, não simplesmente lendo dados que já existem.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="8f9c7-132">Para preservar o estado além de um único circuito, *não apenas armazene os dados na memória do servidor*.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="8f9c7-133">O aplicativo deve persistir os dados em algum outro local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="8f9c7-134">A persistência do estado&mdash;não é automática, você deve tomar medidas ao desenvolver o aplicativo para implementar a persistência de dados imponentes.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="8f9c7-135">A persistência de dados é normalmente necessária apenas para um estado de alto valor que os usuários gastaram esforço para criar.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="8f9c7-136">Nos exemplos a seguir, o estado persistente economiza tempo ou auxilia em atividades comerciais:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="8f9c7-137">Webform multistep &ndash; É demorado para um usuário reinserir dados para várias etapas concluídas de um processo multipasso se seu estado for perdido.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="8f9c7-138">Um usuário perde o estado neste cenário se navegar para longe da forma multipasso e retornar ao formulário mais tarde.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="8f9c7-139">Carrinho &ndash; de compras Qualquer componente comercialmente importante de um aplicativo que represente receita potencial pode ser mantido.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="8f9c7-140">Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando retornar ao site mais tarde.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="8f9c7-141">Normalmente não é necessário preservar o estado facilmente recriado, como o nome de usuário inserido em uma caixa de diálogo de login que não foi enviada.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8f9c7-142">Um aplicativo só pode persistir *o estado do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-142">An app can only persist *app state*.</span></span> <span data-ttu-id="8f9c7-143">UIs não podem ser persistidos, como instâncias de componentes e suas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="8f9c7-144">Componentes e árvores de renderização não são geralmente serializáveis.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="8f9c7-145">Para persistir algo semelhante ao estado de UI, como os nódulos expandidos de um TreeView, o aplicativo deve ter código personalizado para modelar o comportamento como estado de aplicativo serializável.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="8f9c7-146">Onde persistir estado</span><span class="sxs-lookup"><span data-stu-id="8f9c7-146">Where to persist state</span></span>

<span data-ttu-id="8f9c7-147">Existem três locais comuns Blazor para o estado persistente em um aplicativo server.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="8f9c7-148">Cada abordagem é mais adequada para diferentes cenários e tem diferentes ressalvas:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="8f9c7-149">Lado do servidor em um banco de dados</span><span class="sxs-lookup"><span data-stu-id="8f9c7-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="8f9c7-150">URL</span><span class="sxs-lookup"><span data-stu-id="8f9c7-150">URL</span></span>](#url)
* [<span data-ttu-id="8f9c7-151">Lado do cliente no navegador</span><span class="sxs-lookup"><span data-stu-id="8f9c7-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="8f9c7-152">Lado do servidor em um banco de dados</span><span class="sxs-lookup"><span data-stu-id="8f9c7-152">Server-side in a database</span></span>

<span data-ttu-id="8f9c7-153">Para persistência permanente de dados ou para quaisquer dados que devem abranger vários usuários ou dispositivos, um banco de dados independente do lado do servidor é quase certamente a melhor escolha.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="8f9c7-154">As opções incluem:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-154">Options include:</span></span>

* <span data-ttu-id="8f9c7-155">Banco de dados SQL relacional</span><span class="sxs-lookup"><span data-stu-id="8f9c7-155">Relational SQL database</span></span>
* <span data-ttu-id="8f9c7-156">Repositório de valor-chave</span><span class="sxs-lookup"><span data-stu-id="8f9c7-156">Key-value store</span></span>
* <span data-ttu-id="8f9c7-157">Loja Blob</span><span class="sxs-lookup"><span data-stu-id="8f9c7-157">Blob store</span></span>
* <span data-ttu-id="8f9c7-158">Loja de mesa</span><span class="sxs-lookup"><span data-stu-id="8f9c7-158">Table store</span></span>

<span data-ttu-id="8f9c7-159">Depois que os dados são salvos no banco de dados, um novo circuito pode ser iniciado por um usuário a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="8f9c7-160">Os dados do usuário são retidos e disponíveis em qualquer novo circuito.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="8f9c7-161">Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte a Documentação de armazenamento do [Azure](/azure/storage/) e [os bancos de dados do Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="8f9c7-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="8f9c7-162">URL</span><span class="sxs-lookup"><span data-stu-id="8f9c7-162">URL</span></span>

<span data-ttu-id="8f9c7-163">Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="8f9c7-164">Exemplos de estado modelado na URL incluem:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="8f9c7-165">O ID de uma entidade vista.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="8f9c7-166">O número da página atual em uma grade bicada.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="8f9c7-167">O conteúdo da barra de endereços do navegador é retido:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="8f9c7-168">Se o usuário recarregar manualmente a página.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="8f9c7-169">Se o servidor&mdash;web ficar indisponível, o usuário será forçado a recarregar a página para se conectar a um servidor diferente.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="8f9c7-170">Para obter informações sobre `@page` como definir <xref:blazor/routing>padrões de URL com a diretiva, consulte .</span><span class="sxs-lookup"><span data-stu-id="8f9c7-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="8f9c7-171">Lado do cliente no navegador</span><span class="sxs-lookup"><span data-stu-id="8f9c7-171">Client-side in the browser</span></span>

<span data-ttu-id="8f9c7-172">Para dados transitórios que o usuário está criando ativamente, `localStorage` uma `sessionStorage` loja de backup comum é a do navegador e as coleções.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="8f9c7-173">O aplicativo não é necessário para gerenciar ou limpar o estado armazenado se o circuito for abandonado, o que é uma vantagem sobre o armazenamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="8f9c7-174">"Lado do cliente" nesta seção refere-se a cenários do lado do cliente no navegador, não ao [ Blazor modelo de hospedagem WebAssembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="8f9c7-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="8f9c7-175">`localStorage`e `sessionStorage` pode ser Blazor usado em aplicativos WebAssembly, mas apenas escrevendo código personalizado ou usando um pacote de terceiros.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="8f9c7-176">`localStorage`e `sessionStorage` diferem da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="8f9c7-177">`localStorage`é escopo para o navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="8f9c7-178">Se o usuário recarregar a página ou fechar e reabrir o navegador, o estado persistirá.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="8f9c7-179">Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="8f9c7-180">Os dados `localStorage` persistem até serem explicitamente limpos.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="8f9c7-181">`sessionStorage`é escopo para a guia do navegador do usuário. Se o usuário recarregar a guia, o estado persistirá.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="8f9c7-182">Se o usuário fechar a guia ou o navegador, o estado será perdido.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="8f9c7-183">Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="8f9c7-184">Geralmente, `sessionStorage` é mais seguro usar.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="8f9c7-185">`sessionStorage`evita o risco de que um usuário abra várias guias e encontre o seguinte:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="8f9c7-186">Bugs no armazenamento do estado em guias.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="8f9c7-187">Comportamento confuso quando uma guia substitui o estado de outras guias.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="8f9c7-188">`localStorage`é a melhor escolha se o aplicativo deve persistir o estado através do fechamento e reabertura do navegador.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="8f9c7-189">Ressalvas para o uso do armazenamento do navegador:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="8f9c7-190">Semelhante ao uso de um banco de dados do lado do servidor, o carregamento e a salvação de dados são assíncronos.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="8f9c7-191">Ao contrário de um banco de dados do lado do servidor, o armazenamento não está disponível durante a pré-renderização porque a página solicitada não existe no navegador durante a fase de pré-renderização.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="8f9c7-192">O armazenamento de alguns kilobytes de Blazor dados é razoável para persistir para aplicativos do Server.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="8f9c7-193">Além de alguns kilobytes, você deve considerar as implicações de desempenho porque os dados são carregados e salvos em toda a rede.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="8f9c7-194">Os usuários podem visualizar ou adulterar os dados.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="8f9c7-195">ASP.NET [Proteção de Dados Do](xref:security/data-protection/introduction) Núcleo pode mitigar o risco.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="8f9c7-196">Soluções de armazenamento de navegador de terceiros</span><span class="sxs-lookup"><span data-stu-id="8f9c7-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="8f9c7-197">Pacotes NuGet de terceiros fornecem APIs para trabalhar com `localStorage` e `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="8f9c7-198">Vale a pena considerar a escolha de um pacote que use de forma transparente ASP.NET [Proteção](xref:security/data-protection/introduction)de Dados do Core.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="8f9c7-199">ASP.NET O Core Data Protection criptografa os dados armazenados e reduz o risco potencial de adulteração de dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="8f9c7-200">Se os dados serializados do JSON forem armazenados em texto simples, os usuários poderão ver os dados usando ferramentas de desenvolvedor do navegador e também modificar os dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="8f9c7-201">Proteger dados nem sempre é um problema porque os dados podem ser triviais por natureza.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="8f9c7-202">Por exemplo, ler ou modificar a cor armazenada de um elemento de ida e preço não é um risco significativo de segurança para o usuário ou a organização.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="8f9c7-203">Evite permitir que os usuários inspecionem ou alterem *dados confidenciais.*</span><span class="sxs-lookup"><span data-stu-id="8f9c7-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="8f9c7-204">Pacote experimental de armazenamento de navegador protegido</span><span class="sxs-lookup"><span data-stu-id="8f9c7-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="8f9c7-205">Um exemplo de um pacote NuGet `localStorage` `sessionStorage` que fornece [proteção](xref:security/data-protection/introduction) de dados para e é [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="8f9c7-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="8f9c7-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`é um pacote experimental sem suporte inadequado para uso de produção neste momento.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="8f9c7-207">Instalação</span><span class="sxs-lookup"><span data-stu-id="8f9c7-207">Installation</span></span>

<span data-ttu-id="8f9c7-208">Para instalar `Microsoft.AspNetCore.ProtectedBrowserStorage` o pacote:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="8f9c7-209">No Blazor projeto do aplicativo Server, adicione uma referência de pacote ao [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="8f9c7-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="8f9c7-210">No HTML de nível superior (por exemplo, no arquivo *Páginas/_Host.cshtml* no `<script>` modelo padrão do projeto), adicione a seguinte tag:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="8f9c7-211">No `Startup.ConfigureServices` método, `AddProtectedBrowserStorage` chamada `localStorage` para `sessionStorage` adicionar e serviços à coleta de serviços:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="8f9c7-212">Salvar e carregar dados dentro de um componente</span><span class="sxs-lookup"><span data-stu-id="8f9c7-212">Save and load data within a component</span></span>

<span data-ttu-id="8f9c7-213">Em qualquer componente que exija carregar ou [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) salvar dados no armazenamento do navegador, use para injetar uma instância de qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="8f9c7-214">A escolha depende de qual loja de apoio você deseja usar.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="8f9c7-215">No exemplo a `sessionStorage` seguir, é usado:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="8f9c7-216">A `@using` declaração pode ser colocada em um arquivo *_Imports.razor* em vez de no componente.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="8f9c7-217">O uso do arquivo *_Imports.razor* disponibiliza o namespace para segmentos maiores do aplicativo ou de todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="8f9c7-218">Para persistir `_currentCount` o `Counter` valor no componente do `IncrementCount` modelo do `ProtectedSessionStore.SetAsync`projeto, modifique o método a ser usado:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="8f9c7-219">Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="8f9c7-220">Os aplicativos são mais propensos a armazenar objetos de modelo inteiros que incluem estado complexo.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="8f9c7-221">`ProtectedSessionStore`serializa e desserializa automaticamente os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="8f9c7-222">No exemplo de código `_currentCount` anterior, `sessionStorage['count']` os dados são armazenados como no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="8f9c7-223">Os dados não são armazenados em texto simples, mas são protegidos usando ASP.NET [Proteção](xref:security/data-protection/introduction)de Dados do Core .</span><span class="sxs-lookup"><span data-stu-id="8f9c7-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="8f9c7-224">Os dados criptografados `sessionStorage['count']` podem ser vistos se forem avaliados no console de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="8f9c7-225">Para recuperar `_currentCount` os dados se `Counter` o usuário retornar ao componente mais tarde (incluindo `ProtectedSessionStore.GetAsync`se eles estiverem em um circuito totalmente novo), use :</span><span class="sxs-lookup"><span data-stu-id="8f9c7-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="8f9c7-226">Se os parâmetros do componente `ProtectedSessionStore.GetAsync` incluem estado de `OnParametersSetAsync`navegação, `OnInitializedAsync`ligue e atribua o resultado em , não .</span><span class="sxs-lookup"><span data-stu-id="8f9c7-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="8f9c7-227">`OnInitializedAsync`é chamado apenas uma vez quando o componente é instanciado pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="8f9c7-228">`OnInitializedAsync`não é chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="8f9c7-229">Para obter mais informações, consulte <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="8f9c7-230">Os exemplos nesta seção só funcionam se o servidor não tiver pré-renderização ativada.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="8f9c7-231">Com a pré-renderização ativada, um erro é gerado semelhante a:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="8f9c7-232">As chamadas interop JavaScript não podem ser emitidas no momento.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="8f9c7-233">Isso porque o componente está sendo pré-renderizado.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="8f9c7-234">Desabilite a pré-renderização ou adicione código adicional para trabalhar com a pré-renderização.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="8f9c7-235">Para saber mais sobre a escrita de código que funciona com a pré-renderização, consulte a seção [De pré-renderização de Lidar.](#handle-prerendering)</span><span class="sxs-lookup"><span data-stu-id="8f9c7-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="8f9c7-236">Manuseie o estado de carga</span><span class="sxs-lookup"><span data-stu-id="8f9c7-236">Handle the loading state</span></span>

<span data-ttu-id="8f9c7-237">Como o armazenamento do navegador é assíncrono (acessado através de uma conexão de rede), há sempre um período de tempo antes que os dados sejam carregados e disponíveis para uso por um componente.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="8f9c7-238">Para obter os melhores resultados, renderize uma mensagem de estado de carga enquanto o carregamento estiver em andamento, em vez de exibir dados em branco ou padrão.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="8f9c7-239">Uma abordagem é rastrear `null` se os dados estão (ainda carregando) ou não.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="8f9c7-240">No componente `Counter` padrão, a contagem `int`é mantida em um .</span><span class="sxs-lookup"><span data-stu-id="8f9c7-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="8f9c7-241">Tornar `_currentCount` anulado adicionando um`?`ponto de interrogação ( ) ao tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="8f9c7-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="8f9c7-242">Em vez de exibir incondicionalmente o botão contagem e **incremento,** escolha exibir esses elementos somente se os dados forem carregados:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="8f9c7-243">Lidar com a pré-renderização</span><span class="sxs-lookup"><span data-stu-id="8f9c7-243">Handle prerendering</span></span>

<span data-ttu-id="8f9c7-244">Durante a pré-renderização:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-244">During prerendering:</span></span>

* <span data-ttu-id="8f9c7-245">Uma conexão interativa com o navegador do usuário não existe.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="8f9c7-246">O navegador ainda não tem uma página na qual pode executar o código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="8f9c7-247">`localStorage`ou `sessionStorage` não estão disponíveis durante a pré-renderização.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="8f9c7-248">Se o componente tentar interagir com o armazenamento, um erro será gerado semelhante a:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="8f9c7-249">As chamadas interop JavaScript não podem ser emitidas no momento.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="8f9c7-250">Isso porque o componente está sendo pré-renderizado.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="8f9c7-251">Uma maneira de resolver o erro é desativar a pré-renderização.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="8f9c7-252">Esta é geralmente a melhor escolha se o aplicativo faz uso pesado do armazenamento baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="8f9c7-253">A pré-renderização adiciona complexidade e não beneficia o aplicativo porque `localStorage` o `sessionStorage` aplicativo não pode pré-renderizar qualquer conteúdo útil até ou estar disponível.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="8f9c7-254">Para desativar a pré-renderização, abra o arquivo *Páginas/_Host.cshtml* e altere o `render-mode` do [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="8f9c7-255">A pré-renderização pode ser útil `localStorage` para `sessionStorage`outras páginas que não usam ou .</span><span class="sxs-lookup"><span data-stu-id="8f9c7-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="8f9c7-256">Para manter a pré-renderização ativada, adie a operação de carregamento até que o navegador esteja conectado ao circuito.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="8f9c7-257">A seguir, um exemplo para armazenar um contravalor:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="8f9c7-258">Fatorar a preservação do Estado para um local comum</span><span class="sxs-lookup"><span data-stu-id="8f9c7-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="8f9c7-259">Se muitos componentes dependem do armazenamento baseado no navegador, a reimplementação do código do provedor de estado muitas vezes cria duplicação de código.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="8f9c7-260">Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="8f9c7-261">Os componentes infantis podem trabalhar com dados persistentes sem considerar o mecanismo de persistência do estado.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="8f9c7-262">No exemplo a `CounterStateProvider` seguir de um componente, os contra-dados são persistidos:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="8f9c7-263">O `CounterStateProvider` componente lida com a fase de carregamento não renderizando seu conteúdo filho até que o carregamento esteja concluído.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="8f9c7-264">Para usar `CounterStateProvider` o componente, enrole uma instância do componente em torno de qualquer outro componente que exija acesso ao estado de contador.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="8f9c7-265">Para tornar o estado acessível a todos `CounterStateProvider` os componentes `App` de um aplicativo, enrole o componente em torno do `Router` componente *(App.razor):*</span><span class="sxs-lookup"><span data-stu-id="8f9c7-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="8f9c7-266">Os componentes embrulhados recebem e podem modificar o estado de contra-ataque persistido.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="8f9c7-267">O `Counter` seguinte componente implementa o padrão:</span><span class="sxs-lookup"><span data-stu-id="8f9c7-267">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="8f9c7-268">O componente anterior não é `ProtectedBrowserStorage`necessário para interagir, nem lida com uma fase de "carregamento".</span><span class="sxs-lookup"><span data-stu-id="8f9c7-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="8f9c7-269">Para lidar com a pré-renderização como descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consomem os dados de contador trabalhem automaticamente com a pré-renderização.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="8f9c7-270">Consulte a seção [De pré-renderização handle](#handle-prerendering) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="8f9c7-271">Em geral, recomenda-se o padrão *de componente-pai do provedor de estado:*</span><span class="sxs-lookup"><span data-stu-id="8f9c7-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="8f9c7-272">Para consumir estado em muitos outros componentes.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-272">To consume state in many other components.</span></span>
* <span data-ttu-id="8f9c7-273">Se há apenas um objeto de estado de alto nível para persistir.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="8f9c7-274">Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em diferentes lugares, é melhor evitar lidar com o carregamento e a economia do estado globalmente.</span><span class="sxs-lookup"><span data-stu-id="8f9c7-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
