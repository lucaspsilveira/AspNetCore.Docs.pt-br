---
title: Hospedar e implantar ASP.NET CoreBlazor
author: guardrex
description: Descubra como hospedar e implantar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 482b066d347c2c572b0ffb78ccab1ac391195823
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452233"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="a383e-103">Hospedar e implantar ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="a383e-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="a383e-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a383e-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="a383e-105">Publicar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a383e-105">Publish the app</span></span>

<span data-ttu-id="a383e-106">Os aplicativos são publicados para implantação na configuração de versão.</span><span class="sxs-lookup"><span data-stu-id="a383e-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a383e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a383e-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a383e-108">Selecione **Compilar**  >  **publicar {Application}** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="a383e-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="a383e-109">Selecione o botão *destino de publicação*.</span><span class="sxs-lookup"><span data-stu-id="a383e-109">Select the *publish target*.</span></span> <span data-ttu-id="a383e-110">Para publicar localmente, selecione **Pasta**.</span><span class="sxs-lookup"><span data-stu-id="a383e-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="a383e-111">Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente.</span><span class="sxs-lookup"><span data-stu-id="a383e-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="a383e-112">Clique no botão **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="a383e-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a383e-113">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a383e-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a383e-114">Selecione **criar**  >  **publicar na pasta**.</span><span class="sxs-lookup"><span data-stu-id="a383e-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="a383e-115">Confirme a pasta para receber os ativos publicados e selecione **publicar**.</span><span class="sxs-lookup"><span data-stu-id="a383e-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a383e-116">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a383e-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a383e-117">Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:</span><span class="sxs-lookup"><span data-stu-id="a383e-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="a383e-118">Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação.</span><span class="sxs-lookup"><span data-stu-id="a383e-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="a383e-119">Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.</span><span class="sxs-lookup"><span data-stu-id="a383e-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="a383e-120">Locais de publicação:</span><span class="sxs-lookup"><span data-stu-id="a383e-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="a383e-121">Webassembly</span><span class="sxs-lookup"><span data-stu-id="a383e-121"> WebAssembly</span></span>
  * <span data-ttu-id="a383e-122">Autônomo: o aplicativo é publicado na pasta */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="a383e-122">Standalone: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="a383e-123">Para implantar o aplicativo como um site estático, copie o conteúdo da pasta *wwwroot* para o host do site estático.</span><span class="sxs-lookup"><span data-stu-id="a383e-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="a383e-124">Hospedado: o Blazor aplicativo Webassembly do cliente é publicado na pasta */bin/Release/{Target Framework}/Publish/wwwroot* do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="a383e-124">Hosted: The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="a383e-125">Implante o conteúdo da pasta de *publicação* no host.</span><span class="sxs-lookup"><span data-stu-id="a383e-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="a383e-126">Servidor: o aplicativo é publicado na pasta */Publish do/bin/Release/{Target Framework}* .</span><span class="sxs-lookup"><span data-stu-id="a383e-126"> Server: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="a383e-127">Implante o conteúdo da pasta de *publicação* no host.</span><span class="sxs-lookup"><span data-stu-id="a383e-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="a383e-128">Os ativos na pasta são implantados no servidor Web.</span><span class="sxs-lookup"><span data-stu-id="a383e-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="a383e-129">A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="a383e-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="a383e-130">Caminho base do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a383e-130">App base path</span></span>

<span data-ttu-id="a383e-131">O *caminho base do aplicativo* é o caminho da URL raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="a383e-132">Considere o seguinte aplicativo ASP.NET Core e o Blazor subaplicativo:</span><span class="sxs-lookup"><span data-stu-id="a383e-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="a383e-133">O aplicativo ASP.NET Core é denominado `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="a383e-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="a383e-134">O aplicativo reside fisicamente em *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="a383e-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="a383e-135">As solicitações são recebidas em `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="a383e-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="a383e-136">Um Blazor aplicativo chamado `CoolApp` é um subaplicativo de `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="a383e-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="a383e-137">O subaplicativo reside fisicamente em *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="a383e-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="a383e-138">As solicitações são recebidas em `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="a383e-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="a383e-139">Sem especificar a configuração adicional para `CoolApp` o, o subaplicativo nesse cenário não tem conhecimento de onde ele reside no servidor.</span><span class="sxs-lookup"><span data-stu-id="a383e-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="a383e-140">Por exemplo, o aplicativo não pode construir URLs relativas corretas para seus recursos sem saber que ela reside no caminho de URL relativo `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="a383e-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="a383e-141">Para fornecer a configuração para o Blazor caminho base do aplicativo `https://www.contoso.com/CoolApp/` , o `<base>` atributo da marca `href` é definido como o caminho raiz relativo no arquivo *pages/_Host. cshtml* ( Blazor servidor) ou arquivo *wwwroot/index.html* ( Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="a383e-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="a383e-142">Os aplicativos de servidor também definem o caminho base do servidor chamando <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> no pipeline de solicitação do aplicativo de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a383e-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="a383e-143">Ao fornecer o caminho de URL relativo, um componente que não está no diretório raiz pode construir URLs relativas ao caminho raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="a383e-144">Os componentes em diferentes níveis da estrutura de diretório podem criar links para outros recursos em locais em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="a383e-145">O caminho base do aplicativo também é usado para interceptar hiperlinks selecionados onde o `href` destino do link está dentro do espaço de URI do caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="a383e-146">O Blazor roteador manipula a navegação interna.</span><span class="sxs-lookup"><span data-stu-id="a383e-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="a383e-147">Em muitos cenários de hospedagem, o caminho de URL relativo para o aplicativo é a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="a383e-148">Nesses casos, o caminho base da URL relativa do aplicativo é uma barra ( `<base href="/" />` ), que é a configuração padrão para um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="a383e-149">Em outros cenários de hospedagem, como páginas do GitHub e subaplicativos do IIS, o caminho base do aplicativo deve ser definido como o caminho de URL relativo do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="a383e-150">Para definir o caminho base do aplicativo, atualize a `<base>` marca dentro dos `<head>` elementos de marca do arquivo *pages/_Host. cshtml* ( Blazor servidor) ou arquivo *wwwroot/index.html* ( Blazor Webassembly).</span><span class="sxs-lookup"><span data-stu-id="a383e-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="a383e-151">Defina o `href` valor do atributo como `/{RELATIVE URL PATH}/` (a barra à direita é necessária), em que `{RELATIVE URL PATH}` é o caminho de URL relativo completo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a383e-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="a383e-152">Para um Blazor aplicativo Webassembly com um caminho de URL não raiz relativo (por exemplo, `<base href="/CoolApp/">` ), o aplicativo não consegue localizar seus recursos *quando executado localmente*.</span><span class="sxs-lookup"><span data-stu-id="a383e-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="a383e-153">Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no runtime.</span><span class="sxs-lookup"><span data-stu-id="a383e-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="a383e-154">Não inclua uma barra à direita.</span><span class="sxs-lookup"><span data-stu-id="a383e-154">Don't include a trailing slash.</span></span> <span data-ttu-id="a383e-155">Para passar o argumento de base Path ao executar o aplicativo localmente, execute o `dotnet run` comando no diretório do aplicativo com a `--pathbase` opção:</span><span class="sxs-lookup"><span data-stu-id="a383e-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="a383e-156">Para um Blazor aplicativo Webassembly com um caminho de URL relativo de `/CoolApp/` ( `<base href="/CoolApp/">` ), o comando é:</span><span class="sxs-lookup"><span data-stu-id="a383e-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="a383e-157">O Blazor aplicativo Webassembly responde localmente em `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="a383e-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="a383e-158">Implantação</span><span class="sxs-lookup"><span data-stu-id="a383e-158">Deployment</span></span>

<span data-ttu-id="a383e-159">Confira orientações de implantação nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="a383e-159">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
