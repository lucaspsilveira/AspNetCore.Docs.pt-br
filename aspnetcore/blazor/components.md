---
title: Criar e usar ASP.NET componentes da Navalha Central
author: guardrex
description: Saiba como criar e usar componentes da Razor, incluindo como se vincular a dados, lidar com eventos e gerenciar ciclos de vida dos componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791482"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Criar e usar ASP.NET componentes da Navalha Central

Por [Luke Latham](https://github.com/guardrex), Daniel [Roth](https://github.com/danroth27)e [Tobias Bartsch](https://www.aveo-solutions.com/)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Blazoraplicativos são *construídos*usando componentes . Um componente é um pedaço independente da interface de usuário (UI), como uma página, diálogo ou formulário. Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos de IA. Os componentes são flexíveis e leves. Eles podem ser aninhados, reutilizados e compartilhados entre os projetos.

## <a name="component-classes"></a>Classes componentes

Os componentes são implementados em arquivos de [componentes Razor](xref:mvc/views/razor) *(.razor)* usando uma combinação de marcação C# e HTML. Um componente Blazor em é formalmente referido como um *componente Razor*.

O nome de um componente deve começar com um caractere maiúsculo. Por exemplo, *MyCoolComponent.razor* é válido e *myCoolComponent.razor* é inválido.

A ida de ida para um componente é definida usando HTML. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor). Quando um aplicativo é compilado, a lógica de marcação HTML e C# são convertidas em uma classe de componentes. O nome da classe gerada corresponde ao nome do arquivo.

Os membros da classe de componente são definidos em um bloco `@code`. No `@code` bloco, o estado do componente (propriedades, campos) é especificado com métodos para o manuseio de eventos ou para definir a lógica de outros componentes. Mais de um bloco de `@code` é permitido.

Os membros do componente podem ser usados como parte da lógica `@`de renderização do componente usando expressões C# que começam com . Por exemplo, um campo C# é `@` renderizado prefixando o nome do campo. O exemplo a seguir avalia e renderiza:

* `_headingFontStyle`ao valor da propriedade `font-style`CSS para .
* `_headingText`ao conteúdo do `<h1>` elemento.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Depois que o componente é renderizado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos. Blazorem seguida, compara a nova árvore de renderização com a anterior e aplica quaisquer modificações no DOM (Document Object Model, modelo de objeto de documento) do navegador.

Os componentes são classes C# comuns e podem ser colocados em qualquer lugar dentro de um projeto. Os componentes que produzem páginas da Web geralmente residem na pasta *Páginas.* Os componentes não-página são frequentemente colocados na pasta *Compartilhada* ou em uma pasta personalizada adicionada ao projeto.

Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e da localização (pasta) do componente dentro do aplicativo. Se o namespace raiz `BlazorApp` do `Counter` aplicativo for e o componente residir na pasta *Páginas:*

* O `Counter` espaço de nome `BlazorApp.Pages`do componente é .
* O nome de tipo totalmente `BlazorApp.Pages.Counter`qualificado do componente é .

Para obter mais informações, consulte a seção [Importar componentes.](#import-components)

Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports.razor* do aplicativo. Por exemplo, o namespace a seguir torna os componentes em uma `BlazorApp`pasta *Componentes* disponíveis quando o namespace raiz do aplicativo é :

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Ativos estáticos

Blazorsegue a convenção de aplicativos ASP.NET Core que colocam ativos estáticos sob a [pasta web root (wwwroot)](xref:fundamentals/index#web-root)do projeto .

Use um caminho relativo à base (`/`) para se referir à raiz da web para um ativo estático. No exemplo a seguir, *o logo.png* está fisicamente localizado na pasta *{PROJECT ROOT}/wwwroot/images:*

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Os componentes da navalha **não** suportam notação de corte de tilde ().`~/`

Para obter informações sobre como definir <xref:host-and-deploy/blazor/index#app-base-path>o caminho base de um aplicativo, consulte .

## <a name="tag-helpers-arent-supported-in-components"></a>Os ajudantes de marcação não são suportados em componentes

[Os ajudantes de](xref:mvc/views/tag-helpers/intro) marcação não são suportados em componentes razor *(arquivos .razor).* Para fornecer a funcionalidade semelhante Blazorao Tag Helper, crie um componente com a mesma funcionalidade do Tag Helper e use o componente em vez disso.

## <a name="use-components"></a>Usar componentes

Os componentes podem incluir outros componentes declarando-os usando sintaxe de elemento HTML. A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.

A marcação a seguir em `HeadingComponent` *Index.razor* torna uma instância:

```razor
<HeadingComponent />
```

*Componentes/HeadingComponent.razor:*

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Se um componente contiver um elemento HTML com uma primeira letra maiúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado. A `@using` adição de uma diretiva para o namespace do componente torna o componente disponível, o que resolve o aviso.

## <a name="routing"></a>Roteamento

O roteamento Blazor é alcançado fornecendo um modelo de rota para cada componente acessível no aplicativo.

Quando um arquivo `@page` Razor com uma diretiva é compilado, a classe gerada recebe uma <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificação do modelo de rota. Em tempo de execução, o roteador procura classes de componentes com a `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.

```razor
@page "/ParentComponent"

...
```

Para obter mais informações, consulte <xref:blazor/routing>.

## <a name="parameters"></a>Parâmetros

### <a name="route-parameters"></a>Parâmetros de rota

Os componentes podem receber parâmetros de `@page` rota a partir do modelo de rota fornecido na diretiva. O roteador usa parâmetros de rota para preencher os parâmetros de componentecorrespondentes correspondentes.

*Páginas/RouteParameter.razor:*

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Os parâmetros opcionais não são `@page` suportados, portanto, duas diretivas são aplicadas no exemplo anterior. O primeiro permite a navegação ao componente sem parâmetro. A `@page` segunda diretiva `{text}` recebe o parâmetro de rota `Text` e atribui o valor à propriedade.

A sintaxe de`*`/`**`parâmetros *catch-all* (), que captura o caminho através de vários limites de pastas, **não** é suportada em componentes razor *(.razor*).

### <a name="component-parameters"></a>Parâmetros do componente

Os componentes podem ter *parâmetros de componentes,* que `[Parameter]` são definidos usando propriedades públicas na classe de componentes com o atributo. Use atributos para especificar argumentos para um componente na marcação.

*Componentes/ChildComponent.razor:*

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

No exemplo a seguir do `ParentComponent` aplicativo de amostra, define o valor da `Title` propriedade do `ChildComponent`.

*Páginas/ParentComponent.razor:*

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Não crie componentes que escrevam para seus *próprios parâmetros de componentes,* use um campo privado em vez disso. Para obter mais informações, consulte [o Não criar componentes que escrevem na seção de propriedades de parâmetros próprios.](#dont-create-components-that-write-to-their-own-parameter-properties)

## <a name="child-content"></a>Conteúdo infantil

Os componentes podem definir o conteúdo de outro componente. O componente atribuído fornece o conteúdo entre as tags que especificam o componente receptor.

No exemplo a `ChildComponent` seguir, `ChildContent` o tem `RenderFragment`uma propriedade que representa um , que representa um segmento de UI para renderizar. O valor `ChildContent` do é posicionado na marcação do componente onde o conteúdo deve ser renderizado. O valor `ChildContent` do é recebido do componente pai e renderizado dentro do `panel-body`painel Bootstrap .

*Componentes/ChildComponent.razor:*

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> A propriedade `RenderFragment` que recebe `ChildContent` o conteúdo deve ser nomeada por convenção.

O `ParentComponent` aplicativo na amostra pode fornecer `ChildComponent` conteúdo para renderização, colocando o conteúdo dentro das `<ChildComponent>` tags.

*Páginas/ParentComponent.razor:*

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Splatting de atributos e parâmetros arbitrários

Os componentes podem capturar e renderizar atributos adicionais, além dos parâmetros declarados do componente. Atributos adicionais podem ser capturados em um dicionário e, em seguida, [`@attributes`](xref:mvc/views/razor#attributes) *splatted* em um elemento quando o componente é renderizado usando a diretiva Razor. Este cenário é útil ao definir um componente que produz um elemento de marcação que suporta uma variedade de personalizações. Por exemplo, pode ser tedioso definir atributos separadamente para um `<input>` que suporta muitos parâmetros.

No exemplo a seguir, `<input>` `id="useIndividualParams"`o primeiro elemento ( ) `<input>` usa`id="useAttributesDict"`parâmetros de componentes individuais, enquanto o segundo elemento ( ) usa splatting de atributo:

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

O tipo de parâmetro `IEnumerable<KeyValuePair<string, object>>` deve ser implementado com teclas de string. Usar `IReadOnlyDictionary<string, object>` também é uma opção neste cenário.

Os elementos renderizados `<input>` que utilizam ambas as abordagens são idênticos:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Para aceitar atributos arbitrários, `[Parameter]` defina `CaptureUnmatchedValues` um `true`parâmetro de componente usando o atributo com a propriedade definida como:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

A `CaptureUnmatchedValues` propriedade `[Parameter]` em permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro. Um componente só pode definir `CaptureUnmatchedValues`um parâmetro único com . O tipo de `CaptureUnmatchedValues` propriedade usado deve `Dictionary<string, object>` ser atribuído a partir de teclas de string. `IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.

A posição `@attributes` em relação à posição dos atributos do elemento é importante. Quando `@attributes` são splatted no elemento, os atributos são processados da direita para a esquerda (de última para primeira). Considere o seguinte exemplo de um `Child` componente que consome um componente:

*ParentComponent.razor:*

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor:*

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

O `Child` atributo `extra` do componente está `@attributes`definido à direita de . O `Parent` componente renderizado `<div>` `extra="5"` contém quando passado através do atributo adicional porque os atributos são processados da direita para a esquerda (da última à primeira):

```html
<div extra="5" />
```

No exemplo a seguir, `extra` `@attributes` a ordem e `Child` é `<div>`invertida no componente:

*ParentComponent.razor:*

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor:*

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

A renderização `<div>` `Parent` no `extra="10"` componente contém quando passada através do atributo adicional:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Capturar referências a componentes

As referências de componentes fornecem uma maneira de referenciar uma instância `Show` de `Reset`componente para que você possa emitir comandos para essa instância, como ou . Para capturar uma referência de componente:

* Adicione [`@ref`](xref:mvc/views/razor#ref) um atributo ao componente filho.
* Defina um campo com o mesmo tipo do componente filho.

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

Quando o componente é `_loginDialog` renderizado, o `MyLoginDialog` campo é preenchido com a instância do componente filho. Em seguida, você pode invocar métodos .NET na instância do componente.

> [!IMPORTANT]
> A `_loginDialog` variável só é preenchida após a renderização do `MyLoginDialog` componente e sua saída inclui o elemento. Até lá, não há nada para se referir. Para manipular as referências dos componentes após a renderização concluída do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).

Para referenciar componentes em um loop, consulte [Capturar referências a vários componentes infantis semelhantes (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Embora a captura de referências de componentes use uma sintaxe semelhante à [captura de referências de elementos,](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)não é um recurso de interop JavaScript. As referências dos componentes não&mdash;são passadas para o código JavaScript, elas são usadas apenas no código .NET.

> [!NOTE]
> **Não utilize** referências de componentes para alterar o estado dos componentes infantis. Em vez disso, use parâmetros declarativos normais para passar dados para componentes infantis. O uso de parâmetros declarativos normais resulta em componentes infantis que rerenderizam automaticamente nos horários corretos.

## <a name="invoke-component-methods-externally-to-update-state"></a>Invoque métodos de componentes externamente para atualizar o estado

Blazorusa `SynchronizationContext` um para impor um único segmento lógico de execução. Os [métodos](xref:blazor/lifecycle) de ciclo de vida de um componente Blazor e quaisquer retornos de chamada de evento que são levantados por são executados neste `SynchronizationContext`. No caso de um componente ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o `InvokeAsync` método, que será enviado para Blazoro 's `SynchronizationContext`.

Por exemplo, considere um *serviço de notificantes* que possa notificar qualquer componente de escuta do estado atualizado:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Registre `NotifierService` o como singletion:

* No Blazor WebAssembly, registre `Program.Main`o serviço em :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* No Blazor Servidor, registre `Startup.ConfigureServices`o serviço em :

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Use `NotifierService` o para atualizar um componente:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

No exemplo `NotifierService` anterior, invoca o `OnNotify` método do Blazorcomponente `SynchronizationContext`fora de 's . `InvokeAsync`é usado para mudar para o contexto correto e enfileirar uma renderização.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Use \@a chave para controlar a preservação de elementos e componentes

Ao renderizar uma lista de elementos ou componentes Blazore os elementos ou componentes posteriormente mudarem, o algoritmo de difusão deve decidir quais dos elementos ou componentes anteriores podem ser retidos e como os objetos do modelo devem mapeá-los. Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você pode querer controlar o processo.

Considere o exemplo a seguir:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

O conteúdo `People` da coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas. Quando o componente se `<DetailsEditor>` rerenderiza, o `Details` componente pode mudar para receber diferentes valores de parâmetros. Isso pode causar uma rerenderização mais complexa do que o esperado. Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco perdido do elemento.

O processo de mapeamento [`@key`](xref:mvc/views/razor#key) pode ser controlado com o atributo diretivo. `@key`faz com que o algoritmo de difusão garanta a preservação de elementos ou componentes com base no valor da chave:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Quando `People` a coleção muda, o algoritmo de `<DetailsEditor>` difusão retém a associação entre instâncias e `person` instâncias:

* Se `Person` um for excluído `People` da lista, `<DetailsEditor>` apenas a instância correspondente será removida da ui. Outras instâncias são deixadas inalteradas.
* Se `Person` um for inserido em alguma posição `<DetailsEditor>` na lista, uma nova instância será inserida nessa posição correspondente. Outras instâncias são deixadas inalteradas.
* Se `Person` as entradas forem reordenadas, as instâncias correspondentes `<DetailsEditor>` serão preservadas e reordenadas na ui.

Em alguns cenários, o uso de `@key` minimiza a complexidade da rerenderização e evita possíveis problemas com partes estaduais do DOM mudando, como a posição de foco.

> [!IMPORTANT]
> As teclas são locais para cada elemento ou componente do contêiner. As chaves não são comparadas globalmente através do documento.

### <a name="when-to-use-key"></a>Quando usar \@a chave

Normalmente, faz sentido `@key` usar sempre que uma lista é `@foreach` renderizada (por exemplo, em `@key`um bloco) e existe um valor adequado para definir o .

Você também `@key` pode Blazor usar para evitar a preservação de um elemento ou subárvore componente quando um objeto muda:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Se `@currentPerson` mudar, `@key` a Blazor diretiva de `<div>` atributo força a descartar o todo e seus descendentes e reconstruir a subárvore dentro da UI com novos elementos e componentes. Isso pode ser útil se você precisar garantir que `@currentPerson` nenhum estado de IU seja preservado quando mudar.

### <a name="when-not-to-use-key"></a>Quando não \@usar a chave

Há um custo de desempenho `@key`quando se diffing com . O custo de desempenho não é `@key` grande, mas apenas especificar se o controle do elemento ou regras de preservação de componentes beneficiam o aplicativo.

Mesmo `@key` que não seja Blazor usado, preserva o elemento infantil e as instâncias dos componentes tanto quanto possível. A única vantagem `@key` a ser usado é o controle sobre *como* as instâncias do modelo são mapeadas para as instâncias de componentes preservados, em vez do algoritmo de difusão que seleciona o mapeamento.

### <a name="what-values-to-use-for-key"></a>Quais valores \@usar como chave

Geralmente, faz sentido fornecer um dos seguintes `@key`tipos de valor para:

* Modelo de instâncias de `Person` objeto (por exemplo, uma instância como no exemplo anterior). Isso garante a preservação com base na igualdade de referência de objetos.
* Identificadores únicos (por exemplo, valores-chave primários do tipo, `int` `string`ou `Guid`).

Certifique-se de `@key` que os valores usados não se chocam. Se os valores conflitantes forem Blazor detectados dentro do mesmo elemento pai, lança uma exceção porque não pode mapear deterministicamente elementos antigos ou componentes para novos elementos ou componentes. Use apenas valores distintos, como instâncias de objeto ou valores-chave primários.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Não crie componentes que escrevam para suas próprias propriedades de parâmetro

Os parâmetros são substituídos sob as seguintes condições:

* O conteúdo de um componente filho `RenderFragment`é renderizado com um .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado no componente pai.

Os parâmetros são redefinidos porque <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> o componente pai rerenderiza quando é chamado e novos valores de parâmetro são fornecidos ao componente filho.

Considere o `Expander` seguinte componente que:

* Torna o conteúdo infantil.
* Alternações mostrando o conteúdo da criança com um parâmetro componente.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

O `Expander` componente é adicionado a um `StateHasChanged`componente pai que pode chamar:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Inicialmente, `Expander` os componentes se `Expanded` comportam independentemente quando suas propriedades são alternadas. Os componentes infantis mantêm seus estados como esperado. Quando `StateHasChanged` é chamado no `Expanded` pai, o parâmetro do primeiro componente filho é`true`redefinido de volta ao seu valor inicial ( ). O `Expander` valor do `Expanded` segundo componente não é redefinido porque nenhum conteúdo infantil é renderizado no segundo componente.

Para manter o estado no cenário anterior, `Expander` utilize um campo *privado* no componente para manter seu estado alternado.

O `Expander` seguinte componente:

* Aceita o `Expanded` valor do parâmetro do componente do pai.
* Atribui o valor do parâmetro do`_expanded`componente a um campo *privado* ( ) no evento [OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Usa o campo privado para manter seu estado interno de alterne.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a>Suporte parcial da classe

Os componentes da navalha são gerados como classes parciais. Os componentes da navalha são de autoria usando qualquer uma das seguintes abordagens:

* C# code é [`@code`](xref:mvc/views/razor#code) definido em um bloco com marcação HTML e código Razor em um único arquivo. Blazoros modelos definem seus componentes razor usando esta abordagem.
* O código C# é colocado em um arquivo de código atrás definido como uma classe parcial.

O exemplo a `Counter` seguir mostra `@code` o componente padrão Blazor com um bloco em um aplicativo gerado a partir de um modelo. Marcação HTML, código de navalha e código C# estão no mesmo arquivo:

*Contador.navalha:*

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

O `Counter` componente também pode ser criado usando um arquivo de código atrás com uma classe parcial:

*Contador.navalha:*

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

Adicione todos os namespaces necessários ao arquivo de classe parcial, conforme necessário. Os espaços de nome típicos usados pelos componentes da Navalha incluem:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Especifique uma classe base

A [`@inherits`](xref:mvc/views/razor#inherits) diretiva pode ser usada para especificar uma classe base para um componente. O exemplo a seguir mostra como um `BlazorRocksBase`componente pode herdar uma classe base, para fornecer as propriedades e métodos do componente. A classe base `ComponentBase`deve derivar de .

*Páginas/BlazorRocks.razor:*

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>Especifique um atributo

Os atributos podem ser especificados [`@attribute`](xref:mvc/views/razor#attribute) em componentes de Navalha com a diretiva. O exemplo a `[Authorize]` seguir aplica o atributo à classe de componentes:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Componentes de importação

O namespace de um componente de autoria com Razor é baseado em (em ordem de prioridade):

* [`@namespace`](xref:mvc/views/razor#namespace)designação em Razor file *(.razor*) markup ().`@namespace BlazorSample.MyNamespace`
* O projeto `RootNamespace` está no arquivo`<RootNamespace>BlazorSample</RootNamespace>`do projeto ( . .
* O nome do projeto, retirado do nome do arquivo do arquivo do projeto (*.csproj),* e o caminho da raiz do projeto para o componente. Por exemplo, o framework resolve *{PROJECT ROOT}/Pages/Index.razor* *(BlazorSample.csproj)* para o namespace `BlazorSample.Pages`. Os componentes seguem as regras de vinculação de nomes C#. Para `Index` o componente neste exemplo, os componentes no escopo são todos os componentes:
  * Na mesma pasta, *Páginas*.
  * Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.

Os componentes definidos em um namespace diferente [`@using`](xref:mvc/views/razor#using) são colocados em escopo usando a diretiva de Razor.

Se houver `NavMenu.razor`outro componente na pasta *BlazorSample/Shared/,* o `Index.razor` componente poderá `@using` ser usado com a seguinte declaração:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Os componentes também podem ser referenciados usando seus nomes [`@using`](xref:mvc/views/razor#using) totalmente qualificados, o que não requer a diretiva:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> A `global::` qualificação não é suportada.
>
> A importação de componentes `using` com instruções `@using Foo = Bar`alias (por exemplo) não é suportada.
>
> Nomes parcialmente qualificados não são suportados. Por exemplo, `@using BlazorSample` adicionar `NavMenu.razor` e `<Shared.NavMenu></Shared.NavMenu>` referenciar não é suportado.

## <a name="conditional-html-element-attributes"></a>Atributos de elemento HTML condicional

Os atributos do elemento HTML são validamente renderizados com base no valor .NET. Se o `false` valor `null`for ou , o atributo não é renderizado. Se o `true`valor for, o atributo será minimizado.

No exemplo a `IsCompleted` seguir, `checked` determina se é renderizado na marcação do elemento:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Se `IsCompleted` `true`for, a caixa de seleção é renderizada como:

```html
<input type="checkbox" checked />
```

Se `IsCompleted` `false`for, a caixa de seleção é renderizada como:

```html
<input type="checkbox" />
```

Para obter mais informações, consulte <xref:mvc/views/razor>.

> [!WARNING]
> Alguns atributos HTML, como [pressionado por árias,](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)não funcionam `bool`corretamente quando o tipo .NET é um . Nesses casos, use `string` um tipo `bool`em vez de um .

## <a name="raw-html"></a>HTML bruto

As strings são normalmente renderizadas usando nós de texto DOM, o que significa que qualquer marcação que possam conter é ignorada e tratada como texto literal. Para renderizar HTML bruto, enrole o conteúdo HTML em um `MarkupString` valor. O valor é analisado como HTML ou SVG e inserido no DOM.

> [!WARNING]
> Renderizar HTML bruto construído a partir de qualquer fonte não confiável é um risco de **segurança** e deve ser evitado!

O exemplo a `MarkupString` seguir mostra usar o tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Valores e parâmetros em cascata

Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componentes,](#component-parameters)especialmente quando há várias camadas de componentes. Os valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor a todos os seus componentes descendentes. Os valores e parâmetros em cascata também fornecem uma abordagem para os componentes coordenarem.

### <a name="theme-example"></a>Exemplo temático

No exemplo a seguir do `ThemeInfo` aplicativo de exemplo, a classe especifica as informações do tema para fluir pela hierarquia do componente para que todos os botões dentro de uma determinada parte do aplicativo compartilhem o mesmo estilo.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Um componente ancestral pode fornecer um valor em cascata usando o componente Valor em Cascata. O `CascadingValue` componente envolve uma subárvore da hierarquia de componentes e fornece um único valor para todos os componentes dentro dessa subárvore.

Por exemplo, o aplicativo de amostra`ThemeInfo`especifica as informações temáticas ( ) em um dos layouts do aplicativo como `@Body` um parâmetro em cascata para todos os componentes que compõem o corpo de layout da propriedade. `ButtonClass`é atribuído um `btn-success` valor do componente de layout. Qualquer componente descendente pode consumir `ThemeInfo` esta propriedade através do objeto em cascata.

`CascadingValuesParametersLayout`Componente:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Para fazer uso de valores em cascata, os `[CascadingParameter]` componentes declaram parâmetros em cascata usando o atributo. Os valores em cascata estão vinculados a parâmetros em cascata por tipo.

No aplicativo de `CascadingValuesParametersTheme` amostra, o `ThemeInfo` componente liga o valor em cascata a um parâmetro em cascata. O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.

`CascadingValuesParametersTheme`Componente:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

Para cascata de múltiplos valores do mesmo tipo `Name` dentro da `CascadingValue` mesma subárvore, forneça uma seqüência única para cada componente e seu correspondente `CascadingParameter`. No exemplo a `CascadingValue` seguir, dois componentes `MyCascadingType` cascatam diferentes instâncias de nome:

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Exemplo de Set de guia

Os parâmetros em cascata também permitem que os componentes colaborem em toda a hierarquia de componentes. Por exemplo, considere o seguinte exemplo *de TabSet* no aplicativo de exemplo.

O aplicativo de `ITab` exemplo tem uma interface que implementa as guias:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

O `CascadingValuesParametersTabSet` componente `TabSet` usa o componente, que contém vários `Tab` componentes:

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

Os `Tab` componentes da criança não são explicitamente `TabSet`passados como parâmetros para o . Em vez `Tab` disso, os componentes da `TabSet`criança fazem parte do conteúdo infantil do . No entanto, o `TabSet` ainda `Tab` precisa saber sobre cada componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação `TabSet` sem exigir código adicional, o componente *pode fornecer-se como um valor em cascata* que é então captado pelos componentes descendentes. `Tab`

`TabSet`Componente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

`Tab` Os componentes descendentes capturam o contendo `TabSet` como um parâmetro `Tab` em cascata, `TabSet` de modo que os componentes se adicionam à e coordenam em qual guia está ativa.

`Tab`Componente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Modelos de navalha

Os fragmentos de renderização podem ser definidos usando a sintaxe do modelo Razor. Os modelos de navalha são uma maneira de definir um trecho de ui e assumir o seguinte formato:

```razor
@<{HTML tag}>...</{HTML tag}>
```

O exemplo a seguir `RenderFragment` ilustra `RenderFragment<T>` como especificar e valores e renderizar modelos diretamente em um componente. Fragmentos de renderização também podem ser passados como argumentos para [componentes modelados](xref:blazor/templated-components).

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Saída renderizada do código anterior:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Imagens scalable vector graphics (SVG)

Uma Blazor vez que renderizações HTML, imagens suportadas pelo navegador, incluindo imagens SCalable Vector `<img>` Graphics (SVG),*.svg*são suportadas através da tag:

```html
<img alt="Example image" src="some-image.svg" />
```

Da mesma forma, as imagens SVG são suportadas nas regras CSS de um arquivo de folha de estilos *(.css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

No entanto, a marcação SVG inline não é suportada em todos os cenários. Se você `<svg>` colocar uma tag diretamente em um arquivo de componente *(.razor),* a renderização básica de imagem será suportada, mas muitos cenários avançados ainda não são suportados. Por exemplo, `<use>` as tags não são `@bind` respeitadas no momento e não podem ser usadas com algumas tags SVG. Esperamos abordar essas limitações em um lançamento futuro.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/blazor/server>&ndash; Inclui orientação Blazor sobre a construção de aplicativos do Servidor que devem enfrentar a exaustão de recursos.
