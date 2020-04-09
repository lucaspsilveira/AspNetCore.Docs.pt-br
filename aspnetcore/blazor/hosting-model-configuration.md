---
title: configuração Blazor do modelo de hospedagem ASP.NET Core
author: guardrex
description: Saiba Blazor mais sobre a configuração do modelo de hospedagem, incluindo como integrar componentes Razor em páginas de barbear e aplicativos MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306428"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="271ba-103">ASP.NET configuração do modelo de hospedagem Core Blazor</span><span class="sxs-lookup"><span data-stu-id="271ba-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="271ba-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="271ba-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="271ba-105">Este artigo abrange a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="271ba-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="271ba-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="271ba-106">Blazor WebAssembly</span></span>

<span data-ttu-id="271ba-107">A partir da versão ASP.NET Core 3.2 Preview 3, o Blazor WebAssembly suporta a configuração a partir de:</span><span class="sxs-lookup"><span data-stu-id="271ba-107">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="271ba-108">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="271ba-108">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="271ba-109">*wwwroot/appsettings. {ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="271ba-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="271ba-110">Em um aplicativo Blazor Hosted, o [ambiente de tempo de execução](xref:fundamentals/environments) é o mesmo que o valor do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="271ba-110">In a Blazor Hosted app, the [runtime environment](xref:fundamentals/environments) is the same as the server app's value.</span></span>

<span data-ttu-id="271ba-111">Ao executar o aplicativo localmente, o ambiente é padrão para o Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="271ba-111">When running the app locally, the environment defaults to Development.</span></span> <span data-ttu-id="271ba-112">Quando o aplicativo é publicado, o ambiente é padrão para Produção.</span><span class="sxs-lookup"><span data-stu-id="271ba-112">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="271ba-113">Para obter mais informações, incluindo como <xref:fundamentals/environments>configurar o ambiente, consulte .</span><span class="sxs-lookup"><span data-stu-id="271ba-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

> [!WARNING]
> <span data-ttu-id="271ba-114">A configuração em um aplicativo Blazor WebAssembly é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="271ba-114">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="271ba-115">**Não armazene segredos de aplicativos ou credenciais na configuração.**</span><span class="sxs-lookup"><span data-stu-id="271ba-115">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="271ba-116">Os arquivos de configuração são armazenados em cache para uso off-line.</span><span class="sxs-lookup"><span data-stu-id="271ba-116">Configuration files are cached for offline use.</span></span> <span data-ttu-id="271ba-117">Com [pwAs (Progressive Web Applications, aplicativos da Web progressivos),](xref:blazor/progressive-web-app)você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="271ba-117">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="271ba-118">Editar arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="271ba-118">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="271ba-119">Os usuários têm versões armazenadas em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="271ba-119">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="271ba-120">Os arquivos *service-worker.js* e *service-worker-assets.js* do PWA devem ser reconstruídos na compilação, que sinaliza para o aplicativo na próxima visita on-line do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="271ba-120">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="271ba-121">Para obter mais informações sobre como as atualizações <xref:blazor/progressive-web-app#background-updates>em segundo plano são tratadas pelos PWAs, consulte .</span><span class="sxs-lookup"><span data-stu-id="271ba-121">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="271ba-122">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="271ba-122">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="271ba-123">Reflita o estado de conexão na UI</span><span class="sxs-lookup"><span data-stu-id="271ba-123">Reflect the connection state in the UI</span></span>

<span data-ttu-id="271ba-124">Quando o cliente detecta que a conexão foi perdida, uma ui padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="271ba-124">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="271ba-125">Se a reconexão falhar, o usuário tem a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="271ba-125">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="271ba-126">Para personalizar a ia de `id` usuário, defina um elemento com um of `components-reconnect-modal` na `<body>` página *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="271ba-126">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="271ba-127">A tabela a seguir descreve as classes `components-reconnect-modal` CSS aplicadas ao elemento.</span><span class="sxs-lookup"><span data-stu-id="271ba-127">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="271ba-128">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="271ba-128">CSS class</span></span>                       | <span data-ttu-id="271ba-129">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="271ba-129">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="271ba-130">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="271ba-130">A lost connection.</span></span> <span data-ttu-id="271ba-131">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="271ba-131">The client is attempting to reconnect.</span></span> <span data-ttu-id="271ba-132">Mostre o modal.</span><span class="sxs-lookup"><span data-stu-id="271ba-132">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="271ba-133">Uma conexão ativa é restabelecida ao servidor.</span><span class="sxs-lookup"><span data-stu-id="271ba-133">An active connection is re-established to the server.</span></span> <span data-ttu-id="271ba-134">Esconda o modal.</span><span class="sxs-lookup"><span data-stu-id="271ba-134">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="271ba-135">A reconexão falhou, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="271ba-135">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="271ba-136">Para tentar reconectar, ligue . `window.Blazor.reconnect()`</span><span class="sxs-lookup"><span data-stu-id="271ba-136">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="271ba-137">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="271ba-137">Reconnection rejected.</span></span> <span data-ttu-id="271ba-138">O servidor foi alcançado, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="271ba-138">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="271ba-139">Para recarregar o `location.reload()`aplicativo, ligue .</span><span class="sxs-lookup"><span data-stu-id="271ba-139">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="271ba-140">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="271ba-140">This connection state may result when:</span></span><ul><li><span data-ttu-id="271ba-141">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="271ba-141">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="271ba-142">O cliente está desconectado tempo suficiente para que o servidor abandone o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="271ba-142">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="271ba-143">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="271ba-143">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="271ba-144">O servidor é reiniciado ou o processo de trabalhador do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="271ba-144">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="271ba-145">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="271ba-145">Render mode</span></span>

<span data-ttu-id="271ba-146">Os aplicativos do Blazor Server são configurados por padrão para pré-renderizar a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="271ba-146">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="271ba-147">Isso está configurado na página *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="271ba-147">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="271ba-148">`RenderMode`configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="271ba-148">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="271ba-149">É pré-renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="271ba-149">Is prerendered into the page.</span></span>
* <span data-ttu-id="271ba-150">É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.</span><span class="sxs-lookup"><span data-stu-id="271ba-150">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="271ba-151">Descrição</span><span class="sxs-lookup"><span data-stu-id="271ba-151">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="271ba-152">Renderiza o componente em HTML estático e Blazor inclui um marcador para um aplicativo Server.</span><span class="sxs-lookup"><span data-stu-id="271ba-152">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="271ba-153">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="271ba-153">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="271ba-154">Renderiza um marcador Blazor para um aplicativo do Servidor.</span><span class="sxs-lookup"><span data-stu-id="271ba-154">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="271ba-155">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="271ba-155">Output from the component isn't included.</span></span> <span data-ttu-id="271ba-156">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="271ba-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="271ba-157">Torna o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="271ba-157">Renders the component into static HTML.</span></span> |

<span data-ttu-id="271ba-158">A renderização de componentes do servidor de uma página HTML estática não é suportada.</span><span class="sxs-lookup"><span data-stu-id="271ba-158">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="271ba-159">Renderizar componentes interativos imponentes a partir de páginas e visualizações de Navalha</span><span class="sxs-lookup"><span data-stu-id="271ba-159">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="271ba-160">Componentes interativos imponentes podem ser adicionados a uma página ou exibição de Navalha.</span><span class="sxs-lookup"><span data-stu-id="271ba-160">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="271ba-161">Quando a página ou exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="271ba-161">When the page or view renders:</span></span>

* <span data-ttu-id="271ba-162">O componente é pré-renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="271ba-162">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="271ba-163">O estado de componente inicial usado para pré-renderização é perdido.</span><span class="sxs-lookup"><span data-stu-id="271ba-163">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="271ba-164">Novo estado componente é SignalR criado quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="271ba-164">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="271ba-165">A página Navalha `Counter` a seguir renderiza um componente:</span><span class="sxs-lookup"><span data-stu-id="271ba-165">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="271ba-166">Renderizar componentes não interativos de páginas e visualizações de Navalha</span><span class="sxs-lookup"><span data-stu-id="271ba-166">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="271ba-167">Na página Navalha `Counter` a seguir, o componente é renderizado estáticamente com um valor inicial especificado usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="271ba-167">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="271ba-168">Uma `MyComponent` vez que é renderizado estáticamente, o componente não pode ser interativo.</span><span class="sxs-lookup"><span data-stu-id="271ba-168">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="271ba-169">Configure SignalR o Blazor cliente para aplicativos do Servidor</span><span class="sxs-lookup"><span data-stu-id="271ba-169">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="271ba-170">Às vezes, você precisa SignalR configurar Blazor o cliente usado pelos aplicativos do Servidor.</span><span class="sxs-lookup"><span data-stu-id="271ba-170">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="271ba-171">Por exemplo, você pode querer configurar SignalR o login no cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="271ba-171">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="271ba-172">Para configurar SignalR o cliente no arquivo *Páginas/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="271ba-172">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="271ba-173">Adicione `autostart="false"` um atributo `<script>` à `blazor.server.js` tag para o script.</span><span class="sxs-lookup"><span data-stu-id="271ba-173">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="271ba-174">Ligue `Blazor.start` e passe em um objeto SignalR de configuração que especifica o construtor.</span><span class="sxs-lookup"><span data-stu-id="271ba-174">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
