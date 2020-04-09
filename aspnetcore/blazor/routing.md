---
title: roteamento do núcleo Blazor ASP.NET
author: guardrex
description: Saiba como encaminhar solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218889"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Saiba como encaminhar solicitações e `NavLink` como usar o componente para criar links de navegação em aplicativos Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET integração de roteamento do ponto final do Core

O Blazor Server é integrado [ao ASP.NET Core Endpoint Routing](xref:fundamentals/routing). Um aplicativo ASP.NET Core está configurado para aceitar conexões `Startup.Configure`recebidas para componentes interativos com: `MapBlazorHub`

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

A configuração mais típica é encaminhar todas as solicitações para uma página de Navalha, que funciona como o host para a parte do lado do servidor do aplicativo Blazor Server. Por convenção, a página *de host* geralmente é nomeada *_Host.cshtml*. A rota especificada no arquivo host é chamada de *rota de recuo* porque opera com uma baixa prioridade na correspondência de rotas. A rota de recuo é considerada quando outras rotas não correspondem. Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo Blazor Server.

## <a name="route-templates"></a>Modelos de rota

O `Router` componente permite o roteamento para cada componente com uma rota especificada. O `Router` componente aparece no arquivo *App.razor:*

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

Quando um arquivo *.razor* com uma `@page` diretiva é compilado, <xref:Microsoft.AspNetCore.Components.RouteAttribute> a classe gerada é fornecida especificando o modelo de rota.

No tempo de `RouteView` execução, o componente:

* Recebe `RouteData` o `Router` do junto com quaisquer parâmetros desejados.
* Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.

Você pode especificar `DefaultLayout` opcionalmente um parâmetro com uma classe de layout para usar para componentes que não especificam um layout. Os modelos padrão do `MainLayout` Blazor especificam o componente. *MainLayout.razor* está na pasta *compartilhada* do projeto de modelo. Para obter mais informações <xref:blazor/layouts>sobre layouts, consulte .

Vários modelos de rota podem ser aplicados a um componente. O componente a seguir `/BlazorRoute` responde `/DifferentBlazorRoute`a solicitações e:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Para que os URLs resolvam corretamente, o aplicativo deve incluir `<base>` uma tag em seu arquivo *wwwroot/index.html* (Blazor WebAssembly) ou *pages/_Host.cshtml* file (Blazor Server) com o caminho base do aplicativo especificado no atributo `href` ().`<base href="/">` Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Forneça conteúdo personalizado quando o conteúdo não for encontrado

O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.

No arquivo *App.razor,* defina `NotFound` o conteúdo personalizado `Router` no parâmetro modelo do componente:

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

O conteúdo `<NotFound>` das tags pode incluir itens arbitrários, como outros componentes interativos. Para aplicar um `NotFound` layout padrão <xref:blazor/layouts>ao conteúdo, consulte .

## <a name="route-to-components-from-multiple-assemblies"></a>Rota para componentes de vários conjuntos

Use `AdditionalAssemblies` o parâmetro para especificar `Router` conjuntos adicionais para o componente a considerar ao procurar componentes roteáveis. As assembléias especificadas são `AppAssembly`consideradas além do conjunto especificado. No exemplo a `Component1` seguir, está um componente routable definido em uma biblioteca de classes referenciada. O `AdditionalAssemblies` exemplo a seguir resulta no suporte de roteamento para: `Component1`

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parâmetros de rota

O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes com o mesmo nome (caso insensível):

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

Os parâmetros opcionais não são suportados. Duas `@page` diretivas são aplicadas no exemplo anterior. O primeiro permite a navegação ao componente sem parâmetro. A `@page` segunda diretiva `{text}` pega o parâmetro de rota `Text` e atribui o valor à propriedade.

## <a name="route-constraints"></a>Restrições de rota

Uma restrição de rota impõe a correspondência de tipo em um segmento de rota a um componente.

No exemplo a seguir, `Users` a rota para o componente só corresponde se:

* Um `Id` segmento de rota está presente na URL de solicitação.
* O `Id` segmento é um`int`inteiro ( ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

As restrições de rota mostradas na tabela a seguir estão disponíveis. Para obter as restrições de rota que correspondem à cultura invariante, consulte o aviso abaixo da tabela para obter mais informações.

| Constraint | Exemplo           | Correspondências de exemplo                                                                  | Invariável<br>culture<br>correspondência |
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

### <a name="routing-with-urls-that-contain-dots"></a>Roteamento com URLs que contêm pontilhados

Nos aplicativos Blazor Server, a rota padrão `/` em`@page "/"` *_Host.cshtml* é ( ). Uma URL de solicitação que`.`contém um dot ( ) não é compatível com a rota padrão porque a URL parece solicitar um arquivo. Um aplicativo Blazor retorna uma resposta *404 - Não Encontrada* para um arquivo estático que não existe. Para usar rotas que contenham um dot, configure *_Host.cshtml* com o seguinte modelo de rota:

```cshtml
@page "/{**path}"
```

O `"/{**path}"` modelo inclui:

* Sintaxe *de captura de* duplo`**`asterisco () para capturar o caminho através`/`de vários limites de pastas sem codificar barras para frente ().
* `path`nome do parâmetro de rota.

> [!NOTE]
> *A* sintaxe de`*`/`**`parâmetros de captura () **não** é suportada em componentes de navalha *(.razor*).

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Componente NavLink

Use `NavLink` um componente no lugar de`<a>`elementos de hiperlink HTML ( ) ao criar links de navegação. Um `NavLink` componente se comporta `<a>` como um elemento, `active` exceto que alterna uma `href` classe CSS com base em se corresponde à URL atual. A `active` classe ajuda o usuário a entender qual página é a página ativa entre os links de navegação exibidos.

O `NavMenu` componente a seguir cria uma barra `NavLink` de navegação [Bootstrap](https://getbootstrap.com/docs/) que demonstra como usar componentes:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Existem `NavLinkMatch` duas opções que você `Match` pode `<NavLink>` atribuir ao atributo do elemento:

* `NavLinkMatch.All`&ndash; O `NavLink` está ativo quando corresponde a toda a URL atual.
* `NavLinkMatch.Prefix`*(padrão)* &ndash; O `NavLink` está ativo quando corresponde a qualquer prefixo da URL atual.

No exemplo anterior, `NavLink` `href=""` o Home corresponde à `active` URL inicial e só recebe a classe CSS `https://localhost:5001/`na URL de caminho base padrão do aplicativo (por exemplo, ). O `NavLink` segundo `active` recebe a classe quando o `MyComponent` usuário visita `https://localhost:5001/MyComponent` qualquer `https://localhost:5001/MyComponent/AnotherSegment`URL com um prefixo (por exemplo, e ).

Atributos adicionais `NavLink` do componente são passados para a tag de âncora renderizada. No exemplo a `NavLink` seguir, o `target` componente inclui o atributo:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

A seguinte marcação HTML é renderizada:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Ajudadores uri e estado de navegação

Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#. `NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.

| Membro | Descrição |
| ------ | ----------- |
| Uri | Obtém o URI absoluto atual. |
| BaseUri | Obtém o URI base (com uma barra de arrasto) que pode ser preparado para caminhos uri relativos para produzir um URI absoluto. Normalmente, `BaseUri` corresponde ao `href` atributo no `<base>` elemento doBlazor documento em *wwwroot/index.html* ( WebAssembly)Blazor ou *Pages/_Host.cshtml* (Server). |
| Navigateto | Navega até o URI especificado. Se `forceLoad` `true`for:<ul><li>O roteamento do lado do cliente é ignorado.</li><li>O navegador é forçado a carregar a nova página do servidor, quer o URI seja normalmente manipulado pelo roteador do lado do cliente.</li></ul> |
| Locationchanged | Um evento que é acionado quando o local de navegação foi alterado. |
| Toabsoluteuri | Converte um URI relativo em um URI absoluto. |
| <span style="word-break:normal;word-wrap:normal">TobaseRelativePath</span> | Dado um URI base (por exemplo, `GetBaseUri`um URI anteriormente devolvido), converte um URI absoluto em um URI em relação ao prefixo URI base. |

O seguinte componente navega até `Counter` o componente do aplicativo quando o botão é selecionado:

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

O componente a seguir lida com um evento alterado de local. O `HandleLocationChanged` método é desenganado quando `Dispose` é chamado pela estrutura. Desengajá-lo permite a coleta de lixo do componente.

```razor
@implement IDisposable
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
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; `true`Se, Blazor interceptou a navegação do navegador. Se `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) causou a navegação.

Para obter mais informações <xref:blazor/lifecycle#component-disposal-with-idisposable>sobre o descarte de componentes, consulte .
