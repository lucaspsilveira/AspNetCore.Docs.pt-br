---
<span data-ttu-id="6ef08-101">Título: ' criar e usar ASP.NET Core Razor componentes ' autor: Descrição: ' saiba como criar e usar Razor componentes, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente. '</span><span class="sxs-lookup"><span data-stu-id="6ef08-101">title: 'Create and use ASP.NET Core Razor components' author: description: 'Learn how to create and use Razor components, including how to bind to data, handle events, and manage component life cycles.'</span></span>
<span data-ttu-id="6ef08-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6ef08-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6ef08-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6ef08-103">'Blazor'</span></span>
- <span data-ttu-id="6ef08-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6ef08-104">'Identity'</span></span>
- <span data-ttu-id="6ef08-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6ef08-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6ef08-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6ef08-106">'Razor'</span></span>
- <span data-ttu-id="6ef08-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="6ef08-107">'SignalR' uid:</span></span> 

---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="6ef08-108">Criar e usar componentes de ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="6ef08-108">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="6ef08-109">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="6ef08-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="6ef08-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ef08-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="6ef08-111">os aplicativos são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="6ef08-111"> apps are built using *components*.</span></span> <span data-ttu-id="6ef08-112">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="6ef08-112">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="6ef08-113">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="6ef08-113">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="6ef08-114">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="6ef08-114">Components are flexible and lightweight.</span></span> <span data-ttu-id="6ef08-115">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="6ef08-115">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="6ef08-116">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="6ef08-116">Component classes</span></span>

<span data-ttu-id="6ef08-117">Os componentes são implementados em [Razor](xref:mvc/views/razor) arquivos de componente (*. Razor*) usando uma combinação de marcação em C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="6ef08-117">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="6ef08-118">Um componente no Blazor é conhecido formalmente como um \* Razor componente\*.</span><span class="sxs-lookup"><span data-stu-id="6ef08-118">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="6ef08-119">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-119">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="6ef08-120">Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.</span><span class="sxs-lookup"><span data-stu-id="6ef08-120">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="6ef08-121">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="6ef08-121">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="6ef08-122">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe C# inserida chamada *Razor* .</span><span class="sxs-lookup"><span data-stu-id="6ef08-122">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="6ef08-123">Quando um aplicativo é compilado, a marcação HTML e a lógica de renderização C# são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-123">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="6ef08-124">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-124">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="6ef08-125">Os membros da classe de componente são definidos em um [`@code`][1] bloco.</span><span class="sxs-lookup"><span data-stu-id="6ef08-125">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="6ef08-126">No [`@code`][1] bloco, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-126">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="6ef08-127">Mais de um [`@code`][1] bloco é permitido.</span><span class="sxs-lookup"><span data-stu-id="6ef08-127">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="6ef08-128">Os membros do componente podem ser usados como parte da lógica de renderização do componente usando expressões C# que começam com `@` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-128">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="6ef08-129">Por exemplo, um campo C# é renderizado pela prefixação `@` para o nome do campo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-129">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="6ef08-130">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="6ef08-130">The following example evaluates and renders:</span></span>

* <span data-ttu-id="6ef08-131">`headingFontStyle`para o valor da propriedade de CSS para `font-style` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-131">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="6ef08-132">`headingText`para o conteúdo do `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="6ef08-132">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="6ef08-133">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="6ef08-133">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="6ef08-134">em seguida, compara a nova árvore de renderização com a anterior e aplica quaisquer modificações ao Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="6ef08-134"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="6ef08-135">Os componentes são classes C# comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="6ef08-135">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="6ef08-136">Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="6ef08-136">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="6ef08-137">Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="6ef08-137">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="6ef08-138">Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-138">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="6ef08-139">Se o namespace raiz do aplicativo for `BlazorApp` e o `Counter` componente residir na pasta *páginas* :</span><span class="sxs-lookup"><span data-stu-id="6ef08-139">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="6ef08-140">O `Counter` namespace do componente é `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-140">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="6ef08-141">O nome do tipo totalmente qualificado do componente é `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-141">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="6ef08-142">Para pastas personalizadas que contêm componentes, adicione uma `using` instrução ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-142">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="6ef08-143">O exemplo a seguir torna os componentes na pasta *componentes* disponíveis:</span><span class="sxs-lookup"><span data-stu-id="6ef08-143">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="6ef08-144">Como alternativa, um componente pode ser referenciado diretamente:</span><span class="sxs-lookup"><span data-stu-id="6ef08-144">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="6ef08-145">Para obter mais informações, consulte a seção [importar componentes](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="6ef08-145">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="6ef08-146">Sintaxe de Razor</span><span class="sxs-lookup"><span data-stu-id="6ef08-146">Razor syntax</span></span>

Razor<span data-ttu-id="6ef08-147">os componentes em Blazor aplicativos usam extensivamente a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="6ef08-147"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="6ef08-148">Se você não estiver familiarizado com a Razor linguagem de marcação, recomendamos a leitura <xref:mvc/views/razor> antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="6ef08-148">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="6ef08-149">Ao acessar o conteúdo em Razor sintaxe, preste atenção especial às seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="6ef08-149">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="6ef08-150">[Diretivas](xref:mvc/views/razor#directives) &ndash; do `@`-palavras-chave reservadas prefixadas que normalmente alteram a maneira como a marcação de componente é analisada ou funciona.</span><span class="sxs-lookup"><span data-stu-id="6ef08-150">[Directives](xref:mvc/views/razor#directives) &ndash; `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="6ef08-151">[Atributos](xref:mvc/views/razor#directive-attributes) &ndash; de diretiva `@`-palavras-chave reservadas prefixadas que normalmente alteram a maneira como os elementos de componente são analisados ou funcionam.</span><span class="sxs-lookup"><span data-stu-id="6ef08-151">[Directive attributes](xref:mvc/views/razor#directive-attributes) &ndash; `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

## <a name="static-assets"></a><span data-ttu-id="6ef08-152">Ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="6ef08-152">Static assets</span></span>

Blazor<span data-ttu-id="6ef08-153">segue a Convenção de ASP.NET Core aplicativos que posicionam ativos estáticos na [pasta raiz da Web do projeto (wwwroot)](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="6ef08-153"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="6ef08-154">Use um caminho relativo de base ( `/` ) para se referir à raiz da Web para um ativo estático.</span><span class="sxs-lookup"><span data-stu-id="6ef08-154">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="6ef08-155">No exemplo a seguir, *logo. png* está localizado fisicamente na pasta *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="6ef08-155">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="6ef08-156">os componentes **não** dão suporte a notação de barra de til ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="6ef08-156"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="6ef08-157">Para obter informações sobre como definir o caminho base de um aplicativo, consulte <xref:host-and-deploy/blazor/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="6ef08-157">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="6ef08-158">Não há suporte para auxiliares de marcas nos componentes</span><span class="sxs-lookup"><span data-stu-id="6ef08-158">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="6ef08-159">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) não têm suporte em Razor componentes (arquivos *. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="6ef08-159">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="6ef08-160">Para fornecer a funcionalidade do tipo auxiliar de marca no Blazor , crie um componente com a mesma funcionalidade que o auxiliar de marca e use o componente em vez disso.</span><span class="sxs-lookup"><span data-stu-id="6ef08-160">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="6ef08-161">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="6ef08-161">Use components</span></span>

<span data-ttu-id="6ef08-162">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="6ef08-162">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="6ef08-163">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-163">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="6ef08-164">A marcação a seguir no *index. Razor* renderiza uma `HeadingComponent` instância:</span><span class="sxs-lookup"><span data-stu-id="6ef08-164">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="6ef08-165">*Componentes/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-165">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="6ef08-166">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="6ef08-166">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="6ef08-167">A adição de uma [`@using`][2] diretiva para o namespace do componente torna o componente disponível, o que resolve o aviso.</span><span class="sxs-lookup"><span data-stu-id="6ef08-167">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="6ef08-168">Roteamento</span><span class="sxs-lookup"><span data-stu-id="6ef08-168">Routing</span></span>

<span data-ttu-id="6ef08-169">O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-169">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="6ef08-170">Quando um Razor arquivo com uma [`@page`][9] diretiva é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="6ef08-170">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="6ef08-171">Em tempo de execução, o roteador procura classes de componentes com um `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="6ef08-171">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="6ef08-172">Para obter mais informações, consulte <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="6ef08-172">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="6ef08-173">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="6ef08-173">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="6ef08-174">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="6ef08-174">Route parameters</span></span>

<span data-ttu-id="6ef08-175">Os componentes podem receber parâmetros de rota do modelo de rota fornecido na [`@page`][9] diretiva.</span><span class="sxs-lookup"><span data-stu-id="6ef08-175">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="6ef08-176">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="6ef08-176">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="6ef08-177">*Páginas/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-177">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="6ef08-178">Não há suporte para parâmetros opcionais, portanto, duas [`@page`][9] diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="6ef08-178">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="6ef08-179">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6ef08-179">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="6ef08-180">A segunda [`@page`][9] diretiva recebe o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="6ef08-180">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="6ef08-181">A sintaxe de parâmetro *catch-all* ( `*` / `**` ), que captura o caminho entre vários limites de pasta, **não** tem suporte em Razor componentes (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="6ef08-181">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="6ef08-182">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="6ef08-182">Component parameters</span></span>

<span data-ttu-id="6ef08-183">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente com o `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-183">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="6ef08-184">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="6ef08-184">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="6ef08-185">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="6ef08-186">No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da `Title` Propriedade do `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-186">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="6ef08-187">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="6ef08-188">Não crie componentes que gravam em seus próprios *parâmetros de componente*, use um campo particular em vez disso.</span><span class="sxs-lookup"><span data-stu-id="6ef08-188">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="6ef08-189">Para obter mais informações, consulte a seção [não criar componentes que gravam em suas próprias propriedades de parâmetro](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="6ef08-189">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="6ef08-190">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="6ef08-190">Child content</span></span>

<span data-ttu-id="6ef08-191">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-191">Components can set the content of another component.</span></span> <span data-ttu-id="6ef08-192">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="6ef08-192">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="6ef08-193">No exemplo a seguir, o `ChildComponent` tem uma `ChildContent` propriedade que representa um `RenderFragment` , que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="6ef08-193">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="6ef08-194">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="6ef08-194">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="6ef08-195">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do painel de inicialização `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-195">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="6ef08-196">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-196">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="6ef08-197">A propriedade que recebe o `RenderFragment` conteúdo deve ser nomeada `ChildContent` por convenção.</span><span class="sxs-lookup"><span data-stu-id="6ef08-197">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="6ef08-198">O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das `<ChildComponent>` marcas.</span><span class="sxs-lookup"><span data-stu-id="6ef08-198">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="6ef08-199">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-199">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="6ef08-200">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="6ef08-200">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="6ef08-201">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-201">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="6ef08-202">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a [`@attributes`][3] Razor diretiva.</span><span class="sxs-lookup"><span data-stu-id="6ef08-202">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="6ef08-203">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="6ef08-203">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="6ef08-204">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que dê suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="6ef08-204">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="6ef08-205">No exemplo a seguir, o primeiro `<input>` elemento ( `id="useIndividualParams"` ) usa parâmetros de componente individuais, enquanto o segundo `<input>` elemento ( `id="useAttributesDict"` ) usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="6ef08-205">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="6ef08-206">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="6ef08-206">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="6ef08-207">Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="6ef08-207">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="6ef08-208">Os elementos renderizados `<input>` usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="6ef08-208">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="6ef08-209">Para aceitar atributos arbitrários, defina um parâmetro de componente usando o `[Parameter]` atributo com a `CaptureUnmatchedValues` propriedade definida como `true` :</span><span class="sxs-lookup"><span data-stu-id="6ef08-209">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="6ef08-210">A `CaptureUnmatchedValues` Propriedade on `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6ef08-210">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="6ef08-211">Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-211">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="6ef08-212">O tipo de propriedade usado com `CaptureUnmatchedValues` deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="6ef08-212">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="6ef08-213">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="6ef08-213">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="6ef08-214">A posição de [`@attributes`][3] relativo à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="6ef08-214">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="6ef08-215">Quando [`@attributes`][3] são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro).</span><span class="sxs-lookup"><span data-stu-id="6ef08-215">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="6ef08-216">Considere o exemplo a seguir de um componente que consome um `Child` componente:</span><span class="sxs-lookup"><span data-stu-id="6ef08-216">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="6ef08-217">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="6ef08-218">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-218">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="6ef08-219">O `Child` atributo do componente `extra` é definido à direita de [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="6ef08-219">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="6ef08-220">O `Parent` renderizado do componente `<div>` contém `extra="5"` quando passado pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):</span><span class="sxs-lookup"><span data-stu-id="6ef08-220">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="6ef08-221">No exemplo a seguir, a ordem de `extra` e [`@attributes`][3] é invertida no `Child` `<div>` :</span><span class="sxs-lookup"><span data-stu-id="6ef08-221">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="6ef08-222">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-222">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="6ef08-223">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-223">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="6ef08-224">O renderizado `<div>` no `Parent` componente contém `extra="10"` quando passado pelo atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="6ef08-224">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="6ef08-225">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="6ef08-225">Capture references to components</span></span>

<span data-ttu-id="6ef08-226">As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-226">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="6ef08-227">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="6ef08-227">To capture a component reference:</span></span>

* <span data-ttu-id="6ef08-228">Adicione um [`@ref`][4] atributo ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="6ef08-228">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="6ef08-229">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="6ef08-229">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="6ef08-230">Quando o componente é renderizado, o `loginDialog` campo é populado com a `MyLoginDialog` instância de componente filho.</span><span class="sxs-lookup"><span data-stu-id="6ef08-230">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="6ef08-231">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-231">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ef08-232">A `loginDialog` variável é populada apenas depois que o componente é renderizado e sua saída inclui o `MyLoginDialog` elemento.</span><span class="sxs-lookup"><span data-stu-id="6ef08-232">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="6ef08-233">Até esse ponto, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="6ef08-233">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="6ef08-234">Para manipular referências de componentes após a conclusão da renderização do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="6ef08-234">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="6ef08-235">Para fazer referência a componentes em um loop, consulte [capturar referências para vários componentes filho semelhantes (dotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="6ef08-235">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="6ef08-236">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), ela não é um recurso de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ef08-236">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="6ef08-237">As referências de componente não são passadas para o código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ef08-237">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="6ef08-238">As referências de componente são usadas apenas no código .NET.</span><span class="sxs-lookup"><span data-stu-id="6ef08-238">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="6ef08-239">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="6ef08-239">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="6ef08-240">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="6ef08-240">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="6ef08-241">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-241">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="6ef08-242">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="6ef08-242">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="6ef08-243">usa um contexto de sincronização ( `SynchronizationContext` ) para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="6ef08-243"> uses a synchronization context (`SynchronizationContext`) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="6ef08-244">Os métodos de [ciclo de vida](xref:blazor/lifecycle) de um componente e quaisquer retornos de chamada de evento que são gerados pelo Blazor são executados no contexto de sincronização.</span><span class="sxs-lookup"><span data-stu-id="6ef08-244">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="6ef08-245">O contexto de sincronização do servidor tenta emular um ambiente de thread único para que ele corresponda ao modelo Webassembly no navegador, que é um thread único.</span><span class="sxs-lookup"><span data-stu-id="6ef08-245"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="6ef08-246">Em qualquer momento determinado, o trabalho é executado em exatamente um thread, dando a impressão de um único thread lógico.</span><span class="sxs-lookup"><span data-stu-id="6ef08-246">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="6ef08-247">Duas operações não são executadas simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-247">No two operations execute concurrently.</span></span>

<span data-ttu-id="6ef08-248">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o `InvokeAsync` método, que será expedido para o Blazor contexto de sincronização do.</span><span class="sxs-lookup"><span data-stu-id="6ef08-248">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's synchronization context.</span></span> <span data-ttu-id="6ef08-249">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="6ef08-249">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="6ef08-250">Registre o `NotifierService` como um singletion:</span><span class="sxs-lookup"><span data-stu-id="6ef08-250">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="6ef08-251">No Blazor Webassembly, registre o serviço em `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="6ef08-251">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="6ef08-252">Em Blazor servidor, registre o serviço em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6ef08-252">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="6ef08-253">Use o `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="6ef08-253">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="6ef08-254">No exemplo anterior, `NotifierService` invoca o método do componente `OnNotify` fora do contexto de Blazor sincronização do.</span><span class="sxs-lookup"><span data-stu-id="6ef08-254">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="6ef08-255">`InvokeAsync`é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="6ef08-255">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="6ef08-256">Use \@ a chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="6ef08-256">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="6ef08-257">Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o Blazor algoritmo de diferenciação do deve decidir quais elementos ou componentes anteriores podem ser retidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="6ef08-257">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="6ef08-258">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-258">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="6ef08-259">Considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="6ef08-259">Consider the following example:</span></span>

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

<span data-ttu-id="6ef08-260">O conteúdo da `People` coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="6ef08-260">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="6ef08-261">Quando o componente é rerenderizado, o `<DetailsEditor>` componente pode ser alterado para receber `Details` valores de parâmetro diferentes.</span><span class="sxs-lookup"><span data-stu-id="6ef08-261">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="6ef08-262">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="6ef08-262">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="6ef08-263">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="6ef08-263">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="6ef08-264">O processo de mapeamento pode ser controlado com o [`@key`][5] atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="6ef08-264">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="6ef08-265">[`@key`][5]faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="6ef08-265">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="6ef08-266">Quando a `People` coleção é alterada, o algoritmo diff mantém a associação entre `<DetailsEditor>` instâncias e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="6ef08-266">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="6ef08-267">Se um `Person` for excluído da `People` lista, somente a instância correspondente `<DetailsEditor>` será REmovida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="6ef08-267">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="6ef08-268">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="6ef08-268">Other instances are left unchanged.</span></span>
* <span data-ttu-id="6ef08-269">Se um `Person` for inserido em alguma posição na lista, uma nova `<DetailsEditor>` instância será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-269">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="6ef08-270">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="6ef08-270">Other instances are left unchanged.</span></span>
* <span data-ttu-id="6ef08-271">Se `Person` as entradas forem reordenadas, as `<DetailsEditor>` instâncias correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="6ef08-271">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="6ef08-272">Em alguns cenários, o uso de [`@key`][5] minimiza a complexidade da rerenderização e evita possíveis problemas com partes com estado da alteração do dom, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="6ef08-272">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ef08-273">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="6ef08-273">Keys are local to each container element or component.</span></span> <span data-ttu-id="6ef08-274">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="6ef08-274">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="6ef08-275">Quando usar a \@ chave</span><span class="sxs-lookup"><span data-stu-id="6ef08-275">When to use \@key</span></span>

<span data-ttu-id="6ef08-276">Normalmente, faz sentido usar [`@key`][5] sempre que uma lista é renderizada (por exemplo, em um `@foreach` bloco) e um valor adequado existe para definir o [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="6ef08-276">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="6ef08-277">Você também pode usar [`@key`][5] para evitar a Blazor preservação de uma subárvore de elemento ou componente quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="6ef08-277">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="6ef08-278">Se `@currentPerson` forem alteradas, a [`@key`][5] diretiva de atributo forçará Blazor a descartar todo `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="6ef08-278">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="6ef08-279">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando houver `@currentPerson` alterações.</span><span class="sxs-lookup"><span data-stu-id="6ef08-279">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="6ef08-280">Quando não usar a \@ chave</span><span class="sxs-lookup"><span data-stu-id="6ef08-280">When not to use \@key</span></span>

<span data-ttu-id="6ef08-281">Há um custo de desempenho ao comparar com [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="6ef08-281">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="6ef08-282">O custo de desempenho não é grande, mas só especifica [`@key`][5] se controlar as regras de preservação de elementos ou componentes beneficiam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-282">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="6ef08-283">Mesmo que [`@key`][5] não seja usado, Blazor o preserva as instâncias de elemento filho e de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="6ef08-283">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="6ef08-284">A única vantagem de usar o [`@key`][5] é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="6ef08-284">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="6ef08-285">Quais valores usar para a \@ chave</span><span class="sxs-lookup"><span data-stu-id="6ef08-285">What values to use for \@key</span></span>

<span data-ttu-id="6ef08-286">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="6ef08-286">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="6ef08-287">Instâncias de objeto de modelo (por exemplo, uma `Person` instância como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="6ef08-287">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="6ef08-288">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="6ef08-288">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="6ef08-289">Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int` , `string` ou `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="6ef08-289">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="6ef08-290">Verifique se os valores usados para [`@key`][5] não conflitam.</span><span class="sxs-lookup"><span data-stu-id="6ef08-290">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="6ef08-291">Se valores conflitantes forem detectados no mesmo elemento pai, Blazor o lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="6ef08-291">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="6ef08-292">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="6ef08-292">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="6ef08-293">Não crie componentes que gravam em suas próprias propriedades de parâmetro</span><span class="sxs-lookup"><span data-stu-id="6ef08-293">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="6ef08-294">Os parâmetros são substituídos nas seguintes condições:</span><span class="sxs-lookup"><span data-stu-id="6ef08-294">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="6ef08-295">O conteúdo de um componente filho é renderizado com um `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-295">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="6ef08-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado no componente pai.</span><span class="sxs-lookup"><span data-stu-id="6ef08-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="6ef08-297">Os parâmetros são redefinidos porque o componente pai é rerenderizado quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado e novos valores de parâmetro são fornecidos ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="6ef08-297">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="6ef08-298">Considere o seguinte `Expander` componente que:</span><span class="sxs-lookup"><span data-stu-id="6ef08-298">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="6ef08-299">Renderiza conteúdo filho.</span><span class="sxs-lookup"><span data-stu-id="6ef08-299">Renders child content.</span></span>
* <span data-ttu-id="6ef08-300">Alterna a exibição de conteúdo filho com um parâmetro de componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-300">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="6ef08-301">O `Expander` componente é adicionado a um componente pai que pode chamar `StateHasChanged` :</span><span class="sxs-lookup"><span data-stu-id="6ef08-301">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="6ef08-302">Inicialmente, os `Expander` componentes se comportam de forma independente quando suas `Expanded` Propriedades são alternadas.</span><span class="sxs-lookup"><span data-stu-id="6ef08-302">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="6ef08-303">Os componentes filho mantêm seus Estados como esperado.</span><span class="sxs-lookup"><span data-stu-id="6ef08-303">The child components maintain their states as expected.</span></span> <span data-ttu-id="6ef08-304">Quando `StateHasChanged` é chamado no pai, o `Expanded` parâmetro do primeiro componente filho é redefinido de volta para seu valor inicial ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="6ef08-304">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="6ef08-305">O `Expander` valor do segundo componente `Expanded` não é redefinido porque nenhum conteúdo filho é renderizado no segundo componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-305">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="6ef08-306">Para manter o estado no cenário anterior, use um *campo particular* no `Expander` componente para manter seu estado de alternância.</span><span class="sxs-lookup"><span data-stu-id="6ef08-306">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="6ef08-307">O seguinte `Expander` componente:</span><span class="sxs-lookup"><span data-stu-id="6ef08-307">The following `Expander` component:</span></span>

* <span data-ttu-id="6ef08-308">Aceita o `Expanded` valor do parâmetro de componente do pai.</span><span class="sxs-lookup"><span data-stu-id="6ef08-308">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="6ef08-309">Atribui o valor do parâmetro de componente a um *campo privado* ( `expanded` ) no [evento OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="6ef08-309">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="6ef08-310">Usa o campo particular para manter seu estado de alternância interno.</span><span class="sxs-lookup"><span data-stu-id="6ef08-310">Uses the private field to maintain its internal toggle state.</span></span>

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

## <a name="partial-class-support"></a><span data-ttu-id="6ef08-311">Suporte de classe parcial</span><span class="sxs-lookup"><span data-stu-id="6ef08-311">Partial class support</span></span>

Razor<span data-ttu-id="6ef08-312">os componentes são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="6ef08-312"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="6ef08-313">os componentes são criados usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="6ef08-313"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="6ef08-314">O código C# é definido em um [`@code`][1] bloco com marcação e Razor código HTML em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-314">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="6ef08-315">os modelos definem seus Razor componentes usando essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="6ef08-315"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="6ef08-316">O código C# é colocado em um arquivo code-behind definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="6ef08-316">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="6ef08-317">O exemplo a seguir mostra o `Counter` componente padrão com um [`@code`][1] bloco em um aplicativo gerado a partir de um Blazor modelo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-317">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="6ef08-318">A marcação HTML, o Razor código e o código C# estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="6ef08-318">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="6ef08-319">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-319">*Counter.razor*:</span></span>

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

<span data-ttu-id="6ef08-320">O `Counter` componente também pode ser criado usando um arquivo code-behind com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="6ef08-320">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="6ef08-321">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-321">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="6ef08-322">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-322">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="6ef08-323">Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="6ef08-323">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="6ef08-324">Os namespaces típicos usados pelos Razor componentes incluem:</span><span class="sxs-lookup"><span data-stu-id="6ef08-324">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="6ef08-325">Especificar uma classe base</span><span class="sxs-lookup"><span data-stu-id="6ef08-325">Specify a base class</span></span>

<span data-ttu-id="6ef08-326">A [`@inherits`][6] diretiva pode ser usada para especificar uma classe base para um componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-326">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="6ef08-327">O exemplo a seguir mostra como um componente pode herdar uma classe base, `BlazorRocksBase` ,, para fornecer as propriedades e os métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-327">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="6ef08-328">A classe base deve derivar de `ComponentBase` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-328">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="6ef08-329">*Páginas/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-329">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="6ef08-330">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-330">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="6ef08-331">Especificar um atributo</span><span class="sxs-lookup"><span data-stu-id="6ef08-331">Specify an attribute</span></span>

<span data-ttu-id="6ef08-332">Os atributos podem ser especificados em Razor componentes com a [`@attribute`][7] diretiva.</span><span class="sxs-lookup"><span data-stu-id="6ef08-332">Attributes can be specified in Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="6ef08-333">O exemplo a seguir aplica o `[Authorize]` atributo à classe de componente:</span><span class="sxs-lookup"><span data-stu-id="6ef08-333">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="6ef08-334">Importar componentes</span><span class="sxs-lookup"><span data-stu-id="6ef08-334">Import components</span></span>

<span data-ttu-id="6ef08-335">O namespace de um componente criado com o Razor é baseado em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="6ef08-335">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="6ef08-336">[`@namespace`][8]designação na Razor marcação de arquivo (*. Razor*) ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="6ef08-336">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="6ef08-337">O projeto `RootNamespace` no arquivo de projeto ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="6ef08-337">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="6ef08-338">O nome do projeto, obtido do nome do arquivo do projeto (*. csproj*) e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-338">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="6ef08-339">Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-339">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="6ef08-340">Os componentes seguem regras de associação de nome C#.</span><span class="sxs-lookup"><span data-stu-id="6ef08-340">Components follow C# name binding rules.</span></span> <span data-ttu-id="6ef08-341">Para o `Index` componente neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="6ef08-341">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="6ef08-342">Na mesma pasta, *páginas*.</span><span class="sxs-lookup"><span data-stu-id="6ef08-342">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="6ef08-343">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-343">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="6ef08-344">Os componentes definidos em um namespace diferente são trazidos para o escopo usando a Razor [`@using`][2] diretiva do.</span><span class="sxs-lookup"><span data-stu-id="6ef08-344">Components defined in a different namespace are brought into scope using Razor's [`@using`][2] directive.</span></span>

<span data-ttu-id="6ef08-345">Se outro componente, `NavMenu.razor` , existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado no `Index.razor` com a seguinte [`@using`][2] instrução:</span><span class="sxs-lookup"><span data-stu-id="6ef08-345">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following [`@using`][2] statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="6ef08-346">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não exige a [`@using`][2] diretiva:</span><span class="sxs-lookup"><span data-stu-id="6ef08-346">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="6ef08-347">`global::`Não há suporte para a qualificação.</span><span class="sxs-lookup"><span data-stu-id="6ef08-347">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="6ef08-348">`using`Não há suporte para a importação de componentes com instruções com alias (por exemplo, `@using Foo = Bar` ).</span><span class="sxs-lookup"><span data-stu-id="6ef08-348">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="6ef08-349">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="6ef08-349">Partially qualified names aren't supported.</span></span> <span data-ttu-id="6ef08-350">Por exemplo, `@using BlazorSample` `NavMenu.razor` não há suporte para adicionar e referenciar com `<Shared.NavMenu></Shared.NavMenu>` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-350">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="6ef08-351">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="6ef08-351">Conditional HTML element attributes</span></span>

<span data-ttu-id="6ef08-352">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="6ef08-352">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="6ef08-353">Se o valor for `false` ou `null` , o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="6ef08-353">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="6ef08-354">Se o valor é `true` , o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="6ef08-354">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="6ef08-355">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="6ef08-355">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="6ef08-356">Se `IsCompleted` for `true` , a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="6ef08-356">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="6ef08-357">Se `IsCompleted` for `false` , a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="6ef08-357">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="6ef08-358">Para obter mais informações, consulte <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="6ef08-358">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="6ef08-359">Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-359">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="6ef08-360">Nesses casos, use um `string` tipo em vez de um `bool` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-360">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="6ef08-361">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="6ef08-361">Raw HTML</span></span>

<span data-ttu-id="6ef08-362">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="6ef08-362">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="6ef08-363">Para renderizar HTML bruto, empacote o conteúdo HTML em um `MarkupString` valor.</span><span class="sxs-lookup"><span data-stu-id="6ef08-363">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="6ef08-364">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="6ef08-364">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="6ef08-365">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="6ef08-365">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="6ef08-366">O exemplo a seguir mostra como usar o `MarkupString` tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="6ef08-366">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="6ef08-367">Valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="6ef08-367">Cascading values and parameters</span></span>

<span data-ttu-id="6ef08-368">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-368">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="6ef08-369">Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="6ef08-369">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="6ef08-370">Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.</span><span class="sxs-lookup"><span data-stu-id="6ef08-370">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="6ef08-371">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="6ef08-371">Theme example</span></span>

<span data-ttu-id="6ef08-372">No exemplo a seguir do aplicativo de exemplo, a `ThemeInfo` classe especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-372">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="6ef08-373">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="6ef08-373">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="6ef08-374">Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="6ef08-374">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="6ef08-375">O `CascadingValue` componente encapsula uma subárvore da hierarquia do componente e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="6ef08-375">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="6ef08-376">Por exemplo, o aplicativo de exemplo especifica informações de tema ( `ThemeInfo` ) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da `@Body` propriedade.</span><span class="sxs-lookup"><span data-stu-id="6ef08-376">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="6ef08-377">`ButtonClass`é atribuído um valor de `btn-success` no componente layout.</span><span class="sxs-lookup"><span data-stu-id="6ef08-377">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="6ef08-378">Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="6ef08-378">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="6ef08-379">`CascadingValuesParametersLayout`componente</span><span class="sxs-lookup"><span data-stu-id="6ef08-379">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="6ef08-380">Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o `[CascadingParameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-380">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="6ef08-381">Os valores em cascata são associados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-381">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="6ef08-382">No aplicativo de exemplo, o `CascadingValuesParametersTheme` componente associa o `ThemeInfo` valor em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="6ef08-382">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="6ef08-383">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-383">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="6ef08-384">`CascadingValuesParametersTheme`componente</span><span class="sxs-lookup"><span data-stu-id="6ef08-384">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="6ef08-385">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma `Name` cadeia de caracteres exclusiva para cada `CascadingValue` componente e seu correspondente `CascadingParameter` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-385">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="6ef08-386">No exemplo a seguir, dois `CascadingValue` componentes em cascata diferentes instâncias de `MyCascadingType` por nome:</span><span class="sxs-lookup"><span data-stu-id="6ef08-386">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="6ef08-387">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="6ef08-387">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="6ef08-388">Exemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="6ef08-388">TabSet example</span></span>

<span data-ttu-id="6ef08-389">Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-389">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="6ef08-390">Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-390">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="6ef08-391">O aplicativo de exemplo tem uma `ITab` interface que implementa as guias:</span><span class="sxs-lookup"><span data-stu-id="6ef08-391">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="6ef08-392">O `CascadingValuesParametersTabSet` componente usa o `TabSet` componente, que contém vários `Tab` componentes:</span><span class="sxs-lookup"><span data-stu-id="6ef08-392">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="6ef08-393">Os `Tab` componentes filho não são passados explicitamente como parâmetros para `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-393">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="6ef08-394">Em vez disso, os `Tab` componentes filho fazem parte do conteúdo filho do `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-394">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="6ef08-395">No entanto, o `TabSet` ainda precisa saber sobre cada `Tab` componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o `TabSet` componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes descendentes `Tab` .</span><span class="sxs-lookup"><span data-stu-id="6ef08-395">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="6ef08-396">`TabSet`componente</span><span class="sxs-lookup"><span data-stu-id="6ef08-396">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="6ef08-397">Os componentes descendentes `Tab` capturam o que contém `TabSet` como um parâmetro em cascata, de modo que os `Tab` componentes se adicionam à `TabSet` coordenada e na qual a guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="6ef08-397">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="6ef08-398">`Tab`componente</span><span class="sxs-lookup"><span data-stu-id="6ef08-398">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="6ef08-399">modelo</span><span class="sxs-lookup"><span data-stu-id="6ef08-399"> templates</span></span>

<span data-ttu-id="6ef08-400">Os fragmentos de renderização podem ser definidos usando a Razor sintaxe do modelo.</span><span class="sxs-lookup"><span data-stu-id="6ef08-400">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="6ef08-401">os modelos são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="6ef08-401"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="6ef08-402">O exemplo a seguir ilustra como especificar `RenderFragment` e `RenderFragment<T>` valores e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="6ef08-402">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="6ef08-403">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="6ef08-403">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="6ef08-404">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="6ef08-404">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="6ef08-405">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="6ef08-405">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="6ef08-406">Como Blazor o renderiza imagens html, com suporte para navegadores, incluindo imagens SVG (gráficos vetoriais escalonáveis) (*. svg*), há suporte por meio da `<img>` marca:</span><span class="sxs-lookup"><span data-stu-id="6ef08-406">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="6ef08-407">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos (*. css*):</span><span class="sxs-lookup"><span data-stu-id="6ef08-407">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="6ef08-408">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="6ef08-408">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="6ef08-409">Se você posicionar uma `<svg>` marca diretamente em um arquivo de componente (*. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="6ef08-409">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="6ef08-410">Por exemplo, as `<use>` marcas não são respeitadas atualmente e `@bind` não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="6ef08-410">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="6ef08-411">Para obter mais informações, consulte [suporte a SVG em Blazor (dotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="6ef08-411">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ef08-412">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6ef08-412">Additional resources</span></span>

* <span data-ttu-id="6ef08-413"><xref:security/blazor/server/threat-mitigation>&ndash;Inclui diretrizes sobre a criação Blazor Aplicativos de servidor que devem competir com esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="6ef08-413"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

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
