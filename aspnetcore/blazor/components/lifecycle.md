---
title: BlazorCiclo de vida ASP.NET Core
author: guardrex
description: Saiba como usar Razor métodos de ciclo de vida do componente em ASP.NET Core Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/lifecycle
ms.openlocfilehash: 312a265dd251eadf876b4252e3d9f9858adcde1b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400980"
---
# <a name="aspnet-core-blazor-lifecycle"></a>BlazorCiclo de vida ASP.NET Core

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

A Blazor estrutura inclui métodos de ciclo de vida síncronos e assíncronos. Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

### <a name="before-parameters-are-set"></a>Antes de os parâmetros serem definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>define os parâmetros fornecidos pelo pai do componente na árvore de renderização:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>contém o conjunto inteiro de valores de parâmetro a cada vez que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> é chamado.

A implementação padrão de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> define o valor de cada propriedade com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo ou que tem um valor correspondente no <xref:Microsoft.AspNetCore.Components.ParameterView> . Os parâmetros que não têm um valor correspondente em <xref:Microsoft.AspNetCore.Components.ParameterView> são deixados inalterados.

Se [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária. Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .

### <a name="component-initialization-methods"></a>Métodos de inicialização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai no <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> . 

Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.

Para uma operação síncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Para executar uma operação assíncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e use o [`await`](/dotnet/csharp/language-reference/operators/await) operador na operação:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Serveraplicativos que preparam [sua chamada de conteúdo](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_duas vezes_**:

* Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.
* Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.

Para impedir que o código <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> do desenvolvedor seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .

Embora um Blazor Server aplicativo esteja sendo renderizado, determinadas ações, como chamar em JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida. Os componentes podem precisar ser renderizados de forma diferente quando renderizados. Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .

### <a name="after-parameters-are-set"></a>Depois que os parâmetros são definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> são chamados:

* Depois que o componente é inicializado no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ou no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> .
* Quando o componente pai é renderizado novamente e fornece:
  * Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.
  * Qualquer parâmetro de tipo complexo. A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> evento de ciclo de vida.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .

### <a name="after-component-render"></a>Após renderização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> são chamados após a conclusão da renderização de um componente. Referências de elemento e componente são preenchidas neste ponto. Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.

O `firstRender` parâmetro para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :

* É definido como `true` a primeira vez que a instância do componente é renderizada.
* Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> evento do ciclo de vida.
>
> Mesmo que você retorne um <xref:System.Threading.Tasks.Task> do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída. Isso é para evitar um loop de renderização infinito. Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *não são chamados durante o pré-processamento no servidor.*

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .

### <a name="suppress-ui-refreshing"></a>Suprimir atualização da interface do usuário

Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir a atualização da interface do usuário. Se a implementação retornar `true` , a interface do usuário será atualizada:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>é chamado cada vez que o componente é renderizado.

Mesmo se <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> for substituído, o componente sempre será renderizado inicialmente.

Para obter mais informações, consulte <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.

## <a name="state-changes"></a>Alterações de estado

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Notifica o componente de que seu estado foi alterado. Quando aplicável, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> a chamada faz com que o componente seja rerenderizado.

## <a name="handle-incomplete-async-actions-at-render"></a>Tratar ações assíncronas incompletas no processamento

Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado. Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução. Forneça a lógica de renderização para confirmar que os objetos são inicializados. Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto objetos são `null` .

No `FetchData` componente dos Blazor modelos, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é substituído para Asychronously receber dados de previsão ( `forecasts` ). Quando `forecasts` é `null` , uma mensagem de carregamento é exibida para o usuário. Depois que o `Task` retornado por <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> for concluído, o componente será rerenderizado com o estado atualizado.

`Pages/FetchData.razor`no Blazor Server modelo:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Descarte de componentes com IDisposable

Se um componente implementa <xref:System.IDisposable> , o [ `Dispose` método](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário. O componente a seguir usa `@implements IDisposable` o e o `Dispose` método:

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> A chamada <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> no `Dispose` não tem suporte. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.

Cancele a assinatura de manipuladores de eventos de eventos .NET. Os exemplos de [ Blazor formulário](xref:blazor/forms-validation) a seguir mostram como desvincular um manipulador de eventos no `Dispose` método:

* Campo privado e abordagem lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Abordagem do método privado

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Tratar erros

Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .

## <a name="stateful-reconnection-after-prerendering"></a>Reconexão com estado após o pré-processamento

Em um Blazor Server aplicativo quando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> é <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , o componente é inicialmente renderizado estaticamente como parte da página. Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo. Se o [`OnInitialized{Async}`](#component-initialization-methods) método de ciclo de vida para inicializar o componente estiver presente, o método será executado *duas vezes*:

* Quando o componente é renderizado estaticamente.
* Depois que a conexão do servidor tiver sido estabelecida.

Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.

Para evitar o cenário de processamento duplo em um Blazor Server aplicativo:

* Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.
* Use o identificador durante o pré-processamento para salvar o estado do componente.
* Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.

O código a seguir demonstra uma atualização `WeatherForecastService` em um aplicativo baseado em modelo Blazor Server que evita a renderização dupla:

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consulte <xref:blazor/fundamentals/additional-scenarios#render-mode> .

## <a name="detect-when-the-app-is-prerendering"></a>Detectar quando o aplicativo está sendo renderizado

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a>Trabalho em segundo plano cancelável

Os componentes geralmente executam trabalho em segundo plano de longa execução, como fazer chamadas de rede ( <xref:System.Net.Http.HttpClient> ) e interagir com bancos de dados. É desejável interromper o trabalho em segundo plano para conservar recursos do sistema em várias situações. Por exemplo, as operações assíncronas em segundo plano não são interrompidas automaticamente quando um usuário sai de um componente.

Outros motivos pelos quais os itens de trabalho em segundo plano podem exigir cancelamento incluem:

* Uma tarefa em execução em segundo plano foi iniciada com dados de entrada ou parâmetros de processamento com falha.
* O conjunto atual de itens de trabalho em segundo plano em execução deve ser substituído por um novo conjunto de itens de trabalho.
* A prioridade das tarefas em execução no momento deve ser alterada.
* O aplicativo deve ser desligado para reimplantá-lo no servidor.
* Os recursos do servidor se tornam limitados, exigindo o reagendamento de itens de trabalho em segundo plano.

Para implementar um padrão de trabalho de segundo plano cancelável em um componente:

* Use um <xref:System.Threading.CancellationTokenSource> e <xref:System.Threading.CancellationToken> .
* Na [alienação do componente](#component-disposal-with-idisposable) e a qualquer cancelamento do ponto, é desejável cancelar manualmente o token, chamar [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para sinalizar que o trabalho em segundo plano deve ser cancelado.
* Após a chamada assíncrona retornar, chame <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> no token.

No exemplo a seguir:

* `await Task.Delay(5000, cts.Token);`representa o trabalho em segundo plano assíncrono de execução longa.
* `BackgroundResourceMethod`representa um método de plano de fundo de execução longa que não deve iniciar se o `Resource` for descartado antes de o método ser chamado.

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
