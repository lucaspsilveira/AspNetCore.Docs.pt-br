---
title: Introdução ao ASP.NET CoreBlazor
author: guardrex
description: Explore ASP.NET Core Blazor , uma maneira de criar interface do usuário da Web interativa do lado do cliente com o .net em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: 30f11a137e711b1cf7a8b036af92fbb5fa2a1f05
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402566"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="24e1b-103">Introdução ao ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="24e1b-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="24e1b-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="24e1b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="24e1b-105">*Bem-vindo ao Blazor !*</span><span class="sxs-lookup"><span data-stu-id="24e1b-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="24e1b-106">é uma estrutura para a criação de interface do usuário da Web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="24e1b-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="24e1b-107">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="24e1b-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="24e1b-108">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="24e1b-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="24e1b-109">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="24e1b-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="24e1b-110">Integre-se com plataformas de hospedagem modernas, como o [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="24e1b-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="24e1b-111">Usar o .NET para desenvolvimento web do lado do cliente oferece as seguintes vantagens:</span><span class="sxs-lookup"><span data-stu-id="24e1b-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="24e1b-112">escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="24e1b-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="24e1b-113">Aproveite o ecossistema .NET existente das bibliotecas .NET.</span><span class="sxs-lookup"><span data-stu-id="24e1b-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="24e1b-114">Compartilhe a lógica de aplicativo entre o servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="24e1b-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="24e1b-115">Beneficie-se com o desempenho, confiabilidade e segurança do .NET.</span><span class="sxs-lookup"><span data-stu-id="24e1b-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="24e1b-116">mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="24e1b-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="24e1b-117">Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="24e1b-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="24e1b-118">Componentes</span><span class="sxs-lookup"><span data-stu-id="24e1b-118">Components</span></span>

Blazor<span data-ttu-id="24e1b-119">os aplicativos são baseados em *componentes*.</span><span class="sxs-lookup"><span data-stu-id="24e1b-119"> apps are based on *components*.</span></span> <span data-ttu-id="24e1b-120">Um componente no Blazor é um elemento da interface do usuário, como um formulário de página, caixa de diálogo ou entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="24e1b-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="24e1b-121">Os componentes são classes do .NET incorporadas a assemblies .NET que:</span><span class="sxs-lookup"><span data-stu-id="24e1b-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="24e1b-122">Definem a lógica de renderização da interface de usuário flexível.</span><span class="sxs-lookup"><span data-stu-id="24e1b-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="24e1b-123">Tratam eventos do usuário.</span><span class="sxs-lookup"><span data-stu-id="24e1b-123">Handle user events.</span></span>
* <span data-ttu-id="24e1b-124">Podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="24e1b-124">Can be nested and reused.</span></span>
* <span data-ttu-id="24e1b-125">Pode ser compartilhado e distribuído como [ Razor bibliotecas de classes](xref:razor-pages/ui-class) ou [pacotes NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="24e1b-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="24e1b-126">A classe de componente geralmente é escrita na forma de uma [Razor](xref:mvc/views/razor) página de marcação com uma `.razor` extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="24e1b-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="24e1b-127">Os componentes no Blazor são formalmente chamados de \* Razor componentes\*.</span><span class="sxs-lookup"><span data-stu-id="24e1b-127">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="24e1b-128">é uma sintaxe para combinar marcação HTML com código C# projetado para a produtividade do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="24e1b-128"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="24e1b-129">permite que você alterne entre marcação HTML e C# no mesmo arquivo com suporte [IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="24e1b-129"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="24e1b-130">As páginas e MVC também usam o Razor .</span><span class="sxs-lookup"><span data-stu-id="24e1b-130"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="24e1b-131">Ao contrário de Razor páginas e MVC, que são criadas em um modelo de solicitação/resposta, os componentes são usados especificamente para a lógica e a composição da interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="24e1b-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="24e1b-132">A marcação a seguir Razor demonstra um componente ( `Dialog.razor` ), que pode ser aninhado dentro de outro componente:</span><span class="sxs-lookup"><span data-stu-id="24e1b-132">The following Razor markup demonstrates a component (`Dialog.razor`), which can be nested within another component:</span></span>

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

<span data-ttu-id="24e1b-133">O conteúdo do corpo da caixa de diálogo (`ChildContent`) e o título (`Title`) são fornecidos pelo componente que usa esse componente em sua interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="24e1b-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="24e1b-134">`OnYes` é um método C# disparado pelo evento `onclick` do botão.</span><span class="sxs-lookup"><span data-stu-id="24e1b-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="24e1b-135">usa marcas HTML naturais para composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="24e1b-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="24e1b-136">Os elementos HTML especificam componentes e os atributos da marcação transmitem valores para as propriedades de um componente.</span><span class="sxs-lookup"><span data-stu-id="24e1b-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="24e1b-137">No exemplo a seguir, o componente `Index` usa o componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="24e1b-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="24e1b-138">`ChildContent` e `Title` são definidos pelos atributos e pelo conteúdo do elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="24e1b-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="24e1b-139">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="24e1b-139">`Pages/Index.razor`:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="24e1b-140">A caixa de diálogo é renderizada quando o pai ( `Pages/Index.razor` ) é acessado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="24e1b-140">The dialog is rendered when the parent (`Pages/Index.razor`) is accessed in a browser:</span></span>

![Componente da caixa de diálogo renderizada no navegador](index/_static/dialog.png)

<span data-ttu-id="24e1b-142">Quando esse componente é usado no aplicativo, o IntelliSense no [Visual Studio](/visualstudio/ide/using-intellisense) e no [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="24e1b-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="24e1b-143">Os componentes são renderizados em uma representação na memória do Modelo de Objeto do Documento (DOM) do navegador chamada *árvore de renderização*, que é usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="24e1b-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

Blazor WebAssembly<span data-ttu-id="24e1b-144">é uma estrutura de aplicativo de página única para criar aplicativos Web do lado do cliente interativos com o .NET.</span><span class="sxs-lookup"><span data-stu-id="24e1b-144"> is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor WebAssembly<span data-ttu-id="24e1b-145">usa padrões abertos da Web sem plug-ins ou código transpilação e funciona em todos os navegadores da Web modernos, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="24e1b-145"> uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="24e1b-146">A execução de código .NET dentro de navegadores da Web é possibilitada pelo [Webassembly](https://webassembly.org) (abreviado `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="24e1b-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="24e1b-147">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="24e1b-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="24e1b-148">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="24e1b-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="24e1b-149">O código WebAssembly pode acessar a funcionalidade completa do navegador por meio de JavaScript, chamado *Interoperabilidade do JavaScript* (ou *Interop do JavaScript*).</span><span class="sxs-lookup"><span data-stu-id="24e1b-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="24e1b-150">O código .NET executado por meio da WebAssembly no navegador é executado na área restrita do JavaScript do navegador com as proteções que a área restrita oferece contra ações mal intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="24e1b-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="24e1b-151">![Blazor WebAssemblyexecuta o código .NET no navegador com Webassembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="24e1b-151">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="24e1b-152">Quando um Blazor WebAssembly aplicativo é compilado e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="24e1b-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="24e1b-153">Arquivos de código C# e Razor arquivos são compilados em assemblies .net.</span><span class="sxs-lookup"><span data-stu-id="24e1b-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="24e1b-154">os assemblies e o runtime do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="24e1b-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor WebAssembly<span data-ttu-id="24e1b-155">Inicializa o tempo de execução do .NET e configura o tempo de execução para carregar os assemblies para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24e1b-155"> bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="24e1b-156">O Blazor WebAssembly tempo de execução usa a interoperabilidade JavaScript para lidar com a manipulação de Dom e chamadas de API de navegador</span><span class="sxs-lookup"><span data-stu-id="24e1b-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="24e1b-157">O tamanho do aplicativo publicado, seu *tamanho de payload*, é um fator de desempenho crítico para a utilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24e1b-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="24e1b-158">Um aplicativo grande leva um tempo relativamente longo para baixar para um navegador, o que afeta a experiência do usuário.</span><span class="sxs-lookup"><span data-stu-id="24e1b-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor WebAssembly<span data-ttu-id="24e1b-159">otimiza o tamanho da carga para reduzir os tempos de download:</span><span class="sxs-lookup"><span data-stu-id="24e1b-159"> optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="24e1b-160">O código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="24e1b-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="24e1b-161">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="24e1b-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="24e1b-162">O runtime do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="24e1b-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## Blazor Server

Blazor<span data-ttu-id="24e1b-163">dissocia a lógica de renderização do componente de como as atualizações da interface do usuário são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="24e1b-163"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor Server<span data-ttu-id="24e1b-164">fornece suporte para hospedar Razor componentes no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24e1b-164"> provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="24e1b-165">As atualizações da interface do usuário são manipuladas em uma [SignalR](xref:signalr/introduction) conexão.</span><span class="sxs-lookup"><span data-stu-id="24e1b-165">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="24e1b-166">O runtime realiza o envio de eventos da interface do usuário do navegador para o servidor e executar os componentes, aplica as atualizações na interface do usuário retornadas pelo servidor ao navegador.</span><span class="sxs-lookup"><span data-stu-id="24e1b-166">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="24e1b-167">A conexão usada pelo Blazor Server para se comunicar com o navegador também é usada para lidar com as chamadas de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="24e1b-167">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="24e1b-168">![Blazor Serverexecuta o código .NET no servidor e interage com o Modelo de Objeto do Documento no cliente por uma SignalR conexão](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="24e1b-168">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="24e1b-169">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="24e1b-169">JavaScript interop</span></span>

<span data-ttu-id="24e1b-170">Para aplicativos que exigem bibliotecas JavaScript e acesso a APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="24e1b-170">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="24e1b-171">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="24e1b-171">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="24e1b-172">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="24e1b-172">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="24e1b-173">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="24e1b-173">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="24e1b-174">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="24e1b-174">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="24e1b-175">implementa [.NET Standard 2,1](/dotnet/standard/net-standard), que permite que os Blazor projetos referenciem bibliotecas que estão em conformidade com as especificações .net Standard 2,1 ou anteriores.</span><span class="sxs-lookup"><span data-stu-id="24e1b-175"> implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="24e1b-176">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="24e1b-176">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="24e1b-177">.NET Standard bibliotecas de classe podem ser compartilhadas entre diferentes plataformas .NET, como Blazor , .NET Framework, .NET Core, Xamarin, mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="24e1b-177">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="24e1b-178">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete e threading) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="24e1b-178">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24e1b-179">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="24e1b-179">Additional resources</span></span>

* [<span data-ttu-id="24e1b-180">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="24e1b-180">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="24e1b-181">Guia do C#</span><span class="sxs-lookup"><span data-stu-id="24e1b-181">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="24e1b-182">HTML</span><span class="sxs-lookup"><span data-stu-id="24e1b-182">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="24e1b-183">[Incrível Blazor ](https://github.com/AdrienTorris/awesome-blazor) links da Comunidade</span><span class="sxs-lookup"><span data-stu-id="24e1b-183">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
