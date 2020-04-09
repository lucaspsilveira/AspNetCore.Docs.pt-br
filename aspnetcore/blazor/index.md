---
title: Introdução ao Núcleo ASP.NETBlazor
author: guardrex
description: Explore ASP.NET BlazorCore , uma maneira de construir uma u ida web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405948"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="9f959-103">Introdução ao Núcleo ASP.NETBlazor</span><span class="sxs-lookup"><span data-stu-id="9f959-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="9f959-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9f959-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9f959-105">*Bem-vindos a! Blazor*</span><span class="sxs-lookup"><span data-stu-id="9f959-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="9f959-106">é uma estrutura para construir uma ui web interativa do lado do cliente com .NET:</span><span class="sxs-lookup"><span data-stu-id="9f959-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="9f959-107">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9f959-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="9f959-108">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="9f959-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="9f959-109">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="9f959-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="9f959-110">Integre-se a plataformas de hospedagem modernas, como [a Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="9f959-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="9f959-111">Usar o .NET para desenvolvimento web do lado do cliente oferece as seguintes vantagens:</span><span class="sxs-lookup"><span data-stu-id="9f959-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="9f959-112">escreva o código em C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9f959-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="9f959-113">Aproveite o ecossistema .NET existente das bibliotecas .NET.</span><span class="sxs-lookup"><span data-stu-id="9f959-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="9f959-114">Compartilhe a lógica de aplicativo entre o servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="9f959-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="9f959-115">Beneficie-se com o desempenho, confiabilidade e segurança do .NET.</span><span class="sxs-lookup"><span data-stu-id="9f959-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="9f959-116">mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="9f959-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="9f959-117">Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.</span><span class="sxs-lookup"><span data-stu-id="9f959-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="9f959-118">Componentes</span><span class="sxs-lookup"><span data-stu-id="9f959-118">Components</span></span>

Blazor<span data-ttu-id="9f959-119">aplicativos são *baseados*em componentes .</span><span class="sxs-lookup"><span data-stu-id="9f959-119"> apps are based on *components*.</span></span> <span data-ttu-id="9f959-120">Um componente Blazor em é um elemento da ui, como uma página, diálogo ou formulário de entrada de dados.</span><span class="sxs-lookup"><span data-stu-id="9f959-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="9f959-121">Os componentes são classes do .NET incorporadas a assemblies .NET que:</span><span class="sxs-lookup"><span data-stu-id="9f959-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="9f959-122">Definem a lógica de renderização da interface de usuário flexível.</span><span class="sxs-lookup"><span data-stu-id="9f959-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="9f959-123">Tratam eventos do usuário.</span><span class="sxs-lookup"><span data-stu-id="9f959-123">Handle user events.</span></span>
* <span data-ttu-id="9f959-124">Podem ser aninhados e reutilizados.</span><span class="sxs-lookup"><span data-stu-id="9f959-124">Can be nested and reused.</span></span>
* <span data-ttu-id="9f959-125">Podem ser compartilhados e distribuído como [bibliotecas de classes Razor](xref:razor-pages/ui-class) ou [pacotes do NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="9f959-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="9f959-126">A classe de componentes é geralmente escrita na forma de uma página de marcação [Razor](xref:mvc/views/razor) com uma extensão de arquivo *.razor.*</span><span class="sxs-lookup"><span data-stu-id="9f959-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="9f959-127">Os componentes em Blazor são formalmente referidos como *componentes de barbear*.</span><span class="sxs-lookup"><span data-stu-id="9f959-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="9f959-128">O Razor é uma sintaxe para a combinação de marcação HTML com o código C# projetada para melhorar a produtividade do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="9f959-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="9f959-129">O Razor permite que você alterne entre a marcação HTML e C# no mesmo arquivo com suporte [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="9f959-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="9f959-130">As Razor Pages e MVC também usam o Razor.</span><span class="sxs-lookup"><span data-stu-id="9f959-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="9f959-131">Ao contrário das Razor Pages e das MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para composição e lógica da interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="9f959-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="9f959-132">A seguinte marcação Razor demonstra um componente (*Dialog.razor*), que pode ser aninhado dentro de outro componente:</span><span class="sxs-lookup"><span data-stu-id="9f959-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="9f959-133">O conteúdo do corpo da caixa de diálogo (`ChildContent`) e o título (`Title`) são fornecidos pelo componente que usa esse componente em sua interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="9f959-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="9f959-134">`OnYes` é um método C# disparado pelo evento `onclick` do botão.</span><span class="sxs-lookup"><span data-stu-id="9f959-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="9f959-135">usa tags HTML naturais para composição de IA.</span><span class="sxs-lookup"><span data-stu-id="9f959-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="9f959-136">Os elementos HTML especificam componentes e os atributos da marcação transmitem valores para as propriedades de um componente.</span><span class="sxs-lookup"><span data-stu-id="9f959-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="9f959-137">No exemplo a seguir, o componente `Index` usa o componente `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="9f959-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="9f959-138">`ChildContent` e `Title` são definidos pelos atributos e pelo conteúdo do elemento `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="9f959-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="9f959-139">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="9f959-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="9f959-140">A caixa de diálogo é renderizada quando o pai (*Index.razor*) é acessado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="9f959-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Componente da caixa de diálogo renderizada no navegador](index/_static/dialog.png)

<span data-ttu-id="9f959-142">Quando esse componente é usado no aplicativo, o IntelliSense no [Visual Studio](/visualstudio/ide/using-intellisense) e no [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9f959-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="9f959-143">Os componentes são renderizados em uma representação na memória do Modelo de Objeto do Documento (DOM) do navegador chamada *árvore de renderização*, que é usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="9f959-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="9f959-144">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9f959-144"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="9f959-145">WebAssembly é uma estrutura de aplicativo de uma página única para criar aplicativos web interativos do lado do cliente com .NET.</span><span class="sxs-lookup"><span data-stu-id="9f959-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="9f959-146">O WebAssembly usa padrões web abertos sem plugins ou transpilação de código e funciona em todos os navegadores da Web modernos, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="9f959-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="9f959-147">A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](https://webassembly.org) (abreviado como *wasm*).</span><span class="sxs-lookup"><span data-stu-id="9f959-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="9f959-148">O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.</span><span class="sxs-lookup"><span data-stu-id="9f959-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="9f959-149">O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.</span><span class="sxs-lookup"><span data-stu-id="9f959-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="9f959-150">O código WebAssembly pode acessar a funcionalidade completa do navegador por meio de JavaScript, chamado *Interoperabilidade do JavaScript* (ou *Interop do JavaScript*).</span><span class="sxs-lookup"><span data-stu-id="9f959-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="9f959-151">O código .NET executado por meio da WebAssembly no navegador é executado na área restrita do JavaScript do navegador com as proteções que a área restrita oferece contra ações mal intencionadas no computador cliente.</span><span class="sxs-lookup"><span data-stu-id="9f959-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="9f959-152">![BlazorO WebAssembly executa o código .NET no navegador com o WebAssembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="9f959-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="9f959-153">Quando Blazor um aplicativo WebAssembly é construído e executado em um navegador:</span><span class="sxs-lookup"><span data-stu-id="9f959-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="9f959-154">os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.</span><span class="sxs-lookup"><span data-stu-id="9f959-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="9f959-155">os assemblies e o runtime do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="9f959-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="9f959-156">O WebAssembly aproveita o tempo de execução do .NET e configura o tempo de execução para carregar os conjuntos para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f959-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="9f959-157">O Blazor tempo de execução do WebAssembly usa o Interop JavaScript para lidar com a manipulação do DOM e as chamadas de API do navegador.</span><span class="sxs-lookup"><span data-stu-id="9f959-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="9f959-158">O tamanho do aplicativo publicado, seu *tamanho de payload*, é um fator de desempenho crítico para a utilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f959-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="9f959-159">Um aplicativo grande leva um tempo relativamente longo para baixar para um navegador, o que afeta a experiência do usuário.</span><span class="sxs-lookup"><span data-stu-id="9f959-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="9f959-160">O WebAssembly otimiza o tamanho da carga útil para reduzir os tempos de download:</span><span class="sxs-lookup"><span data-stu-id="9f959-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="9f959-161">O código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="9f959-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="9f959-162">As respostas HTTP são compactadas.</span><span class="sxs-lookup"><span data-stu-id="9f959-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="9f959-163">O runtime do .NET e os assemblies são armazenados em cache no navegador.</span><span class="sxs-lookup"><span data-stu-id="9f959-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="9f959-164">Servidor</span><span class="sxs-lookup"><span data-stu-id="9f959-164"> Server</span></span>

Blazor<span data-ttu-id="9f959-165">desacoplar a lógica de renderização de componentes de como as atualizações de ida e futura são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="9f959-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="9f959-166">O servidor oferece suporte para hospedar componentes Razor no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f959-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="9f959-167">As atualizações de iu [SignalR](xref:signalr/introduction) de ui são tratadas por uma conexão.</span><span class="sxs-lookup"><span data-stu-id="9f959-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="9f959-168">O runtime realiza o envio de eventos da interface do usuário do navegador para o servidor e executar os componentes, aplica as atualizações na interface do usuário retornadas pelo servidor ao navegador.</span><span class="sxs-lookup"><span data-stu-id="9f959-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="9f959-169">A conexão Blazor usada pelo Server para se comunicar com o navegador também é usada para lidar com chamadas interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9f959-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="9f959-170">![BlazorO servidor executa o código .NET no servidor e interage SignalR com o Modelo de Objeto de Documento no cliente por uma conexão](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="9f959-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="9f959-171">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f959-171">JavaScript interop</span></span>

<span data-ttu-id="9f959-172">Para aplicativos que exigem bibliotecas JavaScript e acesso a APIs do navegador de terceiros, os componentes interoperam com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9f959-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="9f959-173">Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar.</span><span class="sxs-lookup"><span data-stu-id="9f959-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="9f959-174">O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#.</span><span class="sxs-lookup"><span data-stu-id="9f959-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="9f959-175">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="9f959-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="9f959-176">Compartilhamento de código e o .NET Standard</span><span class="sxs-lookup"><span data-stu-id="9f959-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="9f959-177">implementos [.NET Padrão 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="9f959-177"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="9f959-178">O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET.</span><span class="sxs-lookup"><span data-stu-id="9f959-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="9f959-179">As bibliotecas de classe .NET podem ser compartilhadas Blazorem diferentes plataformas .NET, tais como , .NET Framework, .NET Core, Xamarin, Mono e Unity.</span><span class="sxs-lookup"><span data-stu-id="9f959-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="9f959-180">As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete e threading) geram a <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="9f959-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f959-181">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9f959-181">Additional resources</span></span>

* [<span data-ttu-id="9f959-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9f959-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="9f959-183">Guia C#</span><span class="sxs-lookup"><span data-stu-id="9f959-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="9f959-184">Html</span><span class="sxs-lookup"><span data-stu-id="9f959-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="9f959-185">[Links Blazor incríveis](https://github.com/AdrienTorris/awesome-blazor) da comunidade</span><span class="sxs-lookup"><span data-stu-id="9f959-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
