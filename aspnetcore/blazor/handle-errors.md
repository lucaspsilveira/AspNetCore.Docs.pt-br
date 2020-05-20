---
title: Tratar erros em Blazor aplicativos ASP.NET Core
author: guardrex
description: Descubra como ASP.NET Core Blazor como o Blazor gerencia exceções sem tratamento e como desenvolver aplicativos que detectam e manipulam erros.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: cca4d8ce0c783f26f33cb7b2b1535a4bc53384d6
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424336"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="ffffe-103">Tratar erros em Blazor aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffffe-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="ffffe-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="ffffe-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="ffffe-105">Este artigo descreve como o Blazor gerencia exceções sem tratamento e como desenvolver aplicativos que detectam e manipulam erros.</span><span class="sxs-lookup"><span data-stu-id="ffffe-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="ffffe-106">Erros detalhados durante o desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="ffffe-106">Detailed errors during development</span></span>

<span data-ttu-id="ffffe-107">Quando um Blazor aplicativo não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema.</span><span class="sxs-lookup"><span data-stu-id="ffffe-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="ffffe-108">Quando ocorre um erro, os Blazor aplicativos exibem uma barra de ouro na parte inferior da tela:</span><span class="sxs-lookup"><span data-stu-id="ffffe-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="ffffe-109">Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.</span><span class="sxs-lookup"><span data-stu-id="ffffe-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="ffffe-110">Em produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.</span><span class="sxs-lookup"><span data-stu-id="ffffe-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="ffffe-111">A interface do usuário para essa experiência de tratamento de erros faz parte dos Blazor modelos de projeto.</span><span class="sxs-lookup"><span data-stu-id="ffffe-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="ffffe-112">Em um Blazor aplicativo Webassembly, personalize a experiência no arquivo *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="ffffe-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="ffffe-113">Em um Blazor aplicativo de servidor, personalize a experiência no arquivo *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ffffe-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="ffffe-114">O `blazor-error-ui` elemento é oculto pelos estilos incluídos nos Blazor modelos (*wwwroot/CSS/site. css*) e, em seguida, mostrados quando ocorre um erro:</span><span class="sxs-lookup"><span data-stu-id="ffffe-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

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

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="ffffe-115">Como um Blazor aplicativo de servidor reage a exceções sem tratamento</span><span class="sxs-lookup"><span data-stu-id="ffffe-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="ffffe-116">O servidor é uma estrutura com estado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-116"> Server is a stateful framework.</span></span> <span data-ttu-id="ffffe-117">Enquanto os usuários interagem com um aplicativo, eles mantêm uma conexão com o servidor conhecido como um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="ffffe-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="ffffe-118">O circuito contém instâncias de componentes ativos, além de muitos outros aspectos do estado, como:</span><span class="sxs-lookup"><span data-stu-id="ffffe-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="ffffe-119">A saída renderizada mais recente dos componentes.</span><span class="sxs-lookup"><span data-stu-id="ffffe-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="ffffe-120">O conjunto atual de delegados de manipulação de eventos que poderia ser disparado por eventos do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="ffffe-121">Se um usuário abrir o aplicativo em várias guias do navegador, ele terá vários circuitos independentes.</span><span class="sxs-lookup"><span data-stu-id="ffffe-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="ffffe-122">trata a maioria das exceções sem tratamento como fatal para o circuito onde elas ocorrem.</span><span class="sxs-lookup"><span data-stu-id="ffffe-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="ffffe-123">Se um circuito for encerrado devido a uma exceção sem tratamento, o usuário só poderá continuar a interagir com o aplicativo recarregando a página para criar um novo circuito.</span><span class="sxs-lookup"><span data-stu-id="ffffe-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="ffffe-124">Os circuitos fora do que foi encerrado, que são circuitos para outros usuários ou outras guias do navegador, não são afetados.</span><span class="sxs-lookup"><span data-stu-id="ffffe-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="ffffe-125">Esse cenário é semelhante a um aplicativo de área de trabalho que falha.</span><span class="sxs-lookup"><span data-stu-id="ffffe-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="ffffe-126">O aplicativo com falha deve ser reiniciado, mas outros aplicativos não são afetados.</span><span class="sxs-lookup"><span data-stu-id="ffffe-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="ffffe-127">Um circuito é encerrado quando ocorre uma exceção sem tratamento pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="ffffe-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="ffffe-128">Uma exceção sem tratamento geralmente deixa o circuito em um estado indefinido.</span><span class="sxs-lookup"><span data-stu-id="ffffe-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="ffffe-129">A operação normal do aplicativo não pode ser garantida após uma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="ffffe-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="ffffe-130">As vulnerabilidades de segurança podem aparecer no aplicativo se o circuito continuar.</span><span class="sxs-lookup"><span data-stu-id="ffffe-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="ffffe-131">Gerenciar exceções sem tratamento no código do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="ffffe-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="ffffe-132">Para que um aplicativo continue após um erro, o aplicativo deve ter a lógica de tratamento de erros.</span><span class="sxs-lookup"><span data-stu-id="ffffe-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="ffffe-133">As seções posteriores deste artigo descrevem possíveis fontes de exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="ffffe-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="ffffe-134">Em produção, não processe mensagens de exceção de estrutura ou rastreamentos de pilha na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="ffffe-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="ffffe-135">O processamento de mensagens de exceção ou rastreamentos de pilha pode:</span><span class="sxs-lookup"><span data-stu-id="ffffe-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="ffffe-136">Divulgue informações confidenciais para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="ffffe-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="ffffe-137">Ajudar um usuário mal-intencionado a descobrir pontos fracos em um aplicativo que pode comprometer a segurança do aplicativo, do servidor ou da rede.</span><span class="sxs-lookup"><span data-stu-id="ffffe-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="ffffe-138">Registrar erros em log com um provedor persistente</span><span class="sxs-lookup"><span data-stu-id="ffffe-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="ffffe-139">Se ocorrer uma exceção sem tratamento, a exceção será registrada em <xref:Microsoft.Extensions.Logging.ILogger> instâncias configuradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="ffffe-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="ffffe-140">Por padrão, Blazor os aplicativos registram em log a saída do console com o provedor de log do console.</span><span class="sxs-lookup"><span data-stu-id="ffffe-140">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="ffffe-141">Considere fazer logon em um local mais permanente com um provedor que gerencia o tamanho do log e a rotação do log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="ffffe-142">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="ffffe-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="ffffe-143">Durante o desenvolvimento, Blazor o geralmente envia os detalhes completos das exceções para o console do navegador para auxiliar na depuração.</span><span class="sxs-lookup"><span data-stu-id="ffffe-143">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="ffffe-144">Na produção, os erros detalhados no console do navegador são desabilitados por padrão, o que significa que os erros não são enviados aos clientes, mas os detalhes completos da exceção ainda são registrados no lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="ffffe-145">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="ffffe-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="ffffe-146">Você deve decidir quais incidentes registrar e o nível de gravidade dos incidentes registrados.</span><span class="sxs-lookup"><span data-stu-id="ffffe-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="ffffe-147">Usuários hostis podem ser capazes de disparar erros deliberadamente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="ffffe-148">Por exemplo, não registre um incidente de um erro em que um desconhecido `ProductId` é fornecido na URL de um componente que exibe detalhes do produto.</span><span class="sxs-lookup"><span data-stu-id="ffffe-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="ffffe-149">Nem todos os erros devem ser tratados como incidentes de alta severidade para registro em log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="ffffe-150">Para obter mais informações, consulte <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="ffffe-150">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="ffffe-151">Locais onde podem ocorrer erros</span><span class="sxs-lookup"><span data-stu-id="ffffe-151">Places where errors may occur</span></span>

<span data-ttu-id="ffffe-152">O Framework e o código do aplicativo podem disparar exceções sem tratamento em qualquer um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="ffffe-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="ffffe-153">Instanciação de componente</span><span class="sxs-lookup"><span data-stu-id="ffffe-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="ffffe-154">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="ffffe-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="ffffe-155">Lógica de renderização</span><span class="sxs-lookup"><span data-stu-id="ffffe-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="ffffe-156">Manipuladores de eventos</span><span class="sxs-lookup"><span data-stu-id="ffffe-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="ffffe-157">Disposição do componente</span><span class="sxs-lookup"><span data-stu-id="ffffe-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="ffffe-158">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="ffffe-158">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="ffffe-159">[BlazorReprocessamento de servidor](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="ffffe-159">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="ffffe-160">As exceções sem tratamento anteriores são descritas nas seções a seguir deste artigo.</span><span class="sxs-lookup"><span data-stu-id="ffffe-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="ffffe-161">Instanciação de componente</span><span class="sxs-lookup"><span data-stu-id="ffffe-161">Component instantiation</span></span>

<span data-ttu-id="ffffe-162">Quando o Blazor cria uma instância de um componente:</span><span class="sxs-lookup"><span data-stu-id="ffffe-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="ffffe-163">O construtor do componente é invocado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="ffffe-164">Os construtores de quaisquer serviços não singleton de DI fornecidos ao construtor do componente por meio da [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) diretiva ou do [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) atributo são invocados.</span><span class="sxs-lookup"><span data-stu-id="ffffe-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="ffffe-165">Um Blazor circuito de servidor falha quando qualquer Construtor executado ou um setter para qualquer `[Inject]` propriedade gera uma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="ffffe-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="ffffe-166">A exceção é fatal porque a estrutura não pode instanciar o componente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="ffffe-167">Se a lógica do Construtor puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="ffffe-168">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="ffffe-168">Lifecycle methods</span></span>

<span data-ttu-id="ffffe-169">Durante o tempo de vida de um componente, Blazor o invoca os seguintes [métodos de ciclo de vida](xref:blazor/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="ffffe-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="ffffe-170">Se qualquer método de ciclo de vida lançar uma exceção, de forma síncrona ou assíncrona, a exceção será fatal para um Blazor circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="ffffe-171">Para componentes que lidam com erros em métodos de ciclo de vida, adicione a lógica de tratamento de erros.</span><span class="sxs-lookup"><span data-stu-id="ffffe-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="ffffe-172">No exemplo a seguir, onde `OnParametersSetAsync` chama um método para obter um produto:</span><span class="sxs-lookup"><span data-stu-id="ffffe-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="ffffe-173">Uma exceção gerada no `ProductRepository.GetProductByIdAsync` método é manipulada por uma `try-catch` instrução.</span><span class="sxs-lookup"><span data-stu-id="ffffe-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="ffffe-174">Quando o `catch` bloco é executado:</span><span class="sxs-lookup"><span data-stu-id="ffffe-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="ffffe-175">`loadFailed`é definido como `true` , que é usado para exibir uma mensagem de erro para o usuário.</span><span class="sxs-lookup"><span data-stu-id="ffffe-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="ffffe-176">O erro é registrado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="ffffe-177">Lógica de renderização</span><span class="sxs-lookup"><span data-stu-id="ffffe-177">Rendering logic</span></span>

<span data-ttu-id="ffffe-178">A marcação declarativa em um `.razor` arquivo de componente é compilada em um método C# chamado `BuildRenderTree` .</span><span class="sxs-lookup"><span data-stu-id="ffffe-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="ffffe-179">Quando um componente renderiza, `BuildRenderTree` o executa e cria uma estrutura de dados que descreve os elementos, o texto e os componentes filho do componente renderizado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="ffffe-180">A lógica de renderização pode gerar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="ffffe-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="ffffe-181">Um exemplo desse cenário ocorre quando o `@someObject.PropertyName` é avaliado, mas `@someObject` é `null` .</span><span class="sxs-lookup"><span data-stu-id="ffffe-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="ffffe-182">Uma exceção sem tratamento gerada pela lógica de renderização é fatal para um Blazor circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="ffffe-183">Para evitar uma exceção de referência nula na lógica de renderização, verifique se há um `null` objeto antes de acessar seus membros.</span><span class="sxs-lookup"><span data-stu-id="ffffe-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="ffffe-184">No exemplo a seguir, `person.Address` as propriedades não são acessadas se `person.Address` for `null` :</span><span class="sxs-lookup"><span data-stu-id="ffffe-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="ffffe-185">O código anterior pressupõe que `person` não é `null` .</span><span class="sxs-lookup"><span data-stu-id="ffffe-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="ffffe-186">Geralmente, a estrutura do código garante que um objeto exista no momento em que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="ffffe-187">Nesses casos, não é necessário verificar `null` na lógica de renderização.</span><span class="sxs-lookup"><span data-stu-id="ffffe-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="ffffe-188">No exemplo anterior, `person` talvez seja garantido que exista porque `person` é criado quando o componente é instanciado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="ffffe-189">Manipuladores de eventos</span><span class="sxs-lookup"><span data-stu-id="ffffe-189">Event handlers</span></span>

<span data-ttu-id="ffffe-190">O código do lado do cliente dispara invocações de código C# quando manipuladores de eventos são criados usando:</span><span class="sxs-lookup"><span data-stu-id="ffffe-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="ffffe-191">Outros `@on...` atributos</span><span class="sxs-lookup"><span data-stu-id="ffffe-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="ffffe-192">O código do manipulador de eventos pode gerar uma exceção sem tratamento nesses cenários.</span><span class="sxs-lookup"><span data-stu-id="ffffe-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="ffffe-193">Se um manipulador de eventos lançar uma exceção sem tratamento (por exemplo, uma consulta de banco de dados falhar), a exceção será fatal para um Blazor circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="ffffe-194">Se o aplicativo chamar o código que poderia falhar por motivos externos, intercepte as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="ffffe-195">Se o código do usuário não interceptar e manipular a exceção, a estrutura registrará a exceção e encerrará o circuito.</span><span class="sxs-lookup"><span data-stu-id="ffffe-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="ffffe-196">Disposição do componente</span><span class="sxs-lookup"><span data-stu-id="ffffe-196">Component disposal</span></span>

<span data-ttu-id="ffffe-197">Um componente pode ser removido da interface do usuário, por exemplo, porque o usuário navegou para outra página.</span><span class="sxs-lookup"><span data-stu-id="ffffe-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="ffffe-198">Quando um componente que implementa o <xref:System.IDisposable?displayProperty=fullName> é removido da interface do usuário, a estrutura chama o <xref:System.IDisposable.Dispose%2A> método do componente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="ffffe-199">Se o método do componente `Dispose` lançar uma exceção sem tratamento, a exceção será fatal para um Blazor circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="ffffe-200">Se a lógica de descarte puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="ffffe-201">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="ffffe-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="ffffe-202">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="ffffe-202">JavaScript interop</span></span>

<span data-ttu-id="ffffe-203">`IJSRuntime.InvokeAsync<T>`permite que o código .NET faça chamadas assíncronas para o tempo de execução do JavaScript no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="ffffe-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="ffffe-204">As seguintes condições se aplicam ao tratamento de erros com `InvokeAsync<T>` :</span><span class="sxs-lookup"><span data-stu-id="ffffe-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="ffffe-205">Se uma chamada para `InvokeAsync<T>` falhar de forma síncrona, ocorrerá uma exceção do .net.</span><span class="sxs-lookup"><span data-stu-id="ffffe-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="ffffe-206">Uma chamada para `InvokeAsync<T>` pode falhar, por exemplo, porque os argumentos fornecidos não podem ser serializados.</span><span class="sxs-lookup"><span data-stu-id="ffffe-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="ffffe-207">O código do desenvolvedor deve capturar a exceção.</span><span class="sxs-lookup"><span data-stu-id="ffffe-207">Developer code must catch the exception.</span></span> <span data-ttu-id="ffffe-208">Se o código do aplicativo em um manipulador de eventos ou método de ciclo de vida de componente não tratar uma exceção, a exceção resultante será fatal para um Blazor circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="ffffe-209">Se uma chamada para `InvokeAsync<T>` falhar de forma assíncrona, o .NET <xref:System.Threading.Tasks.Task> falhará.</span><span class="sxs-lookup"><span data-stu-id="ffffe-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="ffffe-210">Uma chamada para `InvokeAsync<T>` pode falhar, por exemplo, porque o código do lado do JavaScript gera uma exceção ou retorna um `Promise` que foi concluído como `rejected` .</span><span class="sxs-lookup"><span data-stu-id="ffffe-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="ffffe-211">O código do desenvolvedor deve capturar a exceção.</span><span class="sxs-lookup"><span data-stu-id="ffffe-211">Developer code must catch the exception.</span></span> <span data-ttu-id="ffffe-212">Se estiver usando o operador [Await](/dotnet/csharp/language-reference/keywords/await) , considere encapsular a chamada de método em uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="ffffe-213">Caso contrário, o código com falha resultará em uma exceção sem tratamento que é fatal para um Blazor circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="ffffe-214">Por padrão, as chamadas para `InvokeAsync<T>` devem ser concluídas em um determinado período ou a chamada atinge o tempo limite. O período de tempo limite padrão é de um minuto.</span><span class="sxs-lookup"><span data-stu-id="ffffe-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="ffffe-215">O tempo limite protege o código contra uma perda na conectividade de rede ou no código JavaScript que nunca envia uma mensagem de conclusão.</span><span class="sxs-lookup"><span data-stu-id="ffffe-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="ffffe-216">Se a chamada atingir o tempo limite, o resultado `Task` falhará com um <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="ffffe-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="ffffe-217">Interceptar e processar a exceção com registro em log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="ffffe-218">Da mesma forma, o código JavaScript pode iniciar chamadas para métodos .NET indicados pelo [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) atributo.</span><span class="sxs-lookup"><span data-stu-id="ffffe-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="ffffe-219">Se esses métodos .NET lançarem uma exceção sem tratamento:</span><span class="sxs-lookup"><span data-stu-id="ffffe-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="ffffe-220">A exceção não é tratada como fatal para um Blazor circuito de servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="ffffe-221">O lado do JavaScript `Promise` é rejeitado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="ffffe-222">Você tem a opção de usar o código de tratamento de erros no lado do .NET ou no lado do JavaScript da chamada do método.</span><span class="sxs-lookup"><span data-stu-id="ffffe-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="ffffe-223">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="ffffe-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Blazor<span data-ttu-id="ffffe-224">Pré-processamento de servidor</span><span class="sxs-lookup"><span data-stu-id="ffffe-224"> Server prerendering</span></span>

Blazor<span data-ttu-id="ffffe-225">os componentes podem ser renderizados usando o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para que sua marcação HTML renderizada seja retornada como parte da solicitação HTTP inicial do usuário.</span><span class="sxs-lookup"><span data-stu-id="ffffe-225"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="ffffe-226">Isso funciona por:</span><span class="sxs-lookup"><span data-stu-id="ffffe-226">This works by:</span></span>

* <span data-ttu-id="ffffe-227">Criar um novo circuito para todos os componentes renderizados que fazem parte da mesma página.</span><span class="sxs-lookup"><span data-stu-id="ffffe-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="ffffe-228">Gerando o HTML inicial.</span><span class="sxs-lookup"><span data-stu-id="ffffe-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="ffffe-229">Tratar o circuito como `disconnected` até que o navegador do usuário estabeleça uma SignalR conexão de volta para o mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-229">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="ffffe-230">Quando a conexão é estabelecida, a interatividade no circuito é retomada e a marcação HTML dos componentes é atualizada.</span><span class="sxs-lookup"><span data-stu-id="ffffe-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="ffffe-231">Se qualquer componente lançar uma exceção sem tratamento durante o pré-processamento, por exemplo, durante um método de ciclo de vida ou na lógica de renderização:</span><span class="sxs-lookup"><span data-stu-id="ffffe-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="ffffe-232">A exceção é fatal para o circuito.</span><span class="sxs-lookup"><span data-stu-id="ffffe-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="ffffe-233">A exceção é gerada na pilha de chamadas a partir do `Component` auxiliar de marca.</span><span class="sxs-lookup"><span data-stu-id="ffffe-233">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="ffffe-234">Portanto, toda a solicitação HTTP falha, a menos que a exceção seja explicitamente detectada pelo código do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="ffffe-235">Em circunstâncias normais, quando o pré-processamento falha, continuar a criar e renderizar o componente não faz sentido porque um componente de trabalho não pode ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="ffffe-236">Para tolerar erros que podem ocorrer durante o pré-processamento, a lógica de tratamento de erro deve ser colocada dentro de um componente que pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="ffffe-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="ffffe-237">Use instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="ffffe-237">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="ffffe-238">Em vez de encapsular o `Component` auxiliar de marca em uma `try-catch` instrução, coloque a lógica de tratamento de erro no componente renderizado pelo `Component` auxiliar de marca.</span><span class="sxs-lookup"><span data-stu-id="ffffe-238">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="ffffe-239">Cenários avançados</span><span class="sxs-lookup"><span data-stu-id="ffffe-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="ffffe-240">Renderização recursiva</span><span class="sxs-lookup"><span data-stu-id="ffffe-240">Recursive rendering</span></span>

<span data-ttu-id="ffffe-241">Os componentes podem ser aninhados recursivamente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-241">Components can be nested recursively.</span></span> <span data-ttu-id="ffffe-242">Isso é útil para representar estruturas de dados recursivas.</span><span class="sxs-lookup"><span data-stu-id="ffffe-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="ffffe-243">Por exemplo, um `TreeNode` componente pode renderizar mais `TreeNode` componentes para cada um dos filhos do nó.</span><span class="sxs-lookup"><span data-stu-id="ffffe-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="ffffe-244">Ao renderizar recursivamente, evite codificar padrões que resultam em recursão infinita:</span><span class="sxs-lookup"><span data-stu-id="ffffe-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="ffffe-245">Não renderizar recursivamente uma estrutura de dados que contém um ciclo.</span><span class="sxs-lookup"><span data-stu-id="ffffe-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="ffffe-246">Por exemplo, não renderize um nó de árvore cujos filhos incluam a si mesmo.</span><span class="sxs-lookup"><span data-stu-id="ffffe-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="ffffe-247">Não crie uma cadeia de layouts que contenham um ciclo.</span><span class="sxs-lookup"><span data-stu-id="ffffe-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="ffffe-248">Por exemplo, não crie um layout cujo layout seja próprio.</span><span class="sxs-lookup"><span data-stu-id="ffffe-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="ffffe-249">Não permita que um usuário final viole invariáveis de recursão (regras) por meio de entradas de dados mal-intencionados ou chamadas de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ffffe-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="ffffe-250">Loops infinitos durante a renderização:</span><span class="sxs-lookup"><span data-stu-id="ffffe-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="ffffe-251">Faz com que o processo de renderização continue para sempre.</span><span class="sxs-lookup"><span data-stu-id="ffffe-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="ffffe-252">É equivalente a criar um loop não finalizado.</span><span class="sxs-lookup"><span data-stu-id="ffffe-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="ffffe-253">Nesses cenários, um circuito de Blazor servidor afetado falha e o thread geralmente tenta:</span><span class="sxs-lookup"><span data-stu-id="ffffe-253">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="ffffe-254">Consuma tanto tempo de CPU quanto permitido pelo sistema operacional, indefinidamente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="ffffe-255">Consuma uma quantidade ilimitada de memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="ffffe-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="ffffe-256">O consumo de memória ilimitada é equivalente ao cenário em que um loop não finalizado adiciona entradas a uma coleção em cada iteração.</span><span class="sxs-lookup"><span data-stu-id="ffffe-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="ffffe-257">Para evitar padrões de recursão infinitos, verifique se o código de renderização recursivo contém condições de parada adequadas.</span><span class="sxs-lookup"><span data-stu-id="ffffe-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="ffffe-258">Lógica de árvore de renderização personalizada</span><span class="sxs-lookup"><span data-stu-id="ffffe-258">Custom render tree logic</span></span>

<span data-ttu-id="ffffe-259">A maioria dos Blazor componentes são implementados como arquivos *. Razor* e são compilados para produzir lógica que opera em um `RenderTreeBuilder` para renderizar sua saída.</span><span class="sxs-lookup"><span data-stu-id="ffffe-259">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="ffffe-260">Um desenvolvedor pode implementar A `RenderTreeBuilder` lógica manualmente usando o código C# de procedimento.</span><span class="sxs-lookup"><span data-stu-id="ffffe-260">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="ffffe-261">Para obter mais informações, consulte <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="ffffe-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="ffffe-262">O uso da lógica do construtor de árvore de renderização manual é considerado um cenário avançado e não seguro, não recomendado para o desenvolvimento geral de componentes.</span><span class="sxs-lookup"><span data-stu-id="ffffe-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="ffffe-263">Se `RenderTreeBuilder` o código for escrito, o desenvolvedor deverá garantir a exatidão do código.</span><span class="sxs-lookup"><span data-stu-id="ffffe-263">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="ffffe-264">Por exemplo, o desenvolvedor deve garantir que:</span><span class="sxs-lookup"><span data-stu-id="ffffe-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="ffffe-265">Chamadas para `OpenElement` e `CloseElement` são balanceadas corretamente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-265">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="ffffe-266">Os atributos são adicionados somente nos locais corretos.</span><span class="sxs-lookup"><span data-stu-id="ffffe-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="ffffe-267">A lógica do construtor de árvore de renderização manual incorreta pode causar um comportamento indefinido arbitrário, incluindo falhas, travamentos do servidor e vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="ffffe-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="ffffe-268">Considere a lógica do construtor de árvore de renderização manual no mesmo nível de complexidade e com o mesmo nível de *perigo* de escrever código de assembly ou instruções MSIL manualmente.</span><span class="sxs-lookup"><span data-stu-id="ffffe-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
