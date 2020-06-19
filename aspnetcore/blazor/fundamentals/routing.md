---
title: Roteamento de ASP.NET Core Blazor
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 9668077d9b59ff20b1aab0b496278f2460e5ad2a
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103556"
---
# <a name="aspnet-core-blazor-routing"></a>Roteamento de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex)

Saiba como rotear solicitações e como usar o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente para criar links de navegação em Blazor aplicativos.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integração de roteamento de ponto de extremidade ASP.NET Core

BlazorO servidor é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing). Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> no `Startup.Configure` :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

A configuração mais típica é rotear todas as solicitações para uma Razor página, que atua como o host da parte do lado do servidor do Blazor aplicativo do servidor. Por convenção, a página *host* geralmente é chamada de *_Host. cshtml*. A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota. A rota de fallback é considerada quando outras rotas não correspondem. Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo do Blazor servidor.

## <a name="route-templates"></a>Modelos de rota

O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite o roteamento para cada componente com uma rota especificada. O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente aparece no arquivo *app. Razor* :

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

Em tempo de execução, o <xref:Microsoft.AspNetCore.Components.RouteView> componente:

* Recebe o <xref:Microsoft.AspNetCore.Components.RouteData> do <xref:Microsoft.AspNetCore.Components.Routing.Router> junto com os parâmetros desejados.
* Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.

Opcionalmente, você pode especificar um <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout. Os Blazor modelos padrão especificam o `MainLayout` componente. *MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo. Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .

Vários modelos de rota podem ser aplicados a um componente. O componente a seguir responde a solicitações para o `/BlazorRoute` e o `/DifferentBlazorRoute` :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu arquivo *wwwroot/index.html* ( Blazor Webassembly) ou no arquivo *pages/_Host. cshtml* ( Blazor servidor) com o caminho base do aplicativo especificado no `href` atributo ( `<base href="/">` ). Para obter mais informações, consulte <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fornecer conteúdo personalizado quando o conteúdo não for encontrado

O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.

No arquivo *app. Razor* , defina conteúdo personalizado no <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parâmetro de modelo do <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:

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

O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos. Para aplicar um layout padrão ao <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, consulte <xref:blazor/layouts> .

## <a name="route-to-components-from-multiple-assemblies"></a>Rotear para componentes de vários assemblies

Use o <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parâmetro para especificar assemblies adicionais para o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente a ser considerado ao procurar componentes roteáveis. Os assemblies especificados são considerados além do `AppAssembly` assembly especificado. No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada. O exemplo a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> resulta no suporte de roteamento para `Component1` :

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
* O `Id` segmento é um inteiro ( `int` ).

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
> As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou <xref:System.DateTime>) sempre usam a cultura invariável. Essas restrições consideram que a URL não é localizável.

### <a name="routing-with-urls-that-contain-dots"></a>Roteamento com URLs que contêm pontos

Em Blazor aplicativos de servidor, a rota padrão em *_Host. cshtml* é `/` ( `@page "/"` ). Uma URL de solicitação que contém um ponto ( `.` ) não é correspondida pela rota padrão porque a URL parece solicitar um arquivo. Um Blazor aplicativo retorna uma resposta *404-não encontrada* para um arquivo estático que não existe. Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:

```cshtml
@page "/{**path}"
```

O `"/{**path}"` modelo inclui:

* Sintaxe *catch-all* de asterisco duplo ( `**` ) para capturar o caminho entre vários limites de pasta sem barras invertidas de codificação ( `/` ).
* `path`nome do parâmetro de rota.

> [!NOTE]
> A sintaxe de parâmetro *catch-all* ( `*` / `**` ) **não** tem suporte em Razor componentes (*. Razor*).

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Componente NavLink

Use um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação. Um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual. A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.

O componente a seguir `NavMenu` cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra como usar <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componentes:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Há duas <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando corresponde à URL atual inteira.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*padrão*): o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando ele corresponde a qualquer prefixo da URL atual.

No exemplo anterior, a página inicial <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ). O segundo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` ).

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Atributos de componente adicionais são passados para a marca de âncora renderizada. No exemplo a seguir, o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente inclui o `target` atributo:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

A seguinte marcação HTML é renderizada:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Auxiliares de URI e estado de navegação

Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#. <xref:Microsoft.AspNetCore.Components.NavigationManager>fornece o evento e os métodos mostrados na tabela a seguir.

| Membro | Descrição |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtém o URI absoluto atual. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto. Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde ao `href` atributo no elemento do documento `<base>` em *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor servidor). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navega para o URI especificado. Se `forceLoad` for `true` :<ul><li>O roteamento do lado do cliente é ignorado.</li><li>O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Um evento que é acionado quando o local de navegação é alterado. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converte um URI relativo em um URI absoluto. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dado um URI de base (por exemplo, um URI retornado anteriormente pelo <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte um URI absoluto em um URI relativo ao prefixo de URI de base. |

O componente a seguir navega para o componente do aplicativo `Counter` quando o botão é selecionado:

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

O componente a seguir manipula um evento de alteração de local definindo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> . O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework. A desconexão do método permite a coleta de lixo do componente.

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

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: A URL do novo local.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor interceptou a navegação do navegador. Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> fez com que a navegação ocorresse.

Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .
