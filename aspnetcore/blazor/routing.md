---
title: Roteamento Blazor de ASP.NET Core
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 291ec33f951008df10f85336c7abd0b3d0a50bbc
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727713"
---
# <a name="aspnet-core-blazor-routing"></a>Roteamento de ASP.NET Core mais

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Saiba como rotear solicitações e como usar o `NavLink` componente para criar links de navegação em aplicativos mais incrivelmenteos.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integração de roteamento de ponto de extremidade ASP.NET Core

O servidor mais incrivelmente é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing). Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com `MapBlazorHub` o no: `Startup.Configure`

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

A configuração mais típica é rotear todas as solicitações para uma página Razor, que atua como o host para a parte do lado do servidor do aplicativo de servidor mais grande. Por convenção, a página *host* geralmente é chamada de *_Host. cshtml*. A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota. A rota de fallback é considerada quando outras rotas não correspondem. Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo de servidor mais incrivelmente.

## <a name="route-templates"></a>Modelos de rota

O `Router` componente permite o roteamento para cada componente com uma rota especificada. O `Router` componente aparece no arquivo *app. Razor* :

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a classe gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota.

Em tempo de execução `RouteView` , o componente:

* Recebe o `RouteData` do `Router` junto com os parâmetros desejados.
* Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.

Opcionalmente, você pode especificar `DefaultLayout` um parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout. Os modelos de mais de um padrão `MainLayout` especificam o componente. *MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo. Para obter mais informações sobre layouts, <xref:blazor/layouts>consulte.

Vários modelos de rota podem ser aplicados a um componente. O componente a seguir responde a solicitações `/BlazorRoute` para `/DifferentBlazorRoute`o e o:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Para que as URLs sejam resolvidas corretamente, o aplicativo `<base>` deve incluir uma marca em seu arquivo *wwwroot/index.html* (Webassembly de mais de um ou mais) ou *páginas/_Host arquivo. cshtml* (servidor mais incrivelmente) com `href` o caminho`<base href="/">`base do aplicativo especificado no atributo (). Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fornecer conteúdo personalizado quando o conteúdo não for encontrado

O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.

No arquivo *app. Razor* , defina conteúdo personalizado no parâmetro de `NotFound` modelo do `Router` componente:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos. Para aplicar um layout padrão ao `NotFound` conteúdo, consulte <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Rotear para componentes de vários assemblies

Use o `AdditionalAssemblies` parâmetro para especificar assemblies adicionais para o `Router` componente a ser considerado ao procurar componentes roteáveis. Os assemblies especificados são considerados além do `AppAssembly`assembly especificado. No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada. O exemplo `AdditionalAssemblies` a seguir resulta no suporte de `Component1`roteamento para:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parâmetros de rota

O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Não há suporte para parâmetros opcionais. Duas `@page` diretivas são aplicadas no exemplo anterior. O primeiro permite a navegação para o componente sem um parâmetro. A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.

## <a name="route-constraints"></a>Restrições de rota

Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.

No exemplo a seguir, a rota para o `Users` componente só corresponde se:

* Um `Id` segmento de rota está presente na URL da solicitação.
* O `Id` segmento é um inteiro (`int`).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

As restrições de rota mostradas na tabela a seguir estão disponíveis. Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.

| Constraint | Exemplo           | Correspondências de exemplo                                                                  | Constante<br>culture<br>correspondência |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Não                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sim                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sim                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sim                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sim                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Não                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sim                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sim                              |

> [!WARNING]
> As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável. Essas restrições consideram que a URL não é localizável.

### <a name="routing-with-urls-that-contain-dots"></a>Roteamento com URLs que contêm pontos

Em aplicativos de servidor mais incrivelmente, a rota padrão em *_Host. cshtml* `/` é`@page "/"`(). Uma URL de solicitação que contém um ponto`.`() não é correspondida pela rota padrão porque a URL parece solicitar um arquivo. Um aplicativo mais alto retorna uma resposta *404-não encontrada* para um arquivo estático que não existe. Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:

```cshtml
@page "/{**path}"
```

O `"/{**path}"` modelo inclui:

* Sintaxe *catch-all* de asterisco duplo (`**`) para capturar o caminho entre vários limites de pasta sem barras invertidas`/`de codificação ().
* `path`nome do parâmetro de rota.

> [!NOTE]
> **Não** há suporte para a sintaxe`*`/`**`de parâmetro *catch-all* () em componentes do Razor (*. Razor*).

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Componente NavLink

Use um `NavLink` componente no lugar de elementos de hiperlink HTML`<a>`() ao criar links de navegação. Um `NavLink` componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual. A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.

O componente `NavMenu` a seguir cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra `NavLink` como usar componentes:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Há duas `NavLinkMatch` opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:

* `NavLinkMatch.All`&ndash; O `NavLink` está ativo quando corresponde à URL atual inteira.
* `NavLinkMatch.Prefix`(*padrão*) &ndash; O `NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.

No exemplo anterior, a página inicial `NavLink` `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/`). O segundo `NavLink` recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).

Atributos `NavLink` de componente adicionais são passados para a marca de âncora renderizada. No exemplo a seguir, o `NavLink` componente inclui o `target` atributo:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

A seguinte marcação HTML é renderizada:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Auxiliares de URI e estado de navegação

Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#. `NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.

| Membro | Descrição |
| ------ | ----------- |
| Uri | Obtém o URI absoluto atual. |
| BaseUri | Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto. Normalmente, `BaseUri` corresponde ao `href` atributo no elemento do `<base>` documento em *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (Blazor servidor). |
| NavigateTo | Navega para o URI especificado. Se `forceLoad` for `true`:<ul><li>O roteamento do lado do cliente é ignorado.</li><li>O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</li></ul> |
| Localchanged | Um evento que é acionado quando o local de navegação é alterado. |
| ToAbsoluteUri | Converte um URI relativo em um URI absoluto. |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Dado um URI de base (por exemplo, um URI retornado anteriormente `GetBaseUri`pelo), converte um URI absoluto em um URI relativo ao prefixo de URI de base. |

O componente a seguir navega para o componente do `Counter` aplicativo quando o botão é selecionado:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

O componente a seguir manipula um evento de local alterado. O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework. A desconexão do método permite a coleta de lixo do componente.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornece as seguintes informações sobre o evento:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; A URL do novo local.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Se `true`, Blazor interceptar a navegação do navegador. Se `false`, [navigationmanager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) fez com que a navegação ocorresse.

Para obter mais informações sobre a disposição de <xref:blazor/lifecycle#component-disposal-with-idisposable>componentes, consulte.
