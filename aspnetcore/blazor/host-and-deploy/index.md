---
title: Hospedar e implantar ASP.NET CoreBlazor
author: guardrex
description: Descubra como hospedar e implantar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 77202cd60d357c27237cdb925e0adc00e66d2e56
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407704"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="95ba9-103">Hospedar e implantar ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="95ba9-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="95ba9-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="95ba9-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="95ba9-105">Publicar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="95ba9-105">Publish the app</span></span>

<span data-ttu-id="95ba9-106">Os aplicativos são publicados para implantação na configuração de versão.</span><span class="sxs-lookup"><span data-stu-id="95ba9-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ba9-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ba9-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="95ba9-108">Selecione **Compilar**  >  **publicar {Application}** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="95ba9-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="95ba9-109">Selecione o botão *destino de publicação*.</span><span class="sxs-lookup"><span data-stu-id="95ba9-109">Select the *publish target*.</span></span> <span data-ttu-id="95ba9-110">Para publicar localmente, selecione **Pasta**.</span><span class="sxs-lookup"><span data-stu-id="95ba9-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="95ba9-111">Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente.</span><span class="sxs-lookup"><span data-stu-id="95ba9-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="95ba9-112">Selecione o **`Publish`** botão.</span><span class="sxs-lookup"><span data-stu-id="95ba9-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ba9-113">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="95ba9-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="95ba9-114">Selecione **criar**  >  **publicar na pasta**.</span><span class="sxs-lookup"><span data-stu-id="95ba9-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="95ba9-115">Confirme a pasta para receber os ativos publicados e selecione **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="95ba9-115">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="95ba9-116">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="95ba9-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="95ba9-117">Use o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando para publicar o aplicativo com uma configuração de versão:</span><span class="sxs-lookup"><span data-stu-id="95ba9-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="95ba9-118">Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação.</span><span class="sxs-lookup"><span data-stu-id="95ba9-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="95ba9-119">Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.</span><span class="sxs-lookup"><span data-stu-id="95ba9-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="95ba9-120">Locais de publicação:</span><span class="sxs-lookup"><span data-stu-id="95ba9-120">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="95ba9-121">Autônomo: o aplicativo é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="95ba9-121">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="95ba9-122">Para implantar o aplicativo como um site estático, copie o conteúdo da `wwwroot` pasta para o host do site estático.</span><span class="sxs-lookup"><span data-stu-id="95ba9-122">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="95ba9-123">Hospedado: o Blazor WebAssembly aplicativo cliente é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="95ba9-123">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="95ba9-124">Implante o conteúdo da `publish` pasta no host.</span><span class="sxs-lookup"><span data-stu-id="95ba9-124">Deploy the contents of the `publish` folder to the host.</span></span>
* Blazor Server<span data-ttu-id="95ba9-125">: O aplicativo é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish` pasta.</span><span class="sxs-lookup"><span data-stu-id="95ba9-125">: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="95ba9-126">Implante o conteúdo da `publish` pasta no host.</span><span class="sxs-lookup"><span data-stu-id="95ba9-126">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="95ba9-127">Os ativos na pasta são implantados no servidor Web.</span><span class="sxs-lookup"><span data-stu-id="95ba9-127">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="95ba9-128">A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="95ba9-128">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="95ba9-129">Caminho base do aplicativo</span><span class="sxs-lookup"><span data-stu-id="95ba9-129">App base path</span></span>

<span data-ttu-id="95ba9-130">O *caminho base do aplicativo* é o caminho da URL raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-130">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="95ba9-131">Considere o seguinte aplicativo ASP.NET Core e o Blazor subaplicativo:</span><span class="sxs-lookup"><span data-stu-id="95ba9-131">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="95ba9-132">O aplicativo ASP.NET Core é denominado `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="95ba9-132">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="95ba9-133">O aplicativo reside fisicamente em `d:/MyApp` .</span><span class="sxs-lookup"><span data-stu-id="95ba9-133">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="95ba9-134">As solicitações são recebidas em `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="95ba9-134">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="95ba9-135">Um Blazor aplicativo chamado `CoolApp` é um subaplicativo de `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="95ba9-135">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="95ba9-136">O subaplicativo reside fisicamente em `d:/MyApp/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="95ba9-136">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="95ba9-137">As solicitações são recebidas em `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="95ba9-137">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="95ba9-138">Sem especificar a configuração adicional para `CoolApp` o, o subaplicativo nesse cenário não tem conhecimento de onde ele reside no servidor.</span><span class="sxs-lookup"><span data-stu-id="95ba9-138">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="95ba9-139">Por exemplo, o aplicativo não pode construir URLs relativas corretas para seus recursos sem saber que ela reside no caminho de URL relativo `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="95ba9-139">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="95ba9-140">Para fornecer a configuração para o Blazor caminho base do aplicativo `https://www.contoso.com/CoolApp/` , o `<base>` atributo da marca `href` é definido como o caminho raiz relativo no `Pages/_Host.cshtml` arquivo ( Blazor Server ) ou `wwwroot/index.html` arquivo ( Blazor WebAssembly ):</span><span class="sxs-lookup"><span data-stu-id="95ba9-140">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor Server<span data-ttu-id="95ba9-141">os aplicativos também definem o caminho base do servidor chamando <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> no pipeline de solicitação do aplicativo de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="95ba9-141"> apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="95ba9-142">Ao fornecer o caminho de URL relativo, um componente que não está no diretório raiz pode construir URLs relativas ao caminho raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-142">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="95ba9-143">Os componentes em diferentes níveis da estrutura de diretório podem criar links para outros recursos em locais em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-143">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="95ba9-144">O caminho base do aplicativo também é usado para interceptar hiperlinks selecionados onde o `href` destino do link está dentro do espaço de URI do caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-144">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="95ba9-145">O Blazor roteador manipula a navegação interna.</span><span class="sxs-lookup"><span data-stu-id="95ba9-145">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="95ba9-146">Em muitos cenários de hospedagem, o caminho de URL relativo para o aplicativo é a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-146">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="95ba9-147">Nesses casos, o caminho base da URL relativa do aplicativo é uma barra ( `<base href="/" />` ), que é a configuração padrão para um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-147">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="95ba9-148">Em outros cenários de hospedagem, como páginas do GitHub e subaplicativos do IIS, o caminho base do aplicativo deve ser definido como o caminho de URL relativo do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-148">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="95ba9-149">Para definir o caminho base do aplicativo, atualize a `<base>` marca dentro dos `<head>` elementos de marca do `Pages/_Host.cshtml` arquivo ( Blazor Server ) ou `wwwroot/index.html` arquivo ( Blazor WebAssembly ).</span><span class="sxs-lookup"><span data-stu-id="95ba9-149">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="95ba9-150">Defina o `href` valor do atributo como `/{RELATIVE URL PATH}/` (a barra à direita é necessária), em que `{RELATIVE URL PATH}` é o caminho de URL relativo completo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="95ba9-150">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="95ba9-151">Para um Blazor WebAssembly aplicativo com um caminho de URL não raiz relativo (por exemplo, `<base href="/CoolApp/">` ), o aplicativo não consegue localizar seus recursos *quando executado localmente*.</span><span class="sxs-lookup"><span data-stu-id="95ba9-151">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="95ba9-152">Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no runtime.</span><span class="sxs-lookup"><span data-stu-id="95ba9-152">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="95ba9-153">Não inclua uma barra à direita.</span><span class="sxs-lookup"><span data-stu-id="95ba9-153">Don't include a trailing slash.</span></span> <span data-ttu-id="95ba9-154">Para passar o argumento de base Path ao executar o aplicativo localmente, execute o `dotnet run` comando no diretório do aplicativo com a `--pathbase` opção:</span><span class="sxs-lookup"><span data-stu-id="95ba9-154">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="95ba9-155">Para um Blazor WebAssembly aplicativo com um caminho de URL relativo de `/CoolApp/` ( `<base href="/CoolApp/">` ), o comando é:</span><span class="sxs-lookup"><span data-stu-id="95ba9-155">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="95ba9-156">O Blazor WebAssembly aplicativo responde localmente em `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="95ba9-156">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="95ba9-157">**Blazor Server`MapFallbackToPage`configuração do**</span><span class="sxs-lookup"><span data-stu-id="95ba9-157">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="95ba9-158">Passe o seguinte caminho para <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="95ba9-158">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="95ba9-159">O espaço reservado `{RELATIVE PATH}` é o caminho não raiz no servidor.</span><span class="sxs-lookup"><span data-stu-id="95ba9-159">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="95ba9-160">Por exemplo, `CoolApp` é o segmento de espaço reservado se a URL não raiz para o aplicativo `https://{HOST}:{PORT}/CoolApp/` for):</span><span class="sxs-lookup"><span data-stu-id="95ba9-160">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

## <a name="deployment"></a><span data-ttu-id="95ba9-161">Implantação</span><span class="sxs-lookup"><span data-stu-id="95ba9-161">Deployment</span></span>

<span data-ttu-id="95ba9-162">Confira orientações de implantação nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="95ba9-162">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
