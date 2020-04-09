---
title: Lidar com erros Blazor em aplicativos ASP.NET Core
author: guardrex
description: Descubra como Blazor ASP.NET Blazor Core como gerencia exceções não tratadas e como desenvolver aplicativos que detectam e lidam com erros.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382269"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="6b1d3-103">Lidar com erros Blazor em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b1d3-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="6b1d3-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="6b1d3-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="6b1d3-105">Este artigo descreve Blazor como gerencia exceções não tratadas e como desenvolver aplicativos que detectam e lidam com erros.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="6b1d3-106">Erros detalhados durante o desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="6b1d3-106">Detailed errors during development</span></span>

<span data-ttu-id="6b1d3-107">Quando Blazor um aplicativo não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="6b1d3-108">Quando ocorre um Blazor erro, os aplicativos exibem uma barra de ouro na parte inferior da tela:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="6b1d3-109">Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="6b1d3-110">Na produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="6b1d3-111">A ui para esta experiência de Blazor manipulação de erros faz parte dos modelos do projeto.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="6b1d3-112">Em Blazor um aplicativo WebAssembly, personalize a experiência no arquivo *wwwroot/index.html:*</span><span class="sxs-lookup"><span data-stu-id="6b1d3-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="6b1d3-113">Em Blazor um aplicativo do Server, personalize a experiência no arquivo *Páginas/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6b1d3-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="6b1d3-114">O `blazor-error-ui` elemento é oculto pelos Blazor estilos incluídos nos modelos *(wwwroot/css/site.css)* e, em seguida, mostrado quando ocorre um erro:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="6b1d3-115">Como Blazor um aplicativo do Servidor reage a exceções não tratadas</span><span class="sxs-lookup"><span data-stu-id="6b1d3-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="6b1d3-116">Server é uma estrutura imponente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-116"> Server is a stateful framework.</span></span> <span data-ttu-id="6b1d3-117">Enquanto os usuários interagem com um aplicativo, eles mantêm uma conexão com o servidor conhecido como *circuito*.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="6b1d3-118">O circuito mantém instâncias de componentes ativos, além de muitos outros aspectos do estado, tais como:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="6b1d3-119">A mais recente saída renderizada de componentes.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="6b1d3-120">O conjunto atual de delegados de manipulação de eventos que poderia ser acionado por eventos do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="6b1d3-121">Se um usuário abrir o aplicativo em várias guias do navegador, ele terá vários circuitos independentes.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="6b1d3-122">trata a maioria das exceções não tratadas como fatais para o circuito onde ocorrem.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="6b1d3-123">Se um circuito for encerrado devido a uma exceção não tratada, o usuário só poderá continuar a interagir com o aplicativo recarregando a página para criar um novo circuito.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="6b1d3-124">Circuitos fora do que está encerrado, que são circuitos para outros usuários ou outras guias do navegador, não são afetados.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="6b1d3-125">Este cenário é semelhante a um&mdash;aplicativo de desktop que trava o aplicativo crash deve ser reiniciado, mas outros aplicativos não são afetados.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="6b1d3-126">Um circuito é encerrado quando ocorre uma exceção não manuseada pelas seguintes razões:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="6b1d3-127">Uma exceção não manuseada muitas vezes deixa o circuito em um estado indefinido.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="6b1d3-128">O funcionamento normal do aplicativo não pode ser garantido após uma exceção não tratada.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="6b1d3-129">As vulnerabilidades de segurança podem aparecer no aplicativo se o circuito continuar.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="6b1d3-130">Gerenciar exceções não tratadas no código do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="6b1d3-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="6b1d3-131">Para que um aplicativo continue após um erro, o aplicativo deve ter lógica de manipulação de erros.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="6b1d3-132">Seções posteriores deste artigo descrevem fontes potenciais de exceções não tratadas.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="6b1d3-133">Na produção, não torne mensagens de exceção de quadro ou empilhe traços na ui.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="6b1d3-134">Renderizar mensagens de exceção ou empilhar vestígios pode:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="6b1d3-135">Divulgar informações confidenciais aos usuários finais.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="6b1d3-136">Ajude um usuário mal-intencionado a descobrir fraquezas em um aplicativo que podem comprometer a segurança do aplicativo, servidor ou rede.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="6b1d3-137">Erros de log com um provedor persistente</span><span class="sxs-lookup"><span data-stu-id="6b1d3-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="6b1d3-138">Se ocorrer uma exceção não manuseada, a exceção será registrada em <xref:Microsoft.Extensions.Logging.ILogger> instâncias configuradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="6b1d3-139">Por padrão, Blazor os aplicativos registram a saída do console com o Provedor de Registro de Console.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="6b1d3-140">Considere fazer login em um local mais permanente com um provedor que gerencia o tamanho do registro e a rotação de log.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="6b1d3-141">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="6b1d3-142">Durante o Blazor desenvolvimento, geralmente envia todos os detalhes de exceções ao console do navegador para ajudar na depuração.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="6b1d3-143">Na produção, erros detalhados no console do navegador são desativados por padrão, o que significa que os erros não são enviados aos clientes, mas os detalhes completos da exceção ainda estão registrados do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="6b1d3-144">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="6b1d3-145">Você deve decidir quais incidentes registrar e o nível de gravidade dos incidentes registrados.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="6b1d3-146">Usuários hostis podem ser capazes de desencadear erros deliberadamente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="6b1d3-147">Por exemplo, não registre um incidente de `ProductId` um erro onde um desconhecido é fornecido na URL de um componente que exibe detalhes do produto.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="6b1d3-148">Nem todos os erros devem ser tratados como incidentes de alta gravidade para o registro.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="6b1d3-149">Locais onde erros podem ocorrer</span><span class="sxs-lookup"><span data-stu-id="6b1d3-149">Places where errors may occur</span></span>

<span data-ttu-id="6b1d3-150">O framework e o código do aplicativo podem disparar exceções não tratadas em qualquer um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="6b1d3-151">Instantiva de componentes</span><span class="sxs-lookup"><span data-stu-id="6b1d3-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="6b1d3-152">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="6b1d3-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="6b1d3-153">Lógica de renderização</span><span class="sxs-lookup"><span data-stu-id="6b1d3-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="6b1d3-154">Manipuladores de eventos</span><span class="sxs-lookup"><span data-stu-id="6b1d3-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="6b1d3-155">Descarte de componentes</span><span class="sxs-lookup"><span data-stu-id="6b1d3-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="6b1d3-156">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="6b1d3-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="6b1d3-157">[BlazorRerenderização do servidor](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="6b1d3-157">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="6b1d3-158">As exceções não tratadas anteriores são descritas nas seguintes seções deste artigo.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-158">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="6b1d3-159">Instantiva de componentes</span><span class="sxs-lookup"><span data-stu-id="6b1d3-159">Component instantiation</span></span>

<span data-ttu-id="6b1d3-160">Quando Blazor cria uma instância de um componente:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-160">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="6b1d3-161">O construtor do componente é invocado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-161">The component's constructor is invoked.</span></span>
* <span data-ttu-id="6b1d3-162">Os construtores de quaisquer serviços DI não singleton fornecidos [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) ao construtor [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) do componente através da diretiva ou do atributo são invocados.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-162">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="6b1d3-163">Um Blazor circuito do servidor falha quando qualquer construtor `[Inject]` executado ou um setter para qualquer propriedade lança uma exceção não manuseada.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-163">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="6b1d3-164">A exceção é fatal porque a estrutura não pode instanciar o componente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-164">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="6b1d3-165">Se a lógica do construtor pode lançar exceções, o aplicativo deve prender as exceções usando uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação de erros e registro.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-165">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="6b1d3-166">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="6b1d3-166">Lifecycle methods</span></span>

<span data-ttu-id="6b1d3-167">Durante a vida útil Blazor de um componente, invoca os seguintes [métodos de ciclo de vida:](xref:blazor/lifecycle)</span><span class="sxs-lookup"><span data-stu-id="6b1d3-167">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="6b1d3-168">Se qualquer método de ciclo de vida lançar uma exceção, sincronizada ou Blazor assíncronamente, a exceção é fatal para um circuito do Servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-168">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="6b1d3-169">Para que os componentes lidem com erros nos métodos do ciclo de vida, adicione a lógica de manipulação de erros.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-169">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="6b1d3-170">No exemplo a `OnParametersSetAsync` seguir, quando se chama um método para obter um produto:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-170">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="6b1d3-171">Uma exceção `ProductRepository.GetProductByIdAsync` lançada no método `try-catch` é tratada por uma declaração.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-171">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="6b1d3-172">Quando `catch` o bloco é executado:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-172">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="6b1d3-173">`loadFailed`é definido `true`como , que é usado para exibir uma mensagem de erro para o usuário.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-173">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="6b1d3-174">O erro está registrado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-174">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="6b1d3-175">Lógica de renderização</span><span class="sxs-lookup"><span data-stu-id="6b1d3-175">Rendering logic</span></span>

<span data-ttu-id="6b1d3-176">A marcação declarativa `.razor` em um arquivo de componente `BuildRenderTree`é compilada em um método C# chamado .</span><span class="sxs-lookup"><span data-stu-id="6b1d3-176">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="6b1d3-177">Quando um componente `BuildRenderTree` renderiza, executa e constrói uma estrutura de dados descrevendo os elementos, texto e componentes filho do componente renderizado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-177">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="6b1d3-178">A lógica de renderização pode abrir uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-178">Rendering logic can throw an exception.</span></span> <span data-ttu-id="6b1d3-179">Um exemplo desse cenário `@someObject.PropertyName` ocorre quando `@someObject` `null`é avaliado, mas é .</span><span class="sxs-lookup"><span data-stu-id="6b1d3-179">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="6b1d3-180">Uma exceção não manuseada lançada Blazor pela lógica de renderização é fatal para um circuito do Servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-180">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="6b1d3-181">Para evitar uma exceção de referência `null` nula na lógica de renderização, verifique se há um objeto antes de acessar seus membros.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-181">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="6b1d3-182">No exemplo a `person.Address` seguir, as propriedades `person.Address` `null`não são acessadas se for:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-182">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="6b1d3-183">O código anterior `person` pressupõe `null`que não é.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-183">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="6b1d3-184">Muitas vezes, a estrutura do código garante que um objeto existe no momento em que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-184">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="6b1d3-185">Nesses casos, não é necessário verificar `null` a lógica de renderização.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-185">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="6b1d3-186">No exemplo anterior, `person` pode ser `person` garantido que exista porque é criado quando o componente é instanciado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-186">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="6b1d3-187">Manipuladores de eventos</span><span class="sxs-lookup"><span data-stu-id="6b1d3-187">Event handlers</span></span>

<span data-ttu-id="6b1d3-188">O código do lado do cliente aciona invocações do código C# quando os manipuladores de eventos são criados usando:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-188">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="6b1d3-189">Outros `@on...` atributos</span><span class="sxs-lookup"><span data-stu-id="6b1d3-189">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="6b1d3-190">O código do manipulador de eventos pode lançar uma exceção não manuseada nesses cenários.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-190">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="6b1d3-191">Se um manipulador de eventos lançar uma exceção não manuseada (por Blazor exemplo, uma consulta de banco de dados falhará), a exceção será fatal para um circuito do Servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-191">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="6b1d3-192">Se o aplicativo chamar código que pode falhar por razões externas, tente exceções usando uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação e registro de erros.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-192">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="6b1d3-193">Se o código do usuário não prender e lidar com a exceção, a estrutura registra a exceção e encerra o circuito.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-193">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="6b1d3-194">Descarte de componentes</span><span class="sxs-lookup"><span data-stu-id="6b1d3-194">Component disposal</span></span>

<span data-ttu-id="6b1d3-195">Um componente pode ser removido da ui, por exemplo, porque o usuário navegou para outra página.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-195">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="6b1d3-196">Quando um componente <xref:System.IDisposable?displayProperty=fullName> que implementa é removido da ui, <xref:System.IDisposable.Dispose*> a estrutura chama o método do componente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-196">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="6b1d3-197">Se o método `Dispose` do componente lançar uma exceção não Blazor manuseada, a exceção será fatal para um circuito do Servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-197">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="6b1d3-198">Se a lógica de descarte pode lançar exceções, o aplicativo deve prender as exceções usando uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação de erros e registro.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-198">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="6b1d3-199">Para obter mais informações <xref:blazor/lifecycle#component-disposal-with-idisposable>sobre o descarte de componentes, consulte .</span><span class="sxs-lookup"><span data-stu-id="6b1d3-199">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="6b1d3-200">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="6b1d3-200">JavaScript interop</span></span>

<span data-ttu-id="6b1d3-201">`IJSRuntime.InvokeAsync<T>`permite que o código .NET faça chamadas assíncronas para o tempo de execução javaScript no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-201">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="6b1d3-202">As seguintes condições se `InvokeAsync<T>`aplicam ao manuseio de erros com:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-202">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="6b1d3-203">Se uma `InvokeAsync<T>` chamada falhar sincronizadamente, uma exceção .NET ocorrerá.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-203">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="6b1d3-204">Uma chamada `InvokeAsync<T>` pode falhar, por exemplo, porque os argumentos fornecidos não podem ser serializados.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-204">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="6b1d3-205">O código do desenvolvedor deve pegar a exceção.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-205">Developer code must catch the exception.</span></span> <span data-ttu-id="6b1d3-206">Se o código do aplicativo em um manipulador de eventos ou método de ciclo Blazor de vida de componentes não lidar com uma exceção, a exceção resultante é fatal para um circuito do Servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-206">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="6b1d3-207">Se uma `InvokeAsync<T>` chamada falhar assíncronamente, o .NET <xref:System.Threading.Tasks.Task> falhará.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-207">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="6b1d3-208">Uma chamada `InvokeAsync<T>` pode falhar, por exemplo, porque o código lado `Promise` JavaScript `rejected`lança uma exceção ou retorna uma que concluída como .</span><span class="sxs-lookup"><span data-stu-id="6b1d3-208">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="6b1d3-209">O código do desenvolvedor deve pegar a exceção.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-209">Developer code must catch the exception.</span></span> <span data-ttu-id="6b1d3-210">Se usar o operador [de espera,](/dotnet/csharp/language-reference/keywords/await) considere envolver a chamada do método em uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação de erro e registro.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-210">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="6b1d3-211">Caso contrário, o código de falha resulta em uma Blazor exceção não tratada que é fatal para um circuito do servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-211">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="6b1d3-212">Por padrão, `InvokeAsync<T>` as chamadas devem ser concluídas dentro de um determinado período ou então os tempos de chamada são esgotados. O período de tempo padrão é de um minuto.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-212">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="6b1d3-213">O tempo há10 protege o código contra uma perda na conectividade de rede ou código JavaScript que nunca envia uma mensagem de conclusão.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-213">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="6b1d3-214">Se a chamada for chamada, o resultado `Task` falhará com um <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-214">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="6b1d3-215">Capturar e processar a exceção com o registro.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-215">Trap and process the exception with logging.</span></span>

<span data-ttu-id="6b1d3-216">Da mesma forma, o código JavaScript pode iniciar [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) chamadas para métodos .NET indicados pelo atributo.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-216">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="6b1d3-217">Se esses métodos .NET lançarem uma exceção não manuseada:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-217">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="6b1d3-218">A exceção não é tratada Blazor como fatal para um circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-218">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="6b1d3-219">O lado `Promise` JavaScript é rejeitado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-219">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="6b1d3-220">Você tem a opção de usar o código de manipulação de erros no lado .NET ou no lado JavaScript da chamada do método.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-220">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="6b1d3-221">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-221">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="6b1d3-222">Prerendering do servidor</span><span class="sxs-lookup"><span data-stu-id="6b1d3-222"> Server prerendering</span></span>

Blazor<span data-ttu-id="6b1d3-223">os componentes podem ser pré-renderizados usando o [Component Tag Helper para](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) que sua marcação HTML renderizada seja devolvida como parte da solicitação HTTP inicial do usuário.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-223"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="6b1d3-224">Isso funciona por:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-224">This works by:</span></span>

* <span data-ttu-id="6b1d3-225">Criação de um novo circuito para todos os componentes pré-renderizados que fazem parte da mesma página.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-225">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="6b1d3-226">Gerando o HTML inicial.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-226">Generating the initial HTML.</span></span>
* <span data-ttu-id="6b1d3-227">Tratando o `disconnected` circuito como até que SignalR o navegador do usuário estabeleça uma conexão de volta para o mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-227">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="6b1d3-228">Quando a conexão é estabelecida, a interatividade no circuito é retomada e a marcação HTML dos componentes é atualizada.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-228">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="6b1d3-229">Se algum componente lançar uma exceção não manuseada durante a pré-renderização, por exemplo, durante um método de ciclo de vida ou na lógica de renderização:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-229">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="6b1d3-230">A exceção é fatal para o circuito.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-230">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="6b1d3-231">A exceção é lançada a `Component` pilha de chamadas do Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-231">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="6b1d3-232">Portanto, toda a solicitação HTTP falha a menos que a exceção seja explicitamente capturada pelo código do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-232">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="6b1d3-233">Em circunstâncias normais, quando a pré-renderização falha, continuar a construir e renderizar o componente não faz sentido porque um componente de trabalho não pode ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-233">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="6b1d3-234">Para tolerar erros que possam ocorrer durante a pré-renderização, a lógica de manipulação de erros deve ser colocada dentro de um componente que pode lançar exceções.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-234">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="6b1d3-235">Use instruções [de captura de](/dotnet/csharp/language-reference/keywords/try-catch) tentativa com manipulação de erros e registro.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-235">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="6b1d3-236">Em vez `Component` de envolver o `try-catch` Tag Helper em uma declaração, `Component` coloque a lógica de manipulação de erros no componente renderizado pelo Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-236">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="6b1d3-237">Cenários avançados</span><span class="sxs-lookup"><span data-stu-id="6b1d3-237">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="6b1d3-238">Renderização recursiva</span><span class="sxs-lookup"><span data-stu-id="6b1d3-238">Recursive rendering</span></span>

<span data-ttu-id="6b1d3-239">Os componentes podem ser aninhados recursivamente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-239">Components can be nested recursively.</span></span> <span data-ttu-id="6b1d3-240">Isso é útil para representar estruturas de dados recursivas.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-240">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="6b1d3-241">Por exemplo, `TreeNode` um componente `TreeNode` pode renderizar mais componentes para cada um dos filhos do nó.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-241">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="6b1d3-242">Ao renderizar recursivamente, evite padrões de codificação que resultem em recursão infinita:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-242">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="6b1d3-243">Não torne recursivamente uma estrutura de dados que contenha um ciclo.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-243">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="6b1d3-244">Por exemplo, não faça um nó de árvore cujos filhos se incluam.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-244">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="6b1d3-245">Não crie uma cadeia de layouts que contenham um ciclo.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-245">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="6b1d3-246">Por exemplo, não crie um layout cujo layout seja em si.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-246">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="6b1d3-247">Não permita que um usuário final viole invariantes (regras) de recursão através de entrada de dados maliciosos ou chamadas interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-247">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="6b1d3-248">Loops infinitos durante a renderização:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-248">Infinite loops during rendering:</span></span>

* <span data-ttu-id="6b1d3-249">Faz com que o processo de renderização continue para sempre.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-249">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="6b1d3-250">Equivale a criar um loop não terminado.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-250">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="6b1d3-251">Nesses cenários, Blazor um circuito servidor afetado falha, e o segmento geralmente tenta:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-251">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="6b1d3-252">Consumir o máximo de tempo de CPU permitido pelo sistema operacional, indefinidamente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-252">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="6b1d3-253">Consumir uma quantidade ilimitada de memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-253">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="6b1d3-254">Consumir memória ilimitada é equivalente ao cenário em que um loop não terminado adiciona entradas a uma coleção em cada iteração.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-254">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="6b1d3-255">Para evitar padrões infinitos de recursão, certifique-se de que o código de renderização recursivo contenha condições de parada adequadas.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-255">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="6b1d3-256">Lógica de árvore de renderização personalizada</span><span class="sxs-lookup"><span data-stu-id="6b1d3-256">Custom render tree logic</span></span>

<span data-ttu-id="6b1d3-257">A Blazor maioria dos componentes são implementados como arquivos *.razor* `RenderTreeBuilder` e são compilados para produzir lógica que opera em um para renderizar sua saída.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-257">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="6b1d3-258">Um desenvolvedor pode `RenderTreeBuilder` implementar manualmente a lógica usando o código C# procedural.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-258">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="6b1d3-259">Para obter mais informações, consulte <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-259">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="6b1d3-260">O uso da lógica manual de construção de árvores de renderização é considerado um cenário avançado e inseguro, não recomendado para o desenvolvimento geral de componentes.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-260">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="6b1d3-261">Se `RenderTreeBuilder` o código for escrito, o desenvolvedor deve garantir a correção do código.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-261">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="6b1d3-262">Por exemplo, o desenvolvedor deve garantir que:</span><span class="sxs-lookup"><span data-stu-id="6b1d3-262">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="6b1d3-263">Chamadas `OpenElement` para `CloseElement` e são corretamente equilibradas.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-263">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="6b1d3-264">Atributos só são adicionados nos lugares corretos.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-264">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="6b1d3-265">A lógica incorreta do construtor de árvores de renderização manual pode causar comportamentos arbitrários indefinidos, incluindo falhas, travamentos de servidor e vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-265">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="6b1d3-266">Considere a lógica manual do construtor de árvores de renderização no mesmo nível de complexidade e com o mesmo nível de *perigo* que escrever código de montagem ou instruções MSIL manualmente.</span><span class="sxs-lookup"><span data-stu-id="6b1d3-266">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
