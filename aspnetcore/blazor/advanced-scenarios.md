---
title: ASP.NET Blazor Core cenários avançados
author: guardrex
description: Saiba mais sobre Blazorcenários avançados em , incluindo como incorporar a lógica manual do RenderTreeBuilder em um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659449"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="bcc63-103">cenários avançados de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="bcc63-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="bcc63-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bcc63-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="bcc63-105">Manipulador de circuito blazor server</span><span class="sxs-lookup"><span data-stu-id="bcc63-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="bcc63-106">O Blazor Server permite que o código defina um *manipulador de circuitos,* que permite executar código em alterações no estado do circuito de um usuário.</span><span class="sxs-lookup"><span data-stu-id="bcc63-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="bcc63-107">Um manipulador de circuitos `CircuitHandler` é implementado por derivação e registro da classe no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bcc63-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="bcc63-108">O exemplo a seguir de um manipulador de circuitos rastreia conexões signalR abertas:</span><span class="sxs-lookup"><span data-stu-id="bcc63-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="bcc63-109">Os manipuladores de circuito são registrados usando DI.</span><span class="sxs-lookup"><span data-stu-id="bcc63-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="bcc63-110">As instâncias escopo são criadas por instância de um circuito.</span><span class="sxs-lookup"><span data-stu-id="bcc63-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="bcc63-111">Usando `TrackingCircuitHandler` o exemplo anterior, um serviço singleton é criado porque o estado de todos os circuitos deve ser rastreado:</span><span class="sxs-lookup"><span data-stu-id="bcc63-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="bcc63-112">Se os métodos de um manipulador de circuito personalizado lançar uma exceção não manuseada, a exceção é fatal para o circuito Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="bcc63-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="bcc63-113">Para tolerar exceções no código de um manipulador ou nos métodos chamados, enrole o código em uma ou mais declarações [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação e registro de erros.</span><span class="sxs-lookup"><span data-stu-id="bcc63-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="bcc63-114">Quando um circuito termina porque um usuário desligou e a estrutura está limpando o estado do circuito, a estrutura elimina o escopo DI do circuito.</span><span class="sxs-lookup"><span data-stu-id="bcc63-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="bcc63-115">A eliminação do escopo elimina todos os serviços DI com escopo de circuito que implementam <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="bcc63-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="bcc63-116">Se qualquer serviço DI lançar uma exceção não manuseada durante a eliminação, o framework registrará a exceção.</span><span class="sxs-lookup"><span data-stu-id="bcc63-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="bcc63-117">Lógica manual do RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="bcc63-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="bcc63-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fornece métodos para manipular componentes e elementos, incluindo componentes de construção manualmente em código C#.</span><span class="sxs-lookup"><span data-stu-id="bcc63-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="bcc63-119">O `RenderTreeBuilder` uso para criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="bcc63-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="bcc63-120">Um componente malformado (por exemplo, uma tag de marcação não fechada) pode resultar em comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="bcc63-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="bcc63-121">Considere o `PetDetails` seguinte componente, que pode ser construído manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="bcc63-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="bcc63-122">No exemplo a seguir, `CreateComponent` o loop `PetDetails` no método gera três componentes.</span><span class="sxs-lookup"><span data-stu-id="bcc63-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="bcc63-123">Ao `RenderTreeBuilder` chamar métodos para criar`OpenComponent` `AddAttribute`os componentes (e), os números de seqüência são números de linha de código fonte.</span><span class="sxs-lookup"><span data-stu-id="bcc63-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="bcc63-124">O algoritmo de diferença de Blazor baseia-se nos números de seqüência correspondentes a linhas distintas de código, não invocações de chamadas distintas.</span><span class="sxs-lookup"><span data-stu-id="bcc63-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="bcc63-125">Ao criar um `RenderTreeBuilder` componente com métodos, codifique os argumentos para números de seqüência.</span><span class="sxs-lookup"><span data-stu-id="bcc63-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="bcc63-126">**Usar um cálculo ou contador para gerar o número de seqüência pode levar a um desempenho ruim.**</span><span class="sxs-lookup"><span data-stu-id="bcc63-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="bcc63-127">Para obter mais informações, consulte os [números de seqüência relacionados com números de linha de código e não](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) seção de ordem de execução.</span><span class="sxs-lookup"><span data-stu-id="bcc63-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="bcc63-128">`BuiltContent`Componente:</span><span class="sxs-lookup"><span data-stu-id="bcc63-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="bcc63-129">Os tipos `Microsoft.AspNetCore.Components.RenderTree` permitem o processamento dos *resultados* das operações de renderização.</span><span class="sxs-lookup"><span data-stu-id="bcc63-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="bcc63-130">Estes são detalhes internos da implementação do quadro blazor.</span><span class="sxs-lookup"><span data-stu-id="bcc63-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="bcc63-131">Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="bcc63-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="bcc63-132">Os números de seqüência se relacionam com números de linha de código e não com ordem de execução</span><span class="sxs-lookup"><span data-stu-id="bcc63-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="bcc63-133">Os arquivos de componentes da*navalha (.razor)* são sempre compilados.</span><span class="sxs-lookup"><span data-stu-id="bcc63-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="bcc63-134">A compilação é uma vantagem potencial sobre a interpretação do código porque a etapa de compilação pode ser usada para injetar informações que melhorem o desempenho do aplicativo em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="bcc63-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="bcc63-135">Um exemplo-chave dessas melhorias envolve *números de seqüência*.</span><span class="sxs-lookup"><span data-stu-id="bcc63-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="bcc63-136">Os números de seqüência indicam para o tempo de execução quais saídas vieram das quais linhas de código distintas e ordenadas.</span><span class="sxs-lookup"><span data-stu-id="bcc63-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="bcc63-137">O tempo de execução usa essas informações para gerar difusões de árvores eficientes em tempo linear, o que é muito mais rápido do que é normalmente possível para um algoritmo geral de difusão de árvores.</span><span class="sxs-lookup"><span data-stu-id="bcc63-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="bcc63-138">Considere o seguinte componente de navalha *(.razor)* arquivo:</span><span class="sxs-lookup"><span data-stu-id="bcc63-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="bcc63-139">O código anterior compila algo como:</span><span class="sxs-lookup"><span data-stu-id="bcc63-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="bcc63-140">Quando o código é executado pela `someFlag` `true`primeira vez, se for, o construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="bcc63-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="bcc63-141">Sequência</span><span class="sxs-lookup"><span data-stu-id="bcc63-141">Sequence</span></span> | <span data-ttu-id="bcc63-142">Type</span><span class="sxs-lookup"><span data-stu-id="bcc63-142">Type</span></span>      | <span data-ttu-id="bcc63-143">Dados</span><span class="sxs-lookup"><span data-stu-id="bcc63-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="bcc63-144">0</span><span class="sxs-lookup"><span data-stu-id="bcc63-144">0</span></span>        | <span data-ttu-id="bcc63-145">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="bcc63-145">Text node</span></span> | <span data-ttu-id="bcc63-146">Primeiro</span><span class="sxs-lookup"><span data-stu-id="bcc63-146">First</span></span>  |
| <span data-ttu-id="bcc63-147">1</span><span class="sxs-lookup"><span data-stu-id="bcc63-147">1</span></span>        | <span data-ttu-id="bcc63-148">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="bcc63-148">Text node</span></span> | <span data-ttu-id="bcc63-149">Segundo</span><span class="sxs-lookup"><span data-stu-id="bcc63-149">Second</span></span> |

<span data-ttu-id="bcc63-150">Imagine `someFlag` que `false`isso se torne , e a marcação é renderizada novamente.</span><span class="sxs-lookup"><span data-stu-id="bcc63-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="bcc63-151">Desta vez, o construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="bcc63-151">This time, the builder receives:</span></span>

| <span data-ttu-id="bcc63-152">Sequência</span><span class="sxs-lookup"><span data-stu-id="bcc63-152">Sequence</span></span> | <span data-ttu-id="bcc63-153">Type</span><span class="sxs-lookup"><span data-stu-id="bcc63-153">Type</span></span>       | <span data-ttu-id="bcc63-154">Dados</span><span class="sxs-lookup"><span data-stu-id="bcc63-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="bcc63-155">1</span><span class="sxs-lookup"><span data-stu-id="bcc63-155">1</span></span>        | <span data-ttu-id="bcc63-156">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="bcc63-156">Text node</span></span>  | <span data-ttu-id="bcc63-157">Segundo</span><span class="sxs-lookup"><span data-stu-id="bcc63-157">Second</span></span> |

<span data-ttu-id="bcc63-158">Quando o tempo de execução executa um diff, ele vê que o item na seqüência `0` foi removido, de modo que gera o seguinte script de *edição*trivial:</span><span class="sxs-lookup"><span data-stu-id="bcc63-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="bcc63-159">Remova o primeiro nó de texto.</span><span class="sxs-lookup"><span data-stu-id="bcc63-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="bcc63-160">O problema de gerar números de seqüência programática</span><span class="sxs-lookup"><span data-stu-id="bcc63-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="bcc63-161">Imagine, em vez disso, que você escreveu a seguinte lógica de construtor de árvores de renderização:</span><span class="sxs-lookup"><span data-stu-id="bcc63-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="bcc63-162">Agora, a primeira saída é:</span><span class="sxs-lookup"><span data-stu-id="bcc63-162">Now, the first output is:</span></span>

| <span data-ttu-id="bcc63-163">Sequência</span><span class="sxs-lookup"><span data-stu-id="bcc63-163">Sequence</span></span> | <span data-ttu-id="bcc63-164">Type</span><span class="sxs-lookup"><span data-stu-id="bcc63-164">Type</span></span>      | <span data-ttu-id="bcc63-165">Dados</span><span class="sxs-lookup"><span data-stu-id="bcc63-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="bcc63-166">0</span><span class="sxs-lookup"><span data-stu-id="bcc63-166">0</span></span>        | <span data-ttu-id="bcc63-167">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="bcc63-167">Text node</span></span> | <span data-ttu-id="bcc63-168">Primeiro</span><span class="sxs-lookup"><span data-stu-id="bcc63-168">First</span></span>  |
| <span data-ttu-id="bcc63-169">1</span><span class="sxs-lookup"><span data-stu-id="bcc63-169">1</span></span>        | <span data-ttu-id="bcc63-170">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="bcc63-170">Text node</span></span> | <span data-ttu-id="bcc63-171">Segundo</span><span class="sxs-lookup"><span data-stu-id="bcc63-171">Second</span></span> |

<span data-ttu-id="bcc63-172">Este resultado é idêntico ao caso anterior, portanto não existem problemas negativos.</span><span class="sxs-lookup"><span data-stu-id="bcc63-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="bcc63-173">`someFlag`está `false` na segunda renderização, e a saída é:</span><span class="sxs-lookup"><span data-stu-id="bcc63-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="bcc63-174">Sequência</span><span class="sxs-lookup"><span data-stu-id="bcc63-174">Sequence</span></span> | <span data-ttu-id="bcc63-175">Type</span><span class="sxs-lookup"><span data-stu-id="bcc63-175">Type</span></span>      | <span data-ttu-id="bcc63-176">Dados</span><span class="sxs-lookup"><span data-stu-id="bcc63-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="bcc63-177">0</span><span class="sxs-lookup"><span data-stu-id="bcc63-177">0</span></span>        | <span data-ttu-id="bcc63-178">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="bcc63-178">Text node</span></span> | <span data-ttu-id="bcc63-179">Segundo</span><span class="sxs-lookup"><span data-stu-id="bcc63-179">Second</span></span> |

<span data-ttu-id="bcc63-180">Desta vez, o algoritmo diff vê que *duas* alterações ocorreram, e o algoritmo gera o seguinte script de edição:</span><span class="sxs-lookup"><span data-stu-id="bcc63-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="bcc63-181">Alterar o valor do primeiro `Second`nó de texto para .</span><span class="sxs-lookup"><span data-stu-id="bcc63-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="bcc63-182">Remova o segundo nó de texto.</span><span class="sxs-lookup"><span data-stu-id="bcc63-182">Remove the second text node.</span></span>

<span data-ttu-id="bcc63-183">A geração dos números de seqüência `if/else` perdeu todas as informações úteis sobre onde os ramos e loops estavam presentes no código original.</span><span class="sxs-lookup"><span data-stu-id="bcc63-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="bcc63-184">Isso resulta em um diff **duas vezes maior do que** antes.</span><span class="sxs-lookup"><span data-stu-id="bcc63-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="bcc63-185">Este é um exemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="bcc63-185">This is a trivial example.</span></span> <span data-ttu-id="bcc63-186">Em casos mais realistas, com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho geralmente é maior.</span><span class="sxs-lookup"><span data-stu-id="bcc63-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="bcc63-187">Em vez de identificar imediatamente quais blocos de loop ou galhos foram inseridos ou removidos, o algoritmo diff tem que reamaldiçoar profundamente nas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="bcc63-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="bcc63-188">Isso geralmente resulta em ter que construir scripts de edição mais longos porque o algoritmo diff está mal informado sobre como as estruturas antigas e novas se relacionam entre si.</span><span class="sxs-lookup"><span data-stu-id="bcc63-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="bcc63-189">Orientação e conclusões</span><span class="sxs-lookup"><span data-stu-id="bcc63-189">Guidance and conclusions</span></span>

* <span data-ttu-id="bcc63-190">O desempenho do aplicativo sofre se os números de seqüência forem gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="bcc63-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="bcc63-191">A estrutura não pode criar seus próprios números de seqüência automaticamente em tempo de execução porque as informações necessárias não existem a menos que sejam capturadas no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="bcc63-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="bcc63-192">Não escreva longos blocos de `RenderTreeBuilder` lógica implementada manualmente.</span><span class="sxs-lookup"><span data-stu-id="bcc63-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="bcc63-193">Prefira arquivos *.razor* e permita que o compilador lide com os números de seqüência.</span><span class="sxs-lookup"><span data-stu-id="bcc63-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="bcc63-194">Se você não conseguir `RenderTreeBuilder` evitar a lógica manual, divida longos blocos de código em pedaços menores envoltos em `OpenRegion` / `CloseRegion` chamadas.</span><span class="sxs-lookup"><span data-stu-id="bcc63-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="bcc63-195">Cada região tem seu próprio espaço separado de números de seqüência, para que você possa reiniciar a partir de zero (ou qualquer outro número arbitrário) dentro de cada região.</span><span class="sxs-lookup"><span data-stu-id="bcc63-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="bcc63-196">Se os números de seqüência forem codificados, o algoritmo diff só requer que os números de seqüência aumentem de valor.</span><span class="sxs-lookup"><span data-stu-id="bcc63-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="bcc63-197">O valor inicial e as lacunas são irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="bcc63-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="bcc63-198">Uma opção legítima é usar o número da linha de código como número de seqüência, ou começar a partir de zero e aumentar por uns ou centenas (ou qualquer intervalo preferido).</span><span class="sxs-lookup"><span data-stu-id="bcc63-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="bcc63-199">usa números de seqüência, enquanto outras estruturas de iu difusora de árvores não as usam.</span><span class="sxs-lookup"><span data-stu-id="bcc63-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="bcc63-200">A difusão é muito mais rápida Blazor quando os números de seqüência são usados, e tem a vantagem de uma etapa de compilação que lida com números de seqüência automaticamente para desenvolvedores que compõem arquivos *.razor.*</span><span class="sxs-lookup"><span data-stu-id="bcc63-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="bcc63-201">Realizar grandes transferências Blazor de dados em aplicativos do Servidor</span><span class="sxs-lookup"><span data-stu-id="bcc63-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="bcc63-202">Em alguns cenários, grandes quantidades de Blazordados devem ser transferidas entre JavaScript e .</span><span class="sxs-lookup"><span data-stu-id="bcc63-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="bcc63-203">Normalmente, grandes transferências de dados ocorrem quando:</span><span class="sxs-lookup"><span data-stu-id="bcc63-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="bcc63-204">ApIs do sistema de arquivos do navegador são usadas para carregar ou baixar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="bcc63-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="bcc63-205">Interop com uma biblioteca de terceiros é necessário.</span><span class="sxs-lookup"><span data-stu-id="bcc63-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="bcc63-206">No Blazor Servidor, existe uma limitação para evitar a passagem de mensagens grandes únicas que podem resultar em problemas de desempenho.</span><span class="sxs-lookup"><span data-stu-id="bcc63-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="bcc63-207">Considere a seguinte orientação ao desenvolver código Blazorque transfere dados entre JavaScript e :</span><span class="sxs-lookup"><span data-stu-id="bcc63-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="bcc63-208">Corte os dados em pedaços menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="bcc63-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="bcc63-209">Não aloque objetos grandes no código JavaScript e C#.</span><span class="sxs-lookup"><span data-stu-id="bcc63-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="bcc63-210">Não bloqueie o segmento principal de iu por longos períodos ao enviar ou receber dados.</span><span class="sxs-lookup"><span data-stu-id="bcc63-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="bcc63-211">Liberte qualquer memória consumida quando o processo estiver concluído ou cancelado.</span><span class="sxs-lookup"><span data-stu-id="bcc63-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="bcc63-212">Imponha os seguintes requisitos adicionais para fins de segurança:</span><span class="sxs-lookup"><span data-stu-id="bcc63-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="bcc63-213">Declare o arquivo máximo ou tamanho de dados que podem ser passados.</span><span class="sxs-lookup"><span data-stu-id="bcc63-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="bcc63-214">Declare a taxa mínima de upload do cliente para o servidor.</span><span class="sxs-lookup"><span data-stu-id="bcc63-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="bcc63-215">Depois que os dados são recebidos pelo servidor, os dados podem ser:</span><span class="sxs-lookup"><span data-stu-id="bcc63-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="bcc63-216">Armazenado temporariamente em um buffer de memória até que todos os segmentos sejam coletados.</span><span class="sxs-lookup"><span data-stu-id="bcc63-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="bcc63-217">Consumido imediatamente.</span><span class="sxs-lookup"><span data-stu-id="bcc63-217">Consumed immediately.</span></span> <span data-ttu-id="bcc63-218">Por exemplo, os dados podem ser armazenados imediatamente em um banco de dados ou gravados em disco à medida que cada segmento é recebido.</span><span class="sxs-lookup"><span data-stu-id="bcc63-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="bcc63-219">A classe de uploader de arquivo a seguir lida com js interop com o cliente.</span><span class="sxs-lookup"><span data-stu-id="bcc63-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="bcc63-220">A classe uploader usa o interop JS para:</span><span class="sxs-lookup"><span data-stu-id="bcc63-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="bcc63-221">Pesquise o cliente para enviar um segmento de dados.</span><span class="sxs-lookup"><span data-stu-id="bcc63-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="bcc63-222">Aborte a transação se os horários de votação forem cancelados.</span><span class="sxs-lookup"><span data-stu-id="bcc63-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="bcc63-223">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="bcc63-223">In the preceding example:</span></span>

* <span data-ttu-id="bcc63-224">O `_maxBase64SegmentSize` é `8192`definido para, `_maxBase64SegmentSize = _segmentSize * 4 / 3`que é calculado a partir de .</span><span class="sxs-lookup"><span data-stu-id="bcc63-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="bcc63-225">As APIs de gerenciamento de memória .NET Core de baixo `_uploadedSegments`nível são usadas para armazenar os segmentos de memória no servidor em .</span><span class="sxs-lookup"><span data-stu-id="bcc63-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="bcc63-226">Um `ReceiveFile` método é usado para lidar com o upload através do interop JS:</span><span class="sxs-lookup"><span data-stu-id="bcc63-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="bcc63-227">O tamanho do arquivo é determinado em bytes através de JS interop com `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span><span class="sxs-lookup"><span data-stu-id="bcc63-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="bcc63-228">O número de segmentos a `numberOfSegments`serem recebidos é calculado e armazenado em .</span><span class="sxs-lookup"><span data-stu-id="bcc63-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="bcc63-229">Os segmentos são `for` solicitados em um `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`loop através de JS interop com .</span><span class="sxs-lookup"><span data-stu-id="bcc63-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="bcc63-230">Todos os segmentos, menos o último, devem ser 8.192 bytes antes da decodificação.</span><span class="sxs-lookup"><span data-stu-id="bcc63-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="bcc63-231">O cliente é forçado a enviar os dados de forma eficiente.</span><span class="sxs-lookup"><span data-stu-id="bcc63-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="bcc63-232">Para cada segmento recebido, as verificações são realizadas antes da decodificação com <xref:System.Convert.TryFromBase64String*>.</span><span class="sxs-lookup"><span data-stu-id="bcc63-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="bcc63-233">Um fluxo com os dados é <xref:System.IO.Stream> `SegmentedStream`retornado como um novo ( ) após o upload ser concluído.</span><span class="sxs-lookup"><span data-stu-id="bcc63-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="bcc63-234">A classe de fluxo segmentado expõe a lista de <xref:System.IO.Stream>segmentos como um não-buscado não-procurado:</span><span class="sxs-lookup"><span data-stu-id="bcc63-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="bcc63-235">O código a seguir implementa funções JavaScript para receber os dados:</span><span class="sxs-lookup"><span data-stu-id="bcc63-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
