---
<span data-ttu-id="207e8-101">Título: ' ASP.NET Core Blazor práticas recomendadas de desempenho do Webassembly ' autor: Descrição: ' dicas para aumentar o desempenho em ASP.NET Core Blazor aplicativos Webassembly e evitar problemas comuns de desempenho. '</span><span class="sxs-lookup"><span data-stu-id="207e8-101">title: 'ASP.NET Core Blazor WebAssembly performance best practices' author: description: 'Tips for increasing performance in ASP.NET Core Blazor WebAssembly apps and avoiding common performance problems.'</span></span>
<span data-ttu-id="207e8-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="207e8-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="207e8-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="207e8-103">'Blazor'</span></span>
- <span data-ttu-id="207e8-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="207e8-104">'Identity'</span></span>
- <span data-ttu-id="207e8-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="207e8-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="207e8-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="207e8-106">'Razor'</span></span>
- <span data-ttu-id="207e8-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="207e8-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="207e8-108">BlazorPráticas recomendadas de desempenho do Webassembly ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="207e8-108">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="207e8-109">Por [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="207e8-109">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="207e8-110">Este artigo fornece diretrizes para ASP.NET Core Blazor práticas recomendadas de desempenho do Webassembly.</span><span class="sxs-lookup"><span data-stu-id="207e8-110">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="207e8-111">Evitar renderizações de componente desnecessárias</span><span class="sxs-lookup"><span data-stu-id="207e8-111">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="207e8-112">o algoritmo de diferenciação do evita a rerenderização de um componente quando o algoritmo percebe que o componente não foi alterado.</span><span class="sxs-lookup"><span data-stu-id="207e8-112">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="207e8-113">Substitua o <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> controle refinado sobre a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="207e8-113">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="207e8-114">Se criar um componente somente de interface do usuário que nunca é alterado após a renderização inicial, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para retornar `false` :</span><span class="sxs-lookup"><span data-stu-id="207e8-114">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="207e8-115">A maioria dos aplicativos não requer controle refinado, mas <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> também pode ser usada para renderizar seletivamente um componente que responde a um evento de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="207e8-115">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="207e8-116">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="207e8-116">In the following example:</span></span>

* <span data-ttu-id="207e8-117"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>é substituído e definido como o valor do <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> campo, que é inicialmente `false` quando o componente é carregado.</span><span class="sxs-lookup"><span data-stu-id="207e8-117"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="207e8-118">Quando o botão é selecionado, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> é definido como `true` , o que força o componente a renderizar novamente com o atualizado `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="207e8-118">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="207e8-119">Imediatamente após o reprocessamento, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> o define o valor de <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> voltar para `false` para evitar a rerenderização posterior até a próxima vez que o botão for selecionado.</span><span class="sxs-lookup"><span data-stu-id="207e8-119">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="207e8-120">Para obter mais informações, consulte <xref:blazor/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="207e8-120">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="207e8-121">Virtualizar fragmentos reutilizáveis</span><span class="sxs-lookup"><span data-stu-id="207e8-121">Virtualize re-usable fragments</span></span>

<span data-ttu-id="207e8-122">Os componentes oferecem uma abordagem conveniente para produzir fragmentos reutilizáveis de código e marcação.</span><span class="sxs-lookup"><span data-stu-id="207e8-122">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="207e8-123">Em geral, é recomendável criar componentes individuais que melhor se alinhem com os requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="207e8-123">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="207e8-124">Uma limitação é que cada componente filho adicional contribui para o tempo total que leva para renderizar um componente pai.</span><span class="sxs-lookup"><span data-stu-id="207e8-124">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="207e8-125">Para a maioria dos aplicativos, a sobrecarga adicional é insignificante.</span><span class="sxs-lookup"><span data-stu-id="207e8-125">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="207e8-126">Os aplicativos que produzem um grande número de componentes devem considerar o uso de estratégias para reduzir a sobrecarga de processamento, como limitar o número de componentes renderizados.</span><span class="sxs-lookup"><span data-stu-id="207e8-126">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="207e8-127">Por exemplo, uma grade ou lista que renderiza centenas de linhas que contêm componentes tem uso intensivo de processador para renderização.</span><span class="sxs-lookup"><span data-stu-id="207e8-127">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="207e8-128">Considere a possibilidade de virtualizar um layout de grade ou lista para que apenas um subconjunto dos componentes seja renderizado em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="207e8-128">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="207e8-129">Para obter um exemplo de renderização de subconjunto de componentes, consulte os seguintes componentes no [aplicativo de exemplo de virtualização (repositório GitHub de exemplos ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="207e8-129">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="207e8-130">`Virtualize`componente ([compartilhado/virtualizar. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): um componente escrito em C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para renderizar um conjunto de linhas de dados meteorológicos com base na rolagem do usuário.</span><span class="sxs-lookup"><span data-stu-id="207e8-130">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="207e8-131">`FetchData`componente ([pages/FetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa o `Virtualize` componente para exibir 25 linhas de dados meteorológicos por vez.</span><span class="sxs-lookup"><span data-stu-id="207e8-131">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="207e8-132">Evitar a interoperabilidade JavaScript para empacotar dados</span><span class="sxs-lookup"><span data-stu-id="207e8-132">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="207e8-133">No Blazor Webassembly, uma chamada de interoperabilidade JavaScript (js) deve atravessar o limite Webassembly-js.</span><span class="sxs-lookup"><span data-stu-id="207e8-133">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="207e8-134">A serialização e a desserialização de conteúdo entre os dois contextos cria sobrecarga de processamento para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="207e8-134">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="207e8-135">As chamadas de interoperabilidade do JS frequentes muitas vezes afetam o desempenho.</span><span class="sxs-lookup"><span data-stu-id="207e8-135">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="207e8-136">Para reduzir o marshaling dos dados em todo o limite, determine se o aplicativo pode consolidar muitas cargas pequenas em uma única carga grande para evitar o alto volume de alternância de contexto entre Webassembly e JS.</span><span class="sxs-lookup"><span data-stu-id="207e8-136">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="207e8-137">Usar System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="207e8-137">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="207e8-138">a implementação de interoperabilidade do JS depende de <xref:System.Text.Json> , que é uma biblioteca de SERIALIZAÇÃO JSON de alto desempenho com alocação de memória insuficiente.</span><span class="sxs-lookup"><span data-stu-id="207e8-138">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="207e8-139">O uso <xref:System.Text.Json> do não resulta em tamanho de carga de aplicativo adicional ao adicionar uma ou mais bibliotecas JSON alternativas.</span><span class="sxs-lookup"><span data-stu-id="207e8-139">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="207e8-140">Para obter diretrizes de migração, consulte [como migrar de Newtonsoft. JSON para System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="207e8-140">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="207e8-141">Usar APIs de interoperabilidade do JS síncronas e não marshalled, quando apropriado</span><span class="sxs-lookup"><span data-stu-id="207e8-141">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="207e8-142">O Webassembly oferece duas versões adicionais do <xref:Microsoft.JSInterop.IJSRuntime> sobre a única versão disponível para Blazor aplicativos de servidor:</span><span class="sxs-lookup"><span data-stu-id="207e8-142"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="207e8-143"><xref:Microsoft.JSInterop.IJSInProcessRuntime>permite invocar chamadas de interoperabilidade JS de forma síncrona, que tem menos sobrecarga do que as versões assíncronas:</span><span class="sxs-lookup"><span data-stu-id="207e8-143"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="207e8-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>permite chamadas de interoperabilidade JS não marshalled:</span><span class="sxs-lookup"><span data-stu-id="207e8-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="207e8-145">Embora o uso do <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> tenha a menor sobrecarga das abordagens de interoperabilidade do js, as APIs de JavaScript necessárias para interagir com essas APIs não estão documentadas no momento e estão sujeitas a alterações significativas em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="207e8-145">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="207e8-146">Reduzir o tamanho do aplicativo</span><span class="sxs-lookup"><span data-stu-id="207e8-146">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="207e8-147">Vinculação de IL (linguagem intermediária)</span><span class="sxs-lookup"><span data-stu-id="207e8-147">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="207e8-148">[Vinculando um Blazor O aplicativo Webassembly](xref:host-and-deploy/blazor/configure-linker) reduz o tamanho do aplicativo ao cortar o código não utilizado nos binários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="207e8-148">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="207e8-149">Por padrão, o vinculador só é habilitado ao compilar na `Release` configuração.</span><span class="sxs-lookup"><span data-stu-id="207e8-149">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="207e8-150">Para se beneficiar disso, publique o aplicativo para implantação usando o comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) com a opção [-c |--configuração](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :</span><span class="sxs-lookup"><span data-stu-id="207e8-150">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a><span data-ttu-id="207e8-151">Desabilitar recursos não utilizados</span><span class="sxs-lookup"><span data-stu-id="207e8-151">Disable unused features</span></span>

Blazor<span data-ttu-id="207e8-152">O tempo de execução do Webassembly inclui os seguintes recursos do .NET que podem ser desabilitados se o aplicativo não exigir um tamanho de carga menor:</span><span class="sxs-lookup"><span data-stu-id="207e8-152"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="207e8-153">Um arquivo de dados está incluído para tornar as informações de fuso horário corretas.</span><span class="sxs-lookup"><span data-stu-id="207e8-153">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="207e8-154">Se o aplicativo não exigir esse recurso, considere a possibilidade de desabilitá-lo definindo a `BlazorEnableTimeZoneSupport` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="207e8-154">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="207e8-155">As informações de agrupamento estão incluídas para fazer APIs como o <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionamento correto.</span><span class="sxs-lookup"><span data-stu-id="207e8-155">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="207e8-156">Se você tiver certeza de que o aplicativo não exige os dados de agrupamento, considere desabilitá-lo definindo a `BlazorWebAssemblyPreserveCollationData` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="207e8-156">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
