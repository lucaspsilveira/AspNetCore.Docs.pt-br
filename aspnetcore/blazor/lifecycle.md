---
title: Ciclo Blazor de vida ASP.NET Core
author: guardrex
description: Saiba como usar Razor métodos de ciclo de vida do Blazor componente em ASP.NET Core aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 571f14247efe08ac6abbd6d1e2720656f94c213c
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967448"
---
# <a name="aspnet-core-blazor-lifecycle"></a>Ciclo Blazor de vida ASP.NET Core

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

A Blazor estrutura inclui métodos de ciclo de vida síncronos e assíncronos. Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

### <a name="component-initialization-methods"></a>Métodos de inicialização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai. Use `OnInitializedAsync` quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.

Para uma operação síncrona, substitua `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Para executar uma operação assíncrona, `OnInitializedAsync` substitua e use `await` a palavra-chave na operação:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorAplicativos de servidor que preparam `OnInitializedAsync` [sua chamada de conteúdo](xref:blazor/hosting-model-configuration#render-mode) **_duas vezes_**:

* Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.
* Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.

Para impedir que o código `OnInitializedAsync` do desenvolvedor seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .

Enquanto um Blazor aplicativo de servidor está sendo renderizado, determinadas ações, como chamar em JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida. Os componentes podem precisar ser renderizados de forma diferente quando renderizados. Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .

### <a name="before-parameters-are-set"></a>Antes de os parâmetros serem definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>define os parâmetros fornecidos pelo pai do componente na árvore de renderização:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>contém o conjunto inteiro de valores de parâmetro a `SetParametersAsync` cada vez que é chamado.

A implementação padrão de `SetParametersAsync` define o valor de cada propriedade com o `[Parameter]` atributo `[CascadingParameter]` ou que tem um valor correspondente no `ParameterView`. Os parâmetros que não têm um valor correspondente `ParameterView` em são deixados inalterados.

Se `base.SetParametersAync` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária. Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .

### <a name="after-parameters-are-set"></a>Depois que os parâmetros são definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> são chamados de:

* Quando o componente é inicializado e recebeu seu primeiro conjunto de parâmetros de seu componente pai.
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
> O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer `OnParametersSetAsync` durante o evento de ciclo de vida.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .

### <a name="after-component-render"></a>Após renderização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> são chamados após a conclusão da renderização de um componente. Referências de elemento e componente são preenchidas neste ponto. Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.

O `firstRender` parâmetro para `OnAfterRenderAsync` e `OnAfterRender`:

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
> O trabalho assíncrono imediatamente após a renderização deve ocorrer `OnAfterRenderAsync` durante o evento do ciclo de vida.
>
> Mesmo que você retorne <xref:System.Threading.Tasks.Task> um `OnAfterRenderAsync`do, a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída. Isso é para evitar um loop de renderização infinito. Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`e `OnAfterRenderAsync` *não são chamados durante o pré-processamento no servidor.*

Se algum manipulador de eventos estiver configurado, desvincule-os na alienação. Para obter mais informações, consulte a seção [descarte de componentes com IDisposable](#component-disposal-with-idisposable) .

### <a name="suppress-ui-refreshing"></a>Suprimir atualização da interface do usuário

Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir a atualização da interface do usuário. Se a implementação retornar `true`, a interface do usuário será atualizada:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`é chamado cada vez que o componente é renderizado.

Mesmo se `ShouldRender` for substituído, o componente sempre será renderizado inicialmente.

## <a name="state-changes"></a>Alterações de estado

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Notifica o componente de que seu estado foi alterado. Quando aplicável, a `StateHasChanged` chamada faz com que o componente seja rerenderizado.

## <a name="handle-incomplete-async-actions-at-render"></a>Tratar ações assíncronas incompletas no processamento

Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado. Os objetos podem `null` ser ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução. Forneça a lógica de renderização para confirmar que os objetos são inicializados. Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma `null`mensagem de carregamento) enquanto objetos são.

No `FetchData` componente dos Blazor modelos, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`). Quando `forecasts` é `null`, uma mensagem de carregamento é exibida para o usuário. Depois que `Task` o retornado `OnInitializedAsync` por for concluído, o componente será rerenderizado com o estado atualizado.

*Páginas/FetchData. Razor* no modelo Blazor de servidor:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Descarte de componentes com IDisposable

Se um componente implementa <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário. O componente a seguir `@implements IDisposable` usa o `Dispose` e o método:

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
> A <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> chamada `Dispose` no não tem suporte. `StateHasChanged`pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.

Cancele a assinatura de manipuladores de eventos de eventos .NET. Os exemplos de [ Blazor formulário](xref:blazor/forms-validation) a seguir mostram como desvincular um manipulador de eventos `Dispose` no método:

* Campo privado e abordagem lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Abordagem do método privado

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Tratar erros

Para obter informações sobre como lidar com erros durante a execução <xref:blazor/handle-errors#lifecycle-methods>do método de ciclo de vida, consulte.

## <a name="stateful-reconnection-after-prerendering"></a>Reconexão com estado após o pré-processamento

Em um Blazor aplicativo de servidor `RenderMode` quando `ServerPrerendered`é, o componente é inicialmente renderizado estaticamente como parte da página. Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo. Se o método de ciclo de vida de [{Async} OnInitialized](#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes*:

* Quando o componente é renderizado estaticamente.
* Depois que a conexão do servidor tiver sido estabelecida.

Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.

Para evitar o cenário de processamento duplo em um Blazor aplicativo de servidor:

* Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.
* Use o identificador durante o pré-processamento para salvar o estado do componente.
* Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.

O código a seguir demonstra uma `WeatherForecastService` atualização em um aplicativo de Blazor servidor baseado em modelo que evita a renderização dupla:

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

Para obter mais informações sobre `RenderMode`o, <xref:blazor/hosting-model-configuration#render-mode>consulte.

## <a name="detect-when-the-app-is-prerendering"></a>Detectar quando o aplicativo está sendo renderizado

[!INCLUDE[](~/includes/blazor-prerendering.md)]
