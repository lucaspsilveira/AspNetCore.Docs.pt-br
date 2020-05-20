---
<span data-ttu-id="e9ed6-101">Título: ' Introdução ao ASP.NET Core Blazor ' autor: Descrição: ' explorar ASP.NET Core Blazor , uma maneira de criar interface do usuário da Web do lado do cliente interativa com .net em um aplicativo ASP.NET Core. '</span><span class="sxs-lookup"><span data-stu-id="e9ed6-101">title: 'Introduction to ASP.NET Core Blazor' author: description: 'Explore ASP.NET Core Blazor, a way to build interactive client-side web UI with .NET in an ASP.NET Core app.'</span></span>
<span data-ttu-id="e9ed6-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9ed6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9ed6-103">'Blazor'</span></span>
- <span data-ttu-id="e9ed6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9ed6-104">'Identity'</span></span>
- <span data-ttu-id="e9ed6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9ed6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9ed6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9ed6-106">'Razor'</span></span>
- <span data-ttu-id="e9ed6-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="e9ed6-107">'SignalR' uid:</span></span> 

---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="e9ed6-108">Introdução ao ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="e9ed6-108">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="e9ed6-109">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e9ed6-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e9ed6-110">*Bem-vindo ao Blazor !*</span><span class="sxs-lookup"><span data-stu-id="e9ed6-110">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="e9ed6-111">é uma estrutura para a criação de interface do usuário da Web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-111"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="e9ed6-112">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-112">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="e9ed6-113">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-113">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="e9ed6-114">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-114">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="e9ed6-115">Integre-se com plataformas de hospedagem modernas, como o [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-115">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="e9ed6-116">Usar o .NET para desenvolvimento web do lado do cliente oferece as seguintes vantagens:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-116">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="e9ed6-117">escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-117">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="e9ed6-118">Aproveite o ecossistema .NET existente das bibliotecas .NET.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-118">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="e9ed6-119">Compartilhe a lógica de aplicativo entre o servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-119">Share app logic across server and client.</span></span>
* <span data-ttu-id="e9ed6-120">Beneficie-se com o desempenho, confiabilidade e segurança do .NET.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-120">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="e9ed6-121">mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-121">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="e9ed6-122">Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-122">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="e9ed6-123">Componentes</span><span class="sxs-lookup"><span data-stu-id="e9ed6-123">Components</span></span>

Blazor<span data-ttu-id="e9ed6-124">os aplicativos são baseados em *componentes*.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-124"> apps are based on *components*.</span></span> <span data-ttu-id="e9ed6-125">Um componente no Blazor é um elemento da interface do usuário, como um formulário de página, caixa de diálogo ou entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-125">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="e9ed6-126">Os componentes são classes do .NET incorporadas a assemblies .NET que:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-126">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="e9ed6-127">Definem a lógica de renderização da interface de usuário flexível.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-127">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="e9ed6-128">Tratam eventos do usuário.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-128">Handle user events.</span></span>
* <span data-ttu-id="e9ed6-129">Podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-129">Can be nested and reused.</span></span>
* <span data-ttu-id="e9ed6-130">Pode ser compartilhado e distribuído como [ Razor bibliotecas de classes](xref:razor-pages/ui-class) ou [pacotes NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-130">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="e9ed6-131">A classe de componente geralmente é escrita na forma de uma [Razor](xref:mvc/views/razor) página de marcação com uma extensão de arquivo *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="e9ed6-131">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="e9ed6-132">Os componentes no Blazor são formalmente chamados de \* Razor componentes\*.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-132">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="e9ed6-133">é uma sintaxe para combinar marcação HTML com código C# projetado para a produtividade do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-133"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="e9ed6-134">permite que você alterne entre marcação HTML e C# no mesmo arquivo com suporte [IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="e9ed6-134"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="e9ed6-135">As páginas e MVC também usam o Razor .</span><span class="sxs-lookup"><span data-stu-id="e9ed6-135"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="e9ed6-136">Ao contrário de Razor páginas e MVC, que são criadas em um modelo de solicitação/resposta, os componentes são usados especificamente para a lógica e a composição da interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-136">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="e9ed6-137">A marcação a seguir Razor demonstra um componente (*Dialog. Razor*), que pode ser aninhado dentro de outro componente:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-137">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="e9ed6-138">O conteúdo do corpo da caixa de diálogo (`ChildContent`) e o título (`Title`) são fornecidos pelo componente que usa esse componente em sua interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-138">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="e9ed6-139">`OnYes` é um método C# disparado pelo evento `onclick` do botão.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-139">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="e9ed6-140">usa marcas HTML naturais para composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-140"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="e9ed6-141">Os elementos HTML especificam componentes e os atributos da marcação transmitem valores para as propriedades de um componente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-141">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="e9ed6-142">No exemplo a seguir, o componente `Index` usa o componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-142">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="e9ed6-143">`ChildContent` e `Title` são definidos pelos atributos e pelo conteúdo do elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-143">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="e9ed6-144">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-144">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="e9ed6-145">A caixa de diálogo é renderizada quando o pai (*Index.razor*) é acessado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-145">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Componente da caixa de diálogo renderizada no navegador](index/_static/dialog.png)

<span data-ttu-id="e9ed6-147">Quando esse componente é usado no aplicativo, o IntelliSense no [Visual Studio](/visualstudio/ide/using-intellisense) e no [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-147">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="e9ed6-148">Os componentes são renderizados em uma representação na memória do Modelo de Objeto do Documento (DOM) do navegador chamada *árvore de renderização*, que é usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-148">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="e9ed6-149">Webassembly</span><span class="sxs-lookup"><span data-stu-id="e9ed6-149"> WebAssembly</span></span>

Blazor<span data-ttu-id="e9ed6-150">O Webassembly é uma estrutura de aplicativo de página única para a criação de aplicativos Web do lado do cliente interativos com o .NET.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-150"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="e9ed6-151">O Webassembly usa padrões abertos da Web sem plug-ins ou códigos transpilação e trabalha em todos os navegadores da Web modernos, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-151"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="e9ed6-152">A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](https://webassembly.org) (abreviado como *wasm*).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-152">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="e9ed6-153">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-153">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="e9ed6-154">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-154">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="e9ed6-155">O código WebAssembly pode acessar a funcionalidade completa do navegador por meio de JavaScript, chamado *Interoperabilidade do JavaScript* (ou *Interop do JavaScript*).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-155">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="e9ed6-156">O código .NET executado por meio da WebAssembly no navegador é executado na área restrita do JavaScript do navegador com as proteções que a área restrita oferece contra ações mal intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-156">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="e9ed6-157">![BlazorO Webassembly executa o código .NET no navegador com Webassembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="e9ed6-157">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="e9ed6-158">Quando um Blazor aplicativo Webassembly é compilado e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-158">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="e9ed6-159">Arquivos de código C# e Razor arquivos são compilados em assemblies .net.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-159">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="e9ed6-160">os assemblies e o runtime do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-160">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="e9ed6-161">O Webassembly Inicializa o tempo de execução do .NET e configura o tempo de execução para carregar os assemblies para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-161"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="e9ed6-162">O Blazor tempo de execução do Webassembly usa a interoperabilidade JavaScript para manipular as chamadas de API do navegador e a manipulação do dom</span><span class="sxs-lookup"><span data-stu-id="e9ed6-162">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="e9ed6-163">O tamanho do aplicativo publicado, seu *tamanho de payload*, é um fator de desempenho crítico para a utilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-163">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="e9ed6-164">Um aplicativo grande leva um tempo relativamente longo para baixar para um navegador, o que afeta a experiência do usuário.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-164">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="e9ed6-165">O Webassembly otimiza o tamanho da carga para reduzir os tempos de download:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-165"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="e9ed6-166">O código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-166">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="e9ed6-167">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-167">HTTP responses are compressed.</span></span>
* <span data-ttu-id="e9ed6-168">O runtime do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-168">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="e9ed6-169">Servidor</span><span class="sxs-lookup"><span data-stu-id="e9ed6-169"> Server</span></span>

Blazor<span data-ttu-id="e9ed6-170">dissocia a lógica de renderização do componente de como as atualizações da interface do usuário são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-170"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="e9ed6-171">O servidor fornece suporte para hospedar Razor componentes no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-171"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="e9ed6-172">As atualizações da interface do usuário são manipuladas em uma [SignalR](xref:signalr/introduction) conexão.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="e9ed6-173">O runtime realiza o envio de eventos da interface do usuário do navegador para o servidor e executar os componentes, aplica as atualizações na interface do usuário retornadas pelo servidor ao navegador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-173">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="e9ed6-174">A conexão usada pelo Blazor servidor para se comunicar com o navegador também é usada para lidar com as chamadas de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-174">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="e9ed6-175">![BlazorO servidor executa o código .NET no servidor e interage com o Modelo de Objeto do Documento no cliente por meio de uma SignalR conexão](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="e9ed6-175">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="e9ed6-176">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="e9ed6-176">JavaScript interop</span></span>

<span data-ttu-id="e9ed6-177">Para aplicativos que exigem bibliotecas JavaScript e acesso a APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-177">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="e9ed6-178">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-178">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="e9ed6-179">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-179">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="e9ed6-180">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-180">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="e9ed6-181">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="e9ed6-181">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="e9ed6-182">implementa [.NET Standard 2,0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-182"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="e9ed6-183">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-183">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="e9ed6-184">.NET Standard bibliotecas de classe podem ser compartilhadas entre diferentes plataformas .NET, como Blazor , .NET Framework, .NET Core, Xamarin, mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-184">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="e9ed6-185">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete e threading) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-185">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9ed6-186">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e9ed6-186">Additional resources</span></span>

* [<span data-ttu-id="e9ed6-187">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e9ed6-187">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="e9ed6-188">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="e9ed6-188">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="e9ed6-189">HTML</span><span class="sxs-lookup"><span data-stu-id="e9ed6-189">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="e9ed6-190">[Incrível Blazor ](https://github.com/AdrienTorris/awesome-blazor) links da Comunidade</span><span class="sxs-lookup"><span data-stu-id="e9ed6-190">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
