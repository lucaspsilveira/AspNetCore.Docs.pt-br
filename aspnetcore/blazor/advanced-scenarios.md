---
title: ASP.NET Core Blazor cenários avançados
author: guardrex
description: Saiba mais sobre cenários avançados no Blazor , incluindo como incorporar a lógica de RenderTreeBuilder manual em um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 3345f545e230ada78e6c66fc9eb049060d5794d6
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851153"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="d6d15-103">ASP.NET Core Blazor cenários avançados</span><span class="sxs-lookup"><span data-stu-id="d6d15-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="d6d15-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d6d15-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a>Blazor<span data-ttu-id="d6d15-105">Manipulador de circuito de servidor</span><span class="sxs-lookup"><span data-stu-id="d6d15-105"> Server circuit handler</span></span>

Blazor<span data-ttu-id="d6d15-106">O servidor permite que o código defina um *manipulador de circuito*, que permite a execução de código em alterações no estado do circuito de um usuário.</span><span class="sxs-lookup"><span data-stu-id="d6d15-106"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="d6d15-107">Um manipulador de circuito é implementado derivando de `CircuitHandler` e registrando a classe no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d6d15-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="d6d15-108">O exemplo a seguir de um manipulador de circuito rastreia SignalR conexões abertas:</span><span class="sxs-lookup"><span data-stu-id="d6d15-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="d6d15-109">Os manipuladores de circuito são registrados usando DI.</span><span class="sxs-lookup"><span data-stu-id="d6d15-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="d6d15-110">Instâncias com escopo são criadas por instância de um circuito.</span><span class="sxs-lookup"><span data-stu-id="d6d15-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="d6d15-111">Usando o `TrackingCircuitHandler` no exemplo anterior, um serviço singleton é criado porque o estado de todos os circuitos deve ser acompanhado:</span><span class="sxs-lookup"><span data-stu-id="d6d15-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="d6d15-112">Se os métodos de um manipulador de circuitos personalizados lançarem uma exceção sem tratamento, a exceção será fatal para o Blazor circuito do servidor.</span><span class="sxs-lookup"><span data-stu-id="d6d15-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="d6d15-113">Para tolerar exceções no código de um manipulador ou em métodos chamados, empacote o código em uma ou mais instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="d6d15-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="d6d15-114">Quando um circuito termina porque um usuário se desconectou e a estrutura está limpando o estado do circuito, a estrutura descarta o escopo de DI do circuito.</span><span class="sxs-lookup"><span data-stu-id="d6d15-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="d6d15-115">Descartar o escopo descarta todos os serviços de DI no escopo do circuito que implementam o <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="d6d15-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="d6d15-116">Se qualquer serviço de DI lançar uma exceção sem tratamento durante a alienação, a estrutura registrará a exceção.</span><span class="sxs-lookup"><span data-stu-id="d6d15-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="d6d15-117">Lógica RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="d6d15-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="d6d15-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes em código C#.</span><span class="sxs-lookup"><span data-stu-id="d6d15-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="d6d15-119">O uso do <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="d6d15-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="d6d15-120">Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="d6d15-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="d6d15-121">Considere o seguinte `PetDetails` componente, que pode ser compilado manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="d6d15-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="d6d15-122">No exemplo a seguir, o loop no `CreateComponent` método gera três `PetDetails` componentes.</span><span class="sxs-lookup"><span data-stu-id="d6d15-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="d6d15-123">Ao chamar <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> métodos para criar os componentes ( `OpenComponent` e `AddAttribute` ), os números de sequência são números de linha de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="d6d15-123">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="d6d15-124">O Blazor algoritmo de diferença depende dos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas.</span><span class="sxs-lookup"><span data-stu-id="d6d15-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="d6d15-125">Ao criar um componente com <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> métodos, codifique os argumentos para números de sequência.</span><span class="sxs-lookup"><span data-stu-id="d6d15-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="d6d15-126">**O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.**</span><span class="sxs-lookup"><span data-stu-id="d6d15-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="d6d15-127">Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="d6d15-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="d6d15-128">`BuiltContent`componente</span><span class="sxs-lookup"><span data-stu-id="d6d15-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="d6d15-129">Os tipos no <xref:Microsoft.AspNetCore.Components.RenderTree> permitem o processamento dos *resultados* das operações de renderização.</span><span class="sxs-lookup"><span data-stu-id="d6d15-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="d6d15-130">Esses são detalhes internos da Blazor implementação da estrutura.</span><span class="sxs-lookup"><span data-stu-id="d6d15-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="d6d15-131">Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="d6d15-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="d6d15-132">Números de sequência se relacionam a números de linha de código e não a ordem de execução</span><span class="sxs-lookup"><span data-stu-id="d6d15-132">Sequence numbers relate to code line numbers and not execution order</span></span>

Razor<span data-ttu-id="d6d15-133">os arquivos de componente (*. Razor*) são sempre compilados.</span><span class="sxs-lookup"><span data-stu-id="d6d15-133"> component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="d6d15-134">A compilação é uma vantagem potencial sobre a interpretação do código, pois a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="d6d15-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="d6d15-135">Um exemplo importante desses aprimoramentos envolve *números de sequência*.</span><span class="sxs-lookup"><span data-stu-id="d6d15-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="d6d15-136">Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas.</span><span class="sxs-lookup"><span data-stu-id="d6d15-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="d6d15-137">O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.</span><span class="sxs-lookup"><span data-stu-id="d6d15-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="d6d15-138">Considere o seguinte Razor arquivo de componente (*. Razor*):</span><span class="sxs-lookup"><span data-stu-id="d6d15-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="d6d15-139">O código anterior é compilado para algo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="d6d15-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="d6d15-140">Quando o código é executado pela primeira vez, se `someFlag` for `true` , o Construtor receberá:</span><span class="sxs-lookup"><span data-stu-id="d6d15-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="d6d15-141">Sequência</span><span class="sxs-lookup"><span data-stu-id="d6d15-141">Sequence</span></span> | <span data-ttu-id="d6d15-142">Digite</span><span class="sxs-lookup"><span data-stu-id="d6d15-142">Type</span></span>      | <span data-ttu-id="d6d15-143">Dados</span><span class="sxs-lookup"><span data-stu-id="d6d15-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="d6d15-144">0</span><span class="sxs-lookup"><span data-stu-id="d6d15-144">0</span></span>        | <span data-ttu-id="d6d15-145">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="d6d15-145">Text node</span></span> | <span data-ttu-id="d6d15-146">Primeiro</span><span class="sxs-lookup"><span data-stu-id="d6d15-146">First</span></span>  |
| <span data-ttu-id="d6d15-147">1</span><span class="sxs-lookup"><span data-stu-id="d6d15-147">1</span></span>        | <span data-ttu-id="d6d15-148">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="d6d15-148">Text node</span></span> | <span data-ttu-id="d6d15-149">Segundo</span><span class="sxs-lookup"><span data-stu-id="d6d15-149">Second</span></span> |

<span data-ttu-id="d6d15-150">Imagine que `someFlag` se torna `false` e a marcação é renderizada novamente.</span><span class="sxs-lookup"><span data-stu-id="d6d15-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="d6d15-151">Desta vez, o Construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="d6d15-151">This time, the builder receives:</span></span>

| <span data-ttu-id="d6d15-152">Sequência</span><span class="sxs-lookup"><span data-stu-id="d6d15-152">Sequence</span></span> | <span data-ttu-id="d6d15-153">Digite</span><span class="sxs-lookup"><span data-stu-id="d6d15-153">Type</span></span>       | <span data-ttu-id="d6d15-154">Dados</span><span class="sxs-lookup"><span data-stu-id="d6d15-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="d6d15-155">1</span><span class="sxs-lookup"><span data-stu-id="d6d15-155">1</span></span>        | <span data-ttu-id="d6d15-156">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="d6d15-156">Text node</span></span>  | <span data-ttu-id="d6d15-157">Segundo</span><span class="sxs-lookup"><span data-stu-id="d6d15-157">Second</span></span> |

<span data-ttu-id="d6d15-158">Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição*trivial:</span><span class="sxs-lookup"><span data-stu-id="d6d15-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="d6d15-159">Remova o primeiro nó de texto.</span><span class="sxs-lookup"><span data-stu-id="d6d15-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="d6d15-160">O problema com a geração de números de sequência programaticamente</span><span class="sxs-lookup"><span data-stu-id="d6d15-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="d6d15-161">Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="d6d15-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="d6d15-162">Agora, a primeira saída é:</span><span class="sxs-lookup"><span data-stu-id="d6d15-162">Now, the first output is:</span></span>

| <span data-ttu-id="d6d15-163">Sequência</span><span class="sxs-lookup"><span data-stu-id="d6d15-163">Sequence</span></span> | <span data-ttu-id="d6d15-164">Digite</span><span class="sxs-lookup"><span data-stu-id="d6d15-164">Type</span></span>      | <span data-ttu-id="d6d15-165">Dados</span><span class="sxs-lookup"><span data-stu-id="d6d15-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="d6d15-166">0</span><span class="sxs-lookup"><span data-stu-id="d6d15-166">0</span></span>        | <span data-ttu-id="d6d15-167">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="d6d15-167">Text node</span></span> | <span data-ttu-id="d6d15-168">Primeiro</span><span class="sxs-lookup"><span data-stu-id="d6d15-168">First</span></span>  |
| <span data-ttu-id="d6d15-169">1</span><span class="sxs-lookup"><span data-stu-id="d6d15-169">1</span></span>        | <span data-ttu-id="d6d15-170">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="d6d15-170">Text node</span></span> | <span data-ttu-id="d6d15-171">Segundo</span><span class="sxs-lookup"><span data-stu-id="d6d15-171">Second</span></span> |

<span data-ttu-id="d6d15-172">Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos.</span><span class="sxs-lookup"><span data-stu-id="d6d15-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="d6d15-173">`someFlag`está `false` no segundo processamento e a saída é:</span><span class="sxs-lookup"><span data-stu-id="d6d15-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="d6d15-174">Sequência</span><span class="sxs-lookup"><span data-stu-id="d6d15-174">Sequence</span></span> | <span data-ttu-id="d6d15-175">Digite</span><span class="sxs-lookup"><span data-stu-id="d6d15-175">Type</span></span>      | <span data-ttu-id="d6d15-176">Dados</span><span class="sxs-lookup"><span data-stu-id="d6d15-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="d6d15-177">0</span><span class="sxs-lookup"><span data-stu-id="d6d15-177">0</span></span>        | <span data-ttu-id="d6d15-178">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="d6d15-178">Text node</span></span> | <span data-ttu-id="d6d15-179">Segundo</span><span class="sxs-lookup"><span data-stu-id="d6d15-179">Second</span></span> |

<span data-ttu-id="d6d15-180">Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:</span><span class="sxs-lookup"><span data-stu-id="d6d15-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="d6d15-181">Altere o valor do primeiro nó de texto para `Second` .</span><span class="sxs-lookup"><span data-stu-id="d6d15-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="d6d15-182">Remova o segundo nó de texto.</span><span class="sxs-lookup"><span data-stu-id="d6d15-182">Remove the second text node.</span></span>

<span data-ttu-id="d6d15-183">A geração de números de sequência perdeu todas as informações úteis sobre onde os `if/else` branches e loops estavam presentes no código original.</span><span class="sxs-lookup"><span data-stu-id="d6d15-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="d6d15-184">Isso resulta em uma comparação **duas vezes mais longa** do que antes.</span><span class="sxs-lookup"><span data-stu-id="d6d15-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="d6d15-185">Esse é um exemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="d6d15-185">This is a trivial example.</span></span> <span data-ttu-id="d6d15-186">Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é geralmente mais alto.</span><span class="sxs-lookup"><span data-stu-id="d6d15-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="d6d15-187">Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="d6d15-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="d6d15-188">Isso geralmente resulta na criação de scripts de edição mais longos porque o algoritmo diff é informado inconscientemente sobre como as estruturas antigas e novas se relacionam entre si.</span><span class="sxs-lookup"><span data-stu-id="d6d15-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="d6d15-189">Diretrizes e conclusões</span><span class="sxs-lookup"><span data-stu-id="d6d15-189">Guidance and conclusions</span></span>

* <span data-ttu-id="d6d15-190">O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="d6d15-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="d6d15-191">A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="d6d15-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="d6d15-192">Não grave blocos longos de lógica implementada manualmente <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d6d15-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="d6d15-193">Prefira arquivos *. Razor* e permita que o compilador lide com os números de sequência.</span><span class="sxs-lookup"><span data-stu-id="d6d15-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="d6d15-194">Se não for possível evitar lógica manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , divida blocos longos de código em partes menores encapsuladas em <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> chamadas.</span><span class="sxs-lookup"><span data-stu-id="d6d15-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="d6d15-195">Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.</span><span class="sxs-lookup"><span data-stu-id="d6d15-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="d6d15-196">Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor.</span><span class="sxs-lookup"><span data-stu-id="d6d15-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="d6d15-197">O valor inicial e as lacunas são irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="d6d15-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="d6d15-198">Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial).</span><span class="sxs-lookup"><span data-stu-id="d6d15-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="d6d15-199">usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam.</span><span class="sxs-lookup"><span data-stu-id="d6d15-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="d6d15-200">A comparação é muito mais rápida quando números de sequência são usados e Blazor tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam arquivos *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="d6d15-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="d6d15-201">Executar grandes transferências de dados em Blazor aplicativos de servidor</span><span class="sxs-lookup"><span data-stu-id="d6d15-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="d6d15-202">Em alguns cenários, grandes quantidades de dados devem ser transferidas entre o JavaScript e o Blazor .</span><span class="sxs-lookup"><span data-stu-id="d6d15-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="d6d15-203">Normalmente, grandes transferências de dados ocorrem quando:</span><span class="sxs-lookup"><span data-stu-id="d6d15-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="d6d15-204">As APIs do sistema de arquivos do navegador são usadas para carregar ou baixar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="d6d15-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="d6d15-205">A interoperabilidade com uma biblioteca de terceiros é necessária.</span><span class="sxs-lookup"><span data-stu-id="d6d15-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="d6d15-206">No Blazor servidor, uma limitação está em vigor para evitar a passagem de mensagens grandes e que podem resultar em problemas de desempenho.</span><span class="sxs-lookup"><span data-stu-id="d6d15-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="d6d15-207">Considere as seguintes diretrizes ao desenvolver código que transfere dados entre JavaScript e Blazor :</span><span class="sxs-lookup"><span data-stu-id="d6d15-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="d6d15-208">Divida os dados em partes menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="d6d15-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="d6d15-209">Não aloque objetos grandes em JavaScript e código C#.</span><span class="sxs-lookup"><span data-stu-id="d6d15-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="d6d15-210">Não bloqueie o thread da interface do usuário principal por longos períodos ao enviar ou receber dados.</span><span class="sxs-lookup"><span data-stu-id="d6d15-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="d6d15-211">Libere qualquer memória consumida quando o processo for concluído ou cancelado.</span><span class="sxs-lookup"><span data-stu-id="d6d15-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="d6d15-212">Impor os seguintes requisitos adicionais para fins de segurança:</span><span class="sxs-lookup"><span data-stu-id="d6d15-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="d6d15-213">Declare o tamanho máximo de arquivo ou de dados que pode ser passado.</span><span class="sxs-lookup"><span data-stu-id="d6d15-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="d6d15-214">Declare a taxa de carregamento mínima do cliente para o servidor.</span><span class="sxs-lookup"><span data-stu-id="d6d15-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="d6d15-215">Depois que os dados são recebidos pelo servidor, os dados podem ser:</span><span class="sxs-lookup"><span data-stu-id="d6d15-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="d6d15-216">Temporariamente armazenados em um buffer de memória até que todos os segmentos sejam coletados.</span><span class="sxs-lookup"><span data-stu-id="d6d15-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="d6d15-217">Consumido imediatamente.</span><span class="sxs-lookup"><span data-stu-id="d6d15-217">Consumed immediately.</span></span> <span data-ttu-id="d6d15-218">Por exemplo, os dados podem ser armazenados imediatamente em um banco de dado ou gravados no disco à medida que cada segmento é recebido.</span><span class="sxs-lookup"><span data-stu-id="d6d15-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="d6d15-219">A classe de carregador de arquivos a seguir manipula a interoperabilidade de JS com o cliente.</span><span class="sxs-lookup"><span data-stu-id="d6d15-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="d6d15-220">A classe do carregador usa a interoperabilidade JS para:</span><span class="sxs-lookup"><span data-stu-id="d6d15-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="d6d15-221">Sondar o cliente para enviar um segmento de dados.</span><span class="sxs-lookup"><span data-stu-id="d6d15-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="d6d15-222">Anular a transação se a sondagem atingir o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d6d15-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="d6d15-223">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="d6d15-223">In the preceding example:</span></span>

* <span data-ttu-id="d6d15-224">O `maxBase64SegmentSize` é definido como `8192` , que é calculado a partir de `maxBase64SegmentSize = segmentSize * 4 / 3` .</span><span class="sxs-lookup"><span data-stu-id="d6d15-224">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="d6d15-225">As APIs de gerenciamento de memória do .NET Core de nível baixo são usadas para armazenar os segmentos de memória no servidor no `uploadedSegments` .</span><span class="sxs-lookup"><span data-stu-id="d6d15-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="d6d15-226">Um `ReceiveFile` método é usado para lidar com o upload por meio da interoperabilidade js:</span><span class="sxs-lookup"><span data-stu-id="d6d15-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="d6d15-227">O tamanho do arquivo é determinado em bytes por meio de interoperabilidade do JS com `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` .</span><span class="sxs-lookup"><span data-stu-id="d6d15-227">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="d6d15-228">O número de segmentos a serem recebidos é calculado e armazenado em `numberOfSegments` .</span><span class="sxs-lookup"><span data-stu-id="d6d15-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="d6d15-229">Os segmentos são solicitados em um `for` loop por meio de interoperabilidade js com `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` .</span><span class="sxs-lookup"><span data-stu-id="d6d15-229">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="d6d15-230">Todos os segmentos, exceto os últimos, devem ser 8.192 bytes antes da decodificação.</span><span class="sxs-lookup"><span data-stu-id="d6d15-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="d6d15-231">O cliente é forçado a enviar os dados de maneira eficiente.</span><span class="sxs-lookup"><span data-stu-id="d6d15-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="d6d15-232">Para cada segmento recebido, as verificações são executadas antes de decodificar com <xref:System.Convert.TryFromBase64String%2A> .</span><span class="sxs-lookup"><span data-stu-id="d6d15-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="d6d15-233">Um fluxo com os dados é retornado como um novo <xref:System.IO.Stream> ( `SegmentedStream` ) após a conclusão do carregamento.</span><span class="sxs-lookup"><span data-stu-id="d6d15-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="d6d15-234">A classe de fluxo segmentada expõe a lista de segmentos como um não pesquisável ReadOnly <xref:System.IO.Stream> :</span><span class="sxs-lookup"><span data-stu-id="d6d15-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
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

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

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

<span data-ttu-id="d6d15-235">O código a seguir implementa as funções JavaScript para receber os dados:</span><span class="sxs-lookup"><span data-stu-id="d6d15-235">The following code implements JavaScript functions to receive the data:</span></span>

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
