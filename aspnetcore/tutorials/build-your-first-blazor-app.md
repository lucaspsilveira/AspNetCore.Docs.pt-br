---
<span data-ttu-id="6f690-101">Título: "criar seu primeiro Blazor aplicativo" autor: guardrex Descrição: "compilar um Blazor aplicativo passo a passo".</span><span class="sxs-lookup"><span data-stu-id="6f690-101">title: 'Build your first Blazor app' author: guardrex description: 'Build a Blazor app step-by-step.'</span></span>
<span data-ttu-id="6f690-102">monikerRange: ' >= aspnetcore-3,0 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/19/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="6f690-102">monikerRange: '>= aspnetcore-3.0' ms.author: riande ms.custom: mvc ms.date: 05/19/2020 no-loc:</span></span>
- <span data-ttu-id="6f690-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f690-103">'Blazor'</span></span>
- <span data-ttu-id="6f690-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f690-104">'Identity'</span></span>
- <span data-ttu-id="6f690-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f690-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f690-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f690-106">'Razor'</span></span>
- <span data-ttu-id="6f690-107">' SignalR ' UID: Tutorials/First-mais incrivelmente-aplicativo</span><span class="sxs-lookup"><span data-stu-id="6f690-107">'SignalR' uid: tutorials/first-blazor-app</span></span>

---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="6f690-108">Crie seu primeiro Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="6f690-108">Build your first Blazor app</span></span>

<span data-ttu-id="6f690-109">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f690-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6f690-110">Este tutorial mostra como criar e modificar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-110">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="6f690-111">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="6f690-111">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f690-112">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="6f690-112">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="6f690-113">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="6f690-113">Modify Razor components</span></span>
> * <span data-ttu-id="6f690-114">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="6f690-114">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="6f690-115">Usar injeção de dependência (DI) e roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="6f690-115">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="6f690-116">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="6f690-116">At the end of this tutorial, you'll have a working chat app.</span></span>

## <a name="build-components"></a><span data-ttu-id="6f690-117">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="6f690-117">Build components</span></span>

1. <span data-ttu-id="6f690-118">Siga as orientações no <xref:blazor/get-started> artigo para criar um Blazor projeto para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="6f690-118">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="6f690-119">Nomeie o projeto como *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="6f690-119">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="6f690-120">Navegue até cada uma das três páginas do aplicativo na pasta *páginas* : Home, Counter e FETCH Data.</span><span class="sxs-lookup"><span data-stu-id="6f690-120">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="6f690-121">Essas páginas são implementadas pelos Razor arquivos de componente *index. Razor*, *Counter. Razor*e *FetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="6f690-121">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="6f690-122">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="6f690-122">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="6f690-123">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6f690-123">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="6f690-124">Com o Blazor , você pode escrever C# em vez disso.</span><span class="sxs-lookup"><span data-stu-id="6f690-124">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="6f690-125">Examine a implementação do componente `Counter` no arquivo *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="6f690-125">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="6f690-126">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6f690-126">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="6f690-127">A interface do usuário do componente `Counter` é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="6f690-127">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="6f690-128">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe C# inserida chamada [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="6f690-128">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="6f690-129">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="6f690-129">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="6f690-130">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="6f690-130">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="6f690-131">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="6f690-131">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="6f690-132">No bloco `@code`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="6f690-132">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="6f690-133">Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="6f690-133">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="6f690-134">Quando o botão **Clique aqui** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="6f690-134">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="6f690-135">O manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="6f690-135">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="6f690-136">O componente `Counter` regenera sua árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="6f690-136">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="6f690-137">A nova árvore de renderização é comparada à anterior.</span><span class="sxs-lookup"><span data-stu-id="6f690-137">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="6f690-138">Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="6f690-138">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="6f690-139">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="6f690-139">The displayed count is updated.</span></span>

1. <span data-ttu-id="6f690-140">Modifique a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="6f690-140">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="6f690-141">Recompile e execute o aplicativo para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="6f690-141">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="6f690-142">Selecione o botão **Clique aqui**.</span><span class="sxs-lookup"><span data-stu-id="6f690-142">Select the **Click me** button.</span></span> <span data-ttu-id="6f690-143">O contador incrementa em dois.</span><span class="sxs-lookup"><span data-stu-id="6f690-143">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="6f690-144">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="6f690-144">Use components</span></span>

<span data-ttu-id="6f690-145">Inclua um componente em outro componente usando uma sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="6f690-145">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="6f690-146">Adicione o componente `Counter` no componente `Index` do aplicativo adicionando um elemento `<Counter />` ao componente `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="6f690-146">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="6f690-147">Se você estiver usando Blazor o Webassembly para essa experiência, um `SurveyPrompt` componente será usado pelo `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="6f690-147">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="6f690-148">Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="6f690-148">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="6f690-149">Se você estiver usando um Blazor aplicativo de servidor para essa experiência, adicione o `<Counter />` elemento ao `Index` componente:</span><span class="sxs-lookup"><span data-stu-id="6f690-149">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="6f690-150">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6f690-150">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="6f690-151">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-151">Rebuild and run the app.</span></span> <span data-ttu-id="6f690-152">O componente `Index` tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="6f690-152">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="6f690-153">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="6f690-153">Component parameters</span></span>

<span data-ttu-id="6f690-154">Componentes também podem ter parâmetros.</span><span class="sxs-lookup"><span data-stu-id="6f690-154">Components can also have parameters.</span></span> <span data-ttu-id="6f690-155">Os parâmetros de componente são definidos usando propriedades públicas na classe de componente com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6f690-155">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="6f690-156">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="6f690-156">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="6f690-157">Atualize o código C# do componente da `@code` seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6f690-157">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="6f690-158">Adicione uma `IncrementAmount` propriedade pública com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6f690-158">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="6f690-159">Altere o `IncrementCount` método para usar a `IncrementAmount` propriedade ao aumentar o valor de `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="6f690-159">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="6f690-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6f690-160">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="6f690-161">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="6f690-161">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="6f690-162">Defina o valor para incrementar o contador em 10.</span><span class="sxs-lookup"><span data-stu-id="6f690-162">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="6f690-163">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6f690-163">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="6f690-164">Recarregue o componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="6f690-164">Reload the `Index` component.</span></span> <span data-ttu-id="6f690-165">O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6f690-165">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="6f690-166">O contador no componente `Counter` continua a incrementar em um.</span><span class="sxs-lookup"><span data-stu-id="6f690-166">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="6f690-167">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="6f690-167">Route to components</span></span>

<span data-ttu-id="6f690-168">A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente `Counter` é um ponto de extremidade de roteamento.</span><span class="sxs-lookup"><span data-stu-id="6f690-168">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="6f690-169">O componente `Counter` manipula solicitações enviadas para `/counter`.</span><span class="sxs-lookup"><span data-stu-id="6f690-169">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="6f690-170">Sem a diretiva `@page`, o componente não lidará com as solicitações roteadas, mas ele ainda poderá ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="6f690-170">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="6f690-171">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6f690-171">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="6f690-172">Experiência do servidor</span><span class="sxs-lookup"><span data-stu-id="6f690-172"> Server experience</span></span>

<span data-ttu-id="6f690-173">Se estiver trabalhando com um Blazor aplicativo de servidor, o `WeatherForecastService` serviço será registrado como um [singleton](xref:fundamentals/dependency-injection#service-lifetimes) no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6f690-173">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f690-174">Uma instância do serviço está disponível em todo o aplicativo por meio de [injeção de dependência (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="6f690-174">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="6f690-175">A [`@inject`](xref:mvc/views/razor#inject) diretiva é usada para injetar a instância do `WeatherForecastService` serviço no `FetchData` componente.</span><span class="sxs-lookup"><span data-stu-id="6f690-175">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="6f690-176">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="6f690-176">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="6f690-177">O componente `FetchData` usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="6f690-177">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="6f690-178">Experiência de Webassembly</span><span class="sxs-lookup"><span data-stu-id="6f690-178"> WebAssembly experience</span></span>

<span data-ttu-id="6f690-179">Se estiver trabalhando com um Blazor aplicativo Webassembly, <xref:System.Net.Http.HttpClient> será injetado para obter dados de previsão do tempo do arquivo *Weather. JSON* na pasta *wwwroot/Sample-data* .</span><span class="sxs-lookup"><span data-stu-id="6f690-179">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="6f690-180">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="6f690-180">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="6f690-181">Um [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop é usado para renderizar cada instância de previsão como uma linha na tabela de dados meteorológicos:</span><span class="sxs-lookup"><span data-stu-id="6f690-181">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="6f690-182">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6f690-182">Build a todo list</span></span>

<span data-ttu-id="6f690-183">Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="6f690-183">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="6f690-184">Adicione um novo `Todo` Razor componente ao aplicativo na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="6f690-184">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="6f690-185">Se você estiver usando o Visual Studio, clique com o botão direito do mouse na pasta **páginas** e selecione **Adicionar**  >  **novo item**  >  \*\* Razor componente\*\*.</span><span class="sxs-lookup"><span data-stu-id="6f690-185">If you're using Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="6f690-186">Nomeie o arquivo do componente *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="6f690-186">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="6f690-187">Em outros ambientes de desenvolvimento, adicione um arquivo em branco à pasta **páginas** chamada *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="6f690-187">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="6f690-188">Forneça a marcação inicial do componente:</span><span class="sxs-lookup"><span data-stu-id="6f690-188">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="6f690-189">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="6f690-189">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="6f690-190">O componente `NavMenu` (*Shared/NavMenu.razor*) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-190">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="6f690-191">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-191">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="6f690-192">Adicione um elemento `<NavLink>` para o componente `Todo` adicionando a seguinte marcação de item da lista abaixo dos itens existentes no arquivo *Shared/NavMenu.razor*:</span><span class="sxs-lookup"><span data-stu-id="6f690-192">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="6f690-193">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-193">Rebuild and run the app.</span></span> <span data-ttu-id="6f690-194">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.</span><span class="sxs-lookup"><span data-stu-id="6f690-194">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="6f690-195">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="6f690-195">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="6f690-196">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6f690-196">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="6f690-197">Retorne para o componente `Todo` (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="6f690-197">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="6f690-198">Adicione um campo para os itens de tarefas pendentes em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="6f690-198">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="6f690-199">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="6f690-199">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="6f690-200">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="6f690-200">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="6f690-201">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="6f690-201">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="6f690-202">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="6f690-202">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="6f690-203">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-203">Rebuild and run the app.</span></span> <span data-ttu-id="6f690-204">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="6f690-204">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="6f690-205">Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="6f690-205">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="6f690-206">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="6f690-206">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="6f690-207">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="6f690-207">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="6f690-208">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="6f690-208">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="6f690-209">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="6f690-209">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="6f690-210">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-210">Rebuild and run the app.</span></span> <span data-ttu-id="6f690-211">Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="6f690-211">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="6f690-212">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="6f690-212">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="6f690-213">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="6f690-213">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="6f690-214">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="6f690-214">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="6f690-215">Para verificar se esses valores estão associados, atualize o cabeçalho `<h3>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="6f690-215">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="6f690-216">O componente `Todo` concluído (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="6f690-216">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="6f690-217">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f690-217">Rebuild and run the app.</span></span> <span data-ttu-id="6f690-218">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="6f690-218">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6f690-219">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="6f690-219">Next steps</span></span>

<span data-ttu-id="6f690-220">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="6f690-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f690-221">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="6f690-221">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="6f690-222">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="6f690-222">Modify Razor components</span></span>
> * <span data-ttu-id="6f690-223">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="6f690-223">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="6f690-224">Usar injeção de dependência (DI) e roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="6f690-224">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="6f690-225">Saiba como criar e usar componentes:</span><span class="sxs-lookup"><span data-stu-id="6f690-225">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
