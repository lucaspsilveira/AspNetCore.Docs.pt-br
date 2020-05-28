---
Título: ' criar e usar ASP.NET Core Razor componentes ' autor: Descrição: ' saiba como criar e usar Razor componentes, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="create-and-use-aspnet-core-razor-components"></a>Criar e usar componentes de ASP.NET Core Razor

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Tobias Bartsch](https://www.aveo-solutions.com/)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Blazoros aplicativos são criados usando *componentes*. Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário. Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário. Os componentes são flexíveis e leves. Eles podem ser aninhados, reutilizados e compartilhados entre projetos.

## <a name="component-classes"></a>Classes de componente

Os componentes são implementados em [Razor](xref:mvc/views/razor) arquivos de componente (*. Razor*) usando uma combinação de marcação em C# e HTML. Um componente no Blazor é conhecido formalmente como um * Razor componente*.

O nome de um componente deve começar com um caractere maiúsculo. Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.

A interface do usuário para um componente é definida usando HTML. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe C# inserida chamada *Razor* . Quando um aplicativo é compilado, a marcação HTML e a lógica de renderização C# são convertidas em uma classe de componente. O nome da classe gerada corresponde ao nome do arquivo.

Os membros da classe de componente são definidos em um [`@code`][1] bloco. No [`@code`][1] bloco, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente. Mais de um [`@code`][1] bloco é permitido.

Os membros do componente podem ser usados como parte da lógica de renderização do componente usando expressões C# que começam com `@` . Por exemplo, um campo C# é renderizado pela prefixação `@` para o nome do campo. O exemplo a seguir avalia e renderiza:

* `headingFontStyle`para o valor da propriedade de CSS para `font-style` .
* `headingText`para o conteúdo do `<h1>` elemento.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos. Blazorem seguida, compara a nova árvore de renderização com a anterior e aplica quaisquer modificações ao Modelo de Objeto do Documento do navegador (DOM).

Os componentes são classes C# comuns e podem ser colocados em qualquer lugar dentro de um projeto. Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* . Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.

Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo. Se o namespace raiz do aplicativo for `BlazorApp` e o `Counter` componente residir na pasta *páginas* :

* O `Counter` namespace do componente é `BlazorApp.Pages` .
* O nome do tipo totalmente qualificado do componente é `BlazorApp.Pages.Counter` .

Para pastas personalizadas que contêm componentes, adicione uma `using` instrução ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo. O exemplo a seguir torna os componentes na pasta *componentes* disponíveis:

```razor
@using BlazorApp.Components
```

Como alternativa, um componente pode ser referenciado diretamente:

```razor
<BlazorApp.Components.MyCoolComponent />
```

Para obter mais informações, consulte a seção [importar componentes](#import-components) .

## <a name="razor-syntax"></a>Sintaxe de Razor

Razoros componentes em Blazor aplicativos usam extensivamente a Razor sintaxe. Se você não estiver familiarizado com a Razor linguagem de marcação, recomendamos a leitura <xref:mvc/views/razor> antes de continuar.

Ao acessar o conteúdo em Razor sintaxe, preste atenção especial às seguintes seções:

* [Diretivas](xref:mvc/views/razor#directives): `@` -palavras-chave reservadas prefixadas que normalmente alteram a maneira como a marcação de componente é analisada ou funciona.
* [Atributos de diretiva](xref:mvc/views/razor#directive-attributes): `@` -palavras-chave reservadas prefixadas que normalmente alteram a maneira como os elementos de componente são analisados ou funcionam.

## <a name="static-assets"></a>Ativos estáticos

Blazorsegue a Convenção de ASP.NET Core aplicativos que posicionam ativos estáticos na [pasta raiz da Web do projeto (wwwroot)](xref:fundamentals/index#web-root).

Use um caminho relativo de base ( `/` ) para se referir à raiz da Web para um ativo estático. No exemplo a seguir, *logo. png* está localizado fisicamente na pasta *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razoros componentes **não** dão suporte a notação de barra de til ( `~/` ).

Para obter informações sobre como definir o caminho base de um aplicativo, consulte <xref:host-and-deploy/blazor/index#app-base-path> .

## <a name="tag-helpers-arent-supported-in-components"></a>Não há suporte para auxiliares de marcas nos componentes

Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) não têm suporte em Razor componentes (arquivos *. Razor* ). Para fornecer a funcionalidade do tipo auxiliar de marca no Blazor , crie um componente com a mesma funcionalidade que o auxiliar de marca e use o componente em vez disso.

## <a name="use-components"></a>Usar componentes

Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML. A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.

A marcação a seguir no *index. Razor* renderiza uma `HeadingComponent` instância:

```razor
<HeadingComponent />
```

*Componentes/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado. A adição de uma [`@using`][2] diretiva para o namespace do componente torna o componente disponível, o que resolve o aviso.

## <a name="routing"></a>Roteamento

O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.

Quando um Razor arquivo com uma [`@page`][9] diretiva é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota. Em tempo de execução, o roteador procura classes de componentes com um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.

```razor
@page "/ParentComponent"

...
```

Para obter mais informações, consulte <xref:blazor/routing>.

## <a name="parameters"></a>Parâmetros

### <a name="route-parameters"></a>Parâmetros de rota

Os componentes podem receber parâmetros de rota do modelo de rota fornecido na [`@page`][9] diretiva. O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.

*Páginas/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Não há suporte para parâmetros opcionais, portanto, duas [`@page`][9] diretivas são aplicadas no exemplo anterior. O primeiro permite a navegação para o componente sem um parâmetro. A segunda [`@page`][9] diretiva recebe o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.

A sintaxe de parâmetro *catch-all* ( `*` / `**` ), que captura o caminho entre vários limites de pasta, **não** tem suporte em Razor componentes (*. Razor*).

### <a name="component-parameters"></a>Parâmetros do componente

Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo. Use atributos para especificar argumentos para um componente na marcação.

*Componentes/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da `Title` Propriedade do `ChildComponent` .

*Páginas/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Não crie componentes que gravam em seus próprios *parâmetros de componente*, use um campo particular em vez disso. Para obter mais informações, consulte a seção [não criar componentes que gravam em suas próprias propriedades de parâmetro](#dont-create-components-that-write-to-their-own-parameter-properties) .

## <a name="child-content"></a>Conteúdo filho

Os componentes podem definir o conteúdo de outro componente. O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.

No exemplo a seguir, o `ChildComponent` tem uma `ChildContent` propriedade que representa um <xref:Microsoft.AspNetCore.Components.RenderFragment> , que representa um segmento de interface do usuário a ser renderizado. O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado. O valor de `ChildContent` é recebido do componente pai e renderizado dentro do painel de inicialização `panel-body` .

*Componentes/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> A propriedade que recebe o <xref:Microsoft.AspNetCore.Components.RenderFragment> conteúdo deve ser nomeada `ChildContent` por convenção.

O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das `<ChildComponent>` marcas.

*Páginas/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Atributo nivelamento e parâmetros arbitrários

Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente. Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a [`@attributes`][3] Razor diretiva. Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações. Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que dê suporte a muitos parâmetros.

No exemplo a seguir, o primeiro `<input>` elemento ( `id="useIndividualParams"` ) usa parâmetros de componente individuais, enquanto o segundo `<input>` elemento ( `id="useAttributesDict"` ) usa o atributo nivelamento:

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

O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres. Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.

Os elementos renderizados `<input>` usando ambas as abordagens são idênticos:

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

Para aceitar atributos arbitrários, defina um parâmetro de componente usando o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo com a <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriedade definida como `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

A <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> Propriedade on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro. Um componente só pode definir um único parâmetro com <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> . O tipo de propriedade usado com <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres. `IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.

A posição de [`@attributes`][3] relativo à posição dos atributos do elemento é importante. Quando [`@attributes`][3] são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro). Considere o exemplo a seguir de um componente que consome um `Child` componente:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

O `Child` atributo do componente `extra` é definido à direita de [`@attributes`][3] . O `Parent` renderizado do componente `<div>` contém `extra="5"` quando passado pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):

```html
<div extra="5" />
```

No exemplo a seguir, a ordem de `extra` e [`@attributes`][3] é invertida no `Child` `<div>` :

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

O renderizado `<div>` no `Parent` componente contém `extra="10"` quando passado pelo atributo adicional:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Capturar referências a componentes

As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset` . Para capturar uma referência de componente:

* Adicione um [`@ref`][4] atributo ao componente filho.
* Defina um campo com o mesmo tipo do componente filho.

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Quando o componente é renderizado, o `loginDialog` campo é populado com a `MyLoginDialog` instância de componente filho. Em seguida, você pode invocar os métodos .NET na instância do componente.

> [!IMPORTANT]
> A `loginDialog` variável é populada apenas depois que o componente é renderizado e sua saída inclui o `MyLoginDialog` elemento. Até esse ponto, não há nada a fazer referência. Para manipular referências de componentes após a conclusão da renderização do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).

Para fazer referência a componentes em um loop, consulte [capturar referências para vários componentes filho semelhantes (dotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), ela não é um recurso de interoperabilidade do JavaScript. As referências de componente não são passadas para o código JavaScript. As referências de componente são usadas apenas no código .NET.

> [!NOTE]
> Não **use referências** de componente para converter o estado dos componentes filho. Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho. O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.

## <a name="invoke-component-methods-externally-to-update-state"></a>Invocar métodos de componente externamente para atualizar o estado

Blazorusa um contexto de sincronização ( <xref:System.Threading.SynchronizationContext> ) para impor um único thread lógico de execução. Os métodos de [ciclo de vida](xref:blazor/lifecycle) de um componente e quaisquer retornos de chamada de evento que são gerados pelo Blazor são executados no contexto de sincronização.

BlazorO contexto de sincronização do servidor tenta emular um ambiente de thread único para que ele corresponda ao modelo Webassembly no navegador, que é um thread único. Em qualquer momento determinado, o trabalho é executado em exatamente um thread, dando a impressão de um único thread lógico. Duas operações não são executadas simultaneamente.

No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o `InvokeAsync` método, que é expedido para o Blazor contexto de sincronização. Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:

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

Registre o `NotifierService` como um singletion:

* No Blazor Webassembly, registre o serviço em `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Em Blazor servidor, registre o serviço em `Startup.ConfigureServices` :

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Use o `NotifierService` para atualizar um componente:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

No exemplo anterior, `NotifierService` invoca o método do componente `OnNotify` fora do contexto de Blazor sincronização do. `InvokeAsync`é usado para alternar para o contexto correto e enfileirar uma renderização.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Use \@ a chave para controlar a preservação de elementos e componentes

Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o Blazor algoritmo de diferenciação do deve decidir quais elementos ou componentes anteriores podem ser retidos e como os objetos de modelo devem ser mapeados para eles. Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.

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

O conteúdo da `People` coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas. Quando o componente é rerenderizado, o `<DetailsEditor>` componente pode ser alterado para receber `Details` valores de parâmetro diferentes. Isso pode causar um reprocessamento mais complexo do que o esperado. Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.

O processo de mapeamento pode ser controlado com o [`@key`][5] atributo de diretiva. [`@key`][5]faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:

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

Quando a `People` coleção é alterada, o algoritmo diff mantém a associação entre `<DetailsEditor>` instâncias e `person` instâncias:

* Se um `Person` for excluído da `People` lista, somente a instância correspondente `<DetailsEditor>` será REmovida da interface do usuário. Outras instâncias permanecem inalteradas.
* Se um `Person` for inserido em alguma posição na lista, uma nova `<DetailsEditor>` instância será inserida na posição correspondente. Outras instâncias permanecem inalteradas.
* Se `Person` as entradas forem reordenadas, as `<DetailsEditor>` instâncias correspondentes serão preservadas e reordenadas na interface do usuário.

Em alguns cenários, o uso de [`@key`][5] minimiza a complexidade da rerenderização e evita possíveis problemas com partes com estado da alteração do dom, como a posição do foco.

> [!IMPORTANT]
> As chaves são locais para cada elemento ou componente de contêiner. As chaves não são comparadas globalmente ao longo do documento.

### <a name="when-to-use-key"></a>Quando usar a \@ chave

Normalmente, faz sentido usar [`@key`][5] sempre que uma lista é renderizada (por exemplo, em um bloco [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) e um valor adequado existe para definir o [`@key`][5] .

Você também pode usar [`@key`][5] para evitar a Blazor preservação de uma subárvore de elemento ou componente quando um objeto for alterado:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Se `@currentPerson` forem alteradas, a [`@key`][5] diretiva de atributo forçará Blazor a descartar todo `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes. Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando houver `@currentPerson` alterações.

### <a name="when-not-to-use-key"></a>Quando não usar a \@ chave

Há um custo de desempenho ao comparar com [`@key`][5] . O custo de desempenho não é grande, mas só especifica [`@key`][5] se controlar as regras de preservação de elementos ou componentes beneficiam o aplicativo.

Mesmo que [`@key`][5] não seja usado, Blazor o preserva as instâncias de elemento filho e de componente o máximo possível. A única vantagem de usar o [`@key`][5] é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.

### <a name="what-values-to-use-for-key"></a>Quais valores usar para a \@ chave

Geralmente, faz sentido fornecer um dos seguintes tipos de valor para [`@key`][5] :

* Instâncias de objeto de modelo (por exemplo, uma `Person` instância como no exemplo anterior). Isso garante a preservação com base na igualdade de referência de objeto.
* Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int` , `string` ou `Guid` ).

Verifique se os valores usados para [`@key`][5] não conflitam. Se valores conflitantes forem detectados no mesmo elemento pai, Blazor o lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes. Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Não crie componentes que gravam em suas próprias propriedades de parâmetro

Os parâmetros são substituídos nas seguintes condições:

* O conteúdo de um componente filho é renderizado com um <xref:Microsoft.AspNetCore.Components.RenderFragment> .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado no componente pai.

Os parâmetros são redefinidos porque o componente pai é rerenderizado quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado e novos valores de parâmetro são fornecidos ao componente filho.

Considere o seguinte `Expander` componente que:

* Renderiza conteúdo filho.
* Alterna a exibição de conteúdo filho com um parâmetro de componente.

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

O `Expander` componente é adicionado a um componente pai que pode chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Inicialmente, os `Expander` componentes se comportam de forma independente quando suas `Expanded` Propriedades são alternadas. Os componentes filho mantêm seus Estados como esperado. Quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado no pai, o `Expanded` parâmetro do primeiro componente filho é redefinido de volta para seu valor inicial ( `true` ). O `Expander` valor do segundo componente `Expanded` não é redefinido porque nenhum conteúdo filho é renderizado no segundo componente.

Para manter o estado no cenário anterior, use um *campo particular* no `Expander` componente para manter seu estado de alternância.

O seguinte `Expander` componente:

* Aceita o `Expanded` valor do parâmetro de componente do pai.
* Atribui o valor do parâmetro de componente a um *campo privado* ( `expanded` ) no [evento OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Usa o campo particular para manter seu estado de alternância interno.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a>Suporte de classe parcial

Razoros componentes são gerados como classes parciais. Razoros componentes são criados usando uma das seguintes abordagens:

* O código C# é definido em um [`@code`][1] bloco com marcação e Razor código HTML em um único arquivo. Blazoros modelos definem seus Razor componentes usando essa abordagem.
* O código C# é colocado em um arquivo code-behind definido como uma classe parcial.

O exemplo a seguir mostra o `Counter` componente padrão com um [`@code`][1] bloco em um aplicativo gerado a partir de um Blazor modelo. A marcação HTML, o Razor código e o código C# estão no mesmo arquivo:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

O `Counter` componente também pode ser criado usando um arquivo code-behind com uma classe parcial:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário. Os namespaces típicos usados pelos Razor componentes incluem:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Especificar uma classe base

A [`@inherits`][6] diretiva pode ser usada para especificar uma classe base para um componente. O exemplo a seguir mostra como um componente pode herdar uma classe base, `BlazorRocksBase` ,, para fornecer as propriedades e os métodos do componente. A classe base deve derivar de <xref:Microsoft.AspNetCore.Components.ComponentBase> .

*Páginas/BlazorRocks. Razor*:

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

## <a name="specify-an-attribute"></a>Especificar um atributo

Os atributos podem ser especificados em Razor componentes com a [`@attribute`][7] diretiva. O exemplo a seguir aplica o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo à classe de componente:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importar componentes

O namespace de um componente criado com o Razor é baseado em (em ordem de prioridade):

* [`@namespace`][8]designação na Razor marcação de arquivo (*. Razor*) ( `@namespace BlazorSample.MyNamespace` ).
* O projeto `RootNamespace` no arquivo de projeto ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* O nome do projeto, obtido do nome do arquivo do projeto (*. csproj*) e o caminho da raiz do projeto para o componente. Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages` . Os componentes seguem regras de associação de nome C#. Para o `Index` componente neste exemplo, os componentes no escopo são todos os componentes:
  * Na mesma pasta, *páginas*.
  * Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.

Os componentes definidos em um namespace diferente são trazidos para o escopo usando a Razor [`@using`][2] diretiva do.

Se outro componente, `NavMenu.razor` , existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado no `Index.razor` com a seguinte [`@using`][2] instrução:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não exige a [`@using`][2] diretiva:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::`Não há suporte para a qualificação.
>
> Não há suporte para a importação de componentes com instruções [using com](/dotnet/csharp/language-reference/keywords/using-statement) alias (por exemplo, `@using Foo = Bar` ).
>
> Não há suporte para nomes parcialmente qualificados. Por exemplo, `@using BlazorSample` não há suporte para adicionar e referenciar o `NavMenu` componente ( `NavMenu.razor` ) com `<Shared.NavMenu></Shared.NavMenu>` .

## <a name="conditional-html-element-attributes"></a>Atributos de elemento HTML condicional

Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET. Se o valor for `false` ou `null` , o atributo não será renderizado. Se o valor é `true` , o atributo é processado minimizado.

No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Se `IsCompleted` for `true` , a caixa de seleção será renderizada como:

```html
<input type="checkbox" checked />
```

Se `IsCompleted` for `false` , a caixa de seleção será renderizada como:

```html
<input type="checkbox" />
```

Para obter mais informações, consulte <xref:mvc/views/razor>.

> [!WARNING]
> Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool` . Nesses casos, use um `string` tipo em vez de um `bool` .

## <a name="raw-html"></a>HTML bruto

Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal. Para renderizar HTML bruto, empacote o conteúdo HTML em um `MarkupString` valor. O valor é analisado como HTML ou SVG e inserido no DOM.

> [!WARNING]
> O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!

O exemplo a seguir mostra como usar o `MarkupString` tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Valores e parâmetros em cascata

Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente. Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes. Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.

### <a name="theme-example"></a>Exemplo de tema

No exemplo a seguir do aplicativo de exemplo, a `ThemeInfo` classe especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.

*UIThemeClasses/ThemeInfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata. O <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente encapsula uma subárvore da hierarquia do componente e fornece um único valor para todos os componentes dentro dessa subárvore.

Por exemplo, o aplicativo de exemplo especifica informações de tema ( `ThemeInfo` ) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da `@Body` propriedade. `ButtonClass`é atribuído um valor de `btn-success` no componente layout. Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.

`CascadingValuesParametersLayout`componente

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo. Os valores em cascata são associados a parâmetros em cascata por tipo.

No aplicativo de exemplo, o `CascadingValuesParametersTheme` componente associa o `ThemeInfo` valor em cascata a um parâmetro em cascata. O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.

`CascadingValuesParametersTheme`componente

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> cadeia de caracteres exclusiva para cada <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente e seu [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo correspondente. No exemplo a seguir, dois <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componentes em cascata diferentes instâncias de `MyCascadingType` por nome:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

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

### <a name="tabset-example"></a>Exemplo de TabSet

Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente. Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.

O aplicativo de exemplo tem uma `ITab` interface que implementa as guias:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

O `CascadingValuesParametersTabSet` componente usa o `TabSet` componente, que contém vários `Tab` componentes:

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

Os `Tab` componentes filho não são passados explicitamente como parâmetros para `TabSet` . Em vez disso, os `Tab` componentes filho fazem parte do conteúdo filho do `TabSet` . No entanto, o `TabSet` ainda precisa saber sobre cada `Tab` componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o `TabSet` componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes descendentes `Tab` .

`TabSet`componente

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Os componentes descendentes `Tab` capturam o que contém `TabSet` como um parâmetro em cascata, de modo que os `Tab` componentes se adicionam à `TabSet` coordenada e na qual a guia está ativa.

`Tab`componente

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razormodelo

Os fragmentos de renderização podem ser definidos usando a Razor sintaxe do modelo. Razoros modelos são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:

```razor
@<{HTML tag}>...</{HTML tag}>
```

O exemplo a seguir ilustra como especificar <xref:Microsoft.AspNetCore.Components.RenderFragment> e <xref:Microsoft.AspNetCore.Components.RenderFragment%601> valores e renderizar modelos diretamente em um componente. Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](xref:blazor/templated-components).

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

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

## <a name="scalable-vector-graphics-svg-images"></a>Imagens SVG (gráficos vetoriais escaláveis)

Como Blazor o renderiza imagens html, com suporte para navegadores, incluindo imagens SVG (gráficos vetoriais escalonáveis) (*. svg*), há suporte por meio da `<img>` marca:

```html
<img alt="Example image" src="some-image.svg" />
```

Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos (*. css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

No entanto, a marcação SVG embutida não tem suporte em todos os cenários. Se você posicionar uma `<svg>` marca diretamente em um arquivo de componente (*. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte. Por exemplo, as `<use>` marcas não são respeitadas atualmente e [`@bind`][10] não podem ser usadas com algumas marcas SVG. Para obter mais informações, consulte [suporte a SVG em Blazor (dotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/blazor/server/threat-mitigation>: Inclui diretrizes sobre a criação de Blazor aplicativos de servidor que devem combater o esgotamento de recursos.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
