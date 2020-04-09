---
title: Link do navegador no núcleo ASP.NET
author: ncarandini
description: Explica como o Browser Link é um recurso do Visual Studio que conecta o ambiente de desenvolvimento com um ou mais navegadores da Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658847"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="b1ff9-103">Link do navegador no núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="b1ff9-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="b1ff9-104">Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b1ff9-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b1ff9-105">Browser Link é um recurso do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="b1ff9-106">Ele cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da Web.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="b1ff9-107">Você pode usar o Browser Link para atualizar seu aplicativo web em vários navegadores ao mesmo tempo, o que é útil para testes entre navegadores.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="b1ff9-108">Configuração do link do navegador</span><span class="sxs-lookup"><span data-stu-id="b1ff9-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1ff9-109">Adicione o pacote [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="b1ff9-110">Para ASP.NET páginas de navalha principais ou projetos MVC, também habilite <xref:mvc/views/view-compilation>a compilação em tempo de execução de arquivos Razor (*.cshtml)* conforme descrito em .</span><span class="sxs-lookup"><span data-stu-id="b1ff9-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="b1ff9-111">As alterações de sintaxe de navalha são aplicadas somente quando a compilação em tempo de execução foi ativada.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="b1ff9-112">Ao converter um projeto ASP.NET Core 2.0 para ASP.NET Core 2.1 e fazer a transição para o [metapacote Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)instale o pacote [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para a funcionalidade do Browser Link.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="b1ff9-113">Os modelos de projeto ASP.NET `Microsoft.AspNetCore.App` Core 2.1 usam o metapacote por padrão.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b1ff9-114">Os modelos de projeto de ASP.NET Core 2.0 **Web Application,** **Empty**e **Web API** usam o [metapacote Microsoft.AspNetCore.All,](xref:fundamentals/metapackage)que contém uma referência de pacote para [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="b1ff9-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="b1ff9-115">Portanto, o `Microsoft.AspNetCore.All` uso do metapacote não requer mais nenhuma ação para disponibilizar o Browser Link para uso.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="b1ff9-116">O modelo de projeto do ASP.NET Core 1.x **Do Aplicativo da Web** tem uma referência de pacote para o pacote [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)</span><span class="sxs-lookup"><span data-stu-id="b1ff9-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="b1ff9-117">Outros tipos de projeto exigem que `Microsoft.VisualStudio.Web.BrowserLink`você adicione uma referência de pacote a .</span><span class="sxs-lookup"><span data-stu-id="b1ff9-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="b1ff9-118">Configuração</span><span class="sxs-lookup"><span data-stu-id="b1ff9-118">Configuration</span></span>

<span data-ttu-id="b1ff9-119">Chame `UseBrowserLink` no método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="b1ff9-120">A `UseBrowserLink` chamada é normalmente `if` colocada dentro de um bloco que só habilita o Link do Navegador no ambiente Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="b1ff9-121">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="b1ff9-122">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="b1ff9-123">Como usar o Link do Navegador</span><span class="sxs-lookup"><span data-stu-id="b1ff9-123">How to use Browser Link</span></span>

<span data-ttu-id="b1ff9-124">Quando você tem um projeto ASP.NET Core aberto, o Visual Studio mostra o controle da barra de ferramentas do Browser Link ao lado do controle da barra de ferramentas **Debug Target:**</span><span class="sxs-lookup"><span data-stu-id="b1ff9-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu suspenso do Link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="b1ff9-126">A partir do controle da barra de ferramentas do Browser Link, você pode:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="b1ff9-127">Atualize o aplicativo web em vários navegadores ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="b1ff9-128">Abra o **painel de link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="b1ff9-129">Habilitar ou desativar **o Link do Navegador**.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="b1ff9-130">Nota: O Link do Navegador é desativado por padrão no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="b1ff9-131">Ativar ou desativar [o CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="b1ff9-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="b1ff9-132">Atualize o aplicativo web em vários navegadores ao mesmo tempo</span><span class="sxs-lookup"><span data-stu-id="b1ff9-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="b1ff9-133">Para escolher um único navegador da Web para iniciar ao iniciar o projeto, use o menu suspenso no controle da barra de ferramentas **Debug Target:**</span><span class="sxs-lookup"><span data-stu-id="b1ff9-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu suspenso F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="b1ff9-135">Para abrir vários navegadores ao mesmo tempo, escolha **Procurar com...** do mesmo drop-down.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="b1ff9-136">Mantenha a tecla <kbd>Ctrl</kbd> para selecionar os navegadores desejados e clique **em Procurar**:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Abra muitos navegadores ao mesmo tempo](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="b1ff9-138">A captura de tela a seguir mostra o Visual Studio com a exibição Index aberta e dois navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Sincronizar com dois navegadores exemplo](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="b1ff9-140">Passar o passar o tempo no controle da barra de ferramentas do Browser Link para ver os navegadores conectados ao projeto:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Ponta de hover](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="b1ff9-142">Alterar a exibição Índice e todos os navegadores conectados são atualizados quando você clica no botão de atualização do Link do navegador:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navegadores-sincronização para alterações](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="b1ff9-144">O Browser Link também funciona com navegadores que você inicia de fora do Visual Studio e navega até a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="b1ff9-145">O painel de link do navegador</span><span class="sxs-lookup"><span data-stu-id="b1ff9-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="b1ff9-146">Abra a janela **browser link dashboard** a partir do menu suspenso do Link do navegador para gerenciar a conexão com navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="b1ff9-148">Se nenhum navegador estiver conectado, você pode iniciar uma sessão de não depuração selecionando o link **Exibir no navegador:**</span><span class="sxs-lookup"><span data-stu-id="b1ff9-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![navegadorlink-dashboard-sem-conexões](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="b1ff9-150">Caso contrário, os navegadores conectados são mostrados com o caminho para a página que cada navegador está mostrando:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![navegadorlink-dashboard-duas conexões](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="b1ff9-152">Você também pode clicar em um nome de navegador individual para atualizar apenas esse navegador.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="b1ff9-153">Habilitar ou desativar o Link do Navegador</span><span class="sxs-lookup"><span data-stu-id="b1ff9-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="b1ff9-154">Quando você reativar o Browser Link depois de desativá-lo, você deve atualizar os navegadores para reconectá-los.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="b1ff9-155">Ativar ou desativar o CSS Auto-Sync</span><span class="sxs-lookup"><span data-stu-id="b1ff9-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="b1ff9-156">Quando o CSS Auto-Sync é ativado, os navegadores conectados são atualizados automaticamente quando você faz qualquer alteração em arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="b1ff9-157">Como ele funciona</span><span class="sxs-lookup"><span data-stu-id="b1ff9-157">How it works</span></span>

<span data-ttu-id="b1ff9-158">O Browser [SignalR](xref:signalr/introduction) Link usa para criar um canal de comunicação entre o Visual Studio e o navegador.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="b1ff9-159">Quando o Browser Link é ativado, SignalR o Visual Studio atua como um servidor ao que vários clientes (navegadores) podem se conectar.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="b1ff9-160">O Browser Link também registra um componente de middleware no pipeline de solicitação do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="b1ff9-161">Este componente injeta `<script>` referências especiais em cada solicitação de página do servidor.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="b1ff9-162">Você pode ver as referências do script selecionando **'Exibir origem'** no navegador e rolando até o final do conteúdo da `<body>` tag:</span><span class="sxs-lookup"><span data-stu-id="b1ff9-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="b1ff9-163">Seus arquivos de origem não foram modificados.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-163">Your source files aren't modified.</span></span> <span data-ttu-id="b1ff9-164">O componente middleware injeta as referências do script dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="b1ff9-165">Como o código do lado do navegador é todo SignalR JavaScript, ele funciona em todos os navegadores que suportam sem exigir um plug-in do navegador.</span><span class="sxs-lookup"><span data-stu-id="b1ff9-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
