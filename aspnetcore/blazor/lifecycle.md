---
title: ciclo Blazor de vida do núcleo ASP.NET
author: guardrex
description: Aprenda a usar os métodos de Blazor ciclo de vida dos componentes Razor em ASP.NET aplicativos Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791460"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ciclo Blazor de vida do núcleo ASP.NET

Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)

A Blazor estrutura inclui métodos síncronos e assíncronos do ciclo de vida. Substituir métodos do ciclo de vida para executar operações adicionais em componentes durante a inicialização e renderização dos componentes.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

### <a name="component-initialization-methods"></a>Métodos de inicialização de componentes

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai. Use `OnInitializedAsync` quando o componente realizar uma operação assíncrona e deve ser atualizado quando a operação estiver concluída.

Para uma operação síncrona, anular: `OnInitialized`

```csharp
protected override void OnInitialized()
{
    ...
}
```

Para realizar uma operação assíncrona, anule `OnInitializedAsync` e use a `await` palavra-chave na operação:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorAplicativos de servidor que `OnInitializedAsync` [prerenderizam sua](xref:blazor/hosting-model-configuration#render-mode) chamada de conteúdo duas **_vezes:_**

* Uma vez quando o componente é inicialmente renderizado estáticamente como parte da página.
* Uma segunda vez quando o navegador estabelece uma conexão de volta ao servidor.

Para evitar que `OnInitializedAsync` o código do desenvolvedor seja executado duas vezes, consulte a [reconexão Stateful após a seção de pré-renderização.](#stateful-reconnection-after-prerendering)

Embora Blazor um aplicativo do Server esteja pré-renderizando, certas ações, como chamar o JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida. Os componentes podem precisar renderizar de forma diferente quando pré-renderizados. Para obter mais informações, consulte o [Detectar quando o aplicativo estiver fazendo pré-renderização.](#detect-when-the-app-is-prerendering)

Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação. Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)

### <a name="before-parameters-are-set"></a>Antes que os parâmetros sejam definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>define parâmetros fornecidos pelo pai do componente na árvore de renderização:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>contém todo o conjunto de `SetParametersAsync` valores de parâmetros cada vez é chamado.

A implementação `SetParametersAsync` padrão de define o `[Parameter]` `[CascadingParameter]` valor de cada propriedade `ParameterView`com o ou atributo que tenha um valor correspondente no . Os parâmetros que não possuem `ParameterView` um valor correspondente são deixados inalterados.

Se `base.SetParametersAync` não for invocado, o código personalizado pode interpretar o valor dos parâmetros de entrada de qualquer maneira necessária. Por exemplo, não há necessidade de atribuir os parâmetros de entrada às propriedades da classe.

Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação. Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)

### <a name="after-parameters-are-set"></a>Depois que os parâmetros forem definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> são chamados:

* Quando o componente é inicializado e recebe seu primeiro conjunto de parâmetros a partir de seu componente pai.
* Quando o componente pai rerenderiza e fornece:
  * Apenas tipos primitivos imutáveis conhecidos dos quais pelo menos um parâmetro mudou.
  * Quaisquer parâmetros de tipo complexo. A estrutura não pode saber se os valores de um parâmetro de tipo complexo sofreram mutação internamente, por isso trata o parâmetro definido como alterado.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> O trabalho assíncrono ao aplicar parâmetros `OnParametersSetAsync` e valores de propriedade deve ocorrer durante o evento do ciclo de vida.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação. Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)

### <a name="after-component-render"></a>Após renderização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> são chamados depois que um componente tiver terminado a renderização. As referências de elementos e componentes são preenchidas neste ponto. Use este estágio para executar etapas adicionais de inicialização usando o conteúdo renderizado, como ativar bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.

O `firstRender` parâmetro `OnAfterRenderAsync` para `OnAfterRender`e:

* É definido `true` como a primeira vez que a instância do componente é renderizada.
* Pode ser usado para garantir que o trabalho de inicialização seja realizado apenas uma vez.

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
> O trabalho assíncrono imediatamente após `OnAfterRenderAsync` a renderização deve ocorrer durante o evento do ciclo de vida.
>
> Mesmo se você <xref:System.Threading.Tasks.Task> `OnAfterRenderAsync`retornar a partir de, a estrutura não agenda um novo ciclo de renderização para o seu componente uma vez que essa tarefa seja concluída. Isto é para evitar um loop de renderização infinita. É diferente dos outros métodos do ciclo de vida, que programam um novo ciclo de renderização quando a tarefa retornada é concluída.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`e `OnAfterRenderAsync` *não são chamados ao pré-renderização no servidor.*

Se algum manipulador de eventos estiver configurado, desconecte-os na eliminação. Para obter mais informações, consulte o descarte de componentes com a seção [IDisposable.](#component-disposal-with-idisposable)

### <a name="suppress-ui-refreshing"></a>Suprimir a iu de iu

Anular <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> para suprimir a iu refrescada. Se a `true`implementação retornar, a ui será atualizada:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`é chamado cada vez que o componente é renderizado.

Mesmo `ShouldRender` que seja substituído, o componente é sempre renderizado inicialmente.

## <a name="state-changes"></a>Alterações de estado

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>notifica o componente de que seu estado mudou. Quando aplicável, `StateHasChanged` a chamada faz com que o componente seja rerenderizado.

## <a name="handle-incomplete-async-actions-at-render"></a>Lidar com ações de sincronia incompletas na renderização

Ações assíncronas realizadas em eventos do ciclo de vida podem não ter sido concluídas antes da renderização do componente. Os objetos podem estar `null` ou incompletamente preenchidos com dados enquanto o método do ciclo de vida está sendo executado. Forneça lógica de renderização para confirmar se os objetos são inicializados. Renderizar elementos de IU de espaço reservado `null`(por exemplo, uma mensagem de carregamento) enquanto os objetos são .

No `FetchData` componente dos Blazor modelos, `OnInitializedAsync` é substituído para receber asaudamente`forecasts`dados de previsão (). Quando `forecasts` `null`é , uma mensagem de carregamento é exibida para o usuário. Após `Task` o retorno `OnInitializedAsync` por completo, o componente é rerenderizado com o estado atualizado.

*Páginas/FetchData.razor* no Blazor modelo Servidor:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Descarte de componentes com IDescartável

Se um componente <xref:System.IDisposable>for implementado, o [método Descarte](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da ui. Os seguintes `@implements IDisposable` usos do componente e o `Dispose` método:

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
> Ligar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` não é suportado. `StateHasChanged`pode ser invocado como parte de derrubar o renderizador, então solicitar atualizações de iu naquele momento não é suportado.

Cancelar os manipuladores de eventos de inscrição de eventos .NET. Os [ Blazor ](xref:blazor/forms-validation) exemplos de formulário a seguir mostram como `Dispose` desengajá-lo no método:

* Campo privado e abordagem lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Abordagem de método privado

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Tratar erros

Para obter informações sobre erros de manuseio <xref:blazor/handle-errors#lifecycle-methods>durante a execução do método do ciclo de vida, consulte .

## <a name="stateful-reconnection-after-prerendering"></a>Reconexão stateful após pré-renderização

Em Blazor um aplicativo `RenderMode` `ServerPrerendered`server quando é , o componente é inicialmente renderizado estáticamente como parte da página. Uma vez que o navegador estabelece uma conexão de volta ao servidor, o componente é renderizado *novamente*e o componente agora é interativo. Se o método [onInitialized{Async}](#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes:*

* Quando o componente é pré-renderizado estáticamente.
* Depois que a conexão do servidor tiver sido estabelecida.

Isso pode resultar em uma mudança perceptível nos dados exibidos na ui quando o componente é finalmente renderizado.

Para evitar o cenário de Blazor dupla renderização em um aplicativo do Servidor:

* Passe em um identificador que pode ser usado para armazenar o estado durante a pré-renderização e para recuperar o estado após a reinicialização do aplicativo.
* Use o identificador durante a pré-renderização para salvar o estado do componente.
* Use o identificador após a pré-renderização para recuperar o estado armazenado em cache.

O código a `WeatherForecastService` seguir demonstra um Blazor atualizado em um aplicativo do Servidor baseado em modelo que evita a dupla renderização:

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

Para obter mais `RenderMode`informações <xref:blazor/hosting-model-configuration#render-mode>sobre o , consulte .

## <a name="detect-when-the-app-is-prerendering"></a>Detecte quando o aplicativo está pré-renderizando

[!INCLUDE[](~/includes/blazor-prerendering.md)]
