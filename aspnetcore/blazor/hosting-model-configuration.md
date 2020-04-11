---
title: configuração Blazor do modelo de hospedagem ASP.NET Core
author: guardrex
description: Saiba Blazor mais sobre a configuração do modelo de hospedagem, incluindo como integrar componentes Razor em páginas de barbear e aplicativos MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: ca1b3ea9092640ca561b3fbe02ddce6f974c525e
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123381"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="249a0-103">ASP.NET configuração do modelo de hospedagem Core Blazor</span><span class="sxs-lookup"><span data-stu-id="249a0-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="249a0-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="249a0-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="249a0-105">Este artigo abrange a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="249a0-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="249a0-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="249a0-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="249a0-107">Ambiente</span><span class="sxs-lookup"><span data-stu-id="249a0-107">Environment</span></span>

<span data-ttu-id="249a0-108">Ao executar um aplicativo localmente, o ambiente é padrão para o Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="249a0-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="249a0-109">Quando o aplicativo é publicado, o ambiente é padrão para Produção.</span><span class="sxs-lookup"><span data-stu-id="249a0-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="249a0-110">Um aplicativo WebAssembly hospedado pega o ambiente do servidor através de um middleware que `blazor-environment` comunica o ambiente ao navegador adicionando o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="249a0-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="249a0-111">O valor do cabeçalho é o ambiente.</span><span class="sxs-lookup"><span data-stu-id="249a0-111">The value of the header is the environment.</span></span> <span data-ttu-id="249a0-112">O aplicativo Blazor hospedado e o aplicativo do servidor compartilham o mesmo ambiente.</span><span class="sxs-lookup"><span data-stu-id="249a0-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="249a0-113">Para obter mais informações, incluindo como <xref:fundamentals/environments>configurar o ambiente, consulte .</span><span class="sxs-lookup"><span data-stu-id="249a0-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="249a0-114">Para um aplicativo autônomo em execução local, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="249a0-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="249a0-115">Para especificar o ambiente para outros `blazor-environment` ambientes de hospedagem, adicione o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="249a0-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="249a0-116">No exemplo a seguir para IIS, adicione o cabeçalho personalizado ao arquivo *web.config* publicado.</span><span class="sxs-lookup"><span data-stu-id="249a0-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="249a0-117">O arquivo *Web.config* está localizado na pasta *bin/Release/{TARGET FRAMEWORK}/publish:*</span><span class="sxs-lookup"><span data-stu-id="249a0-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="249a0-118">Para usar um arquivo *web.config* personalizado para IIS que não seja substituído quando <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>o aplicativo for publicado na pasta *de publicação,* consulte .</span><span class="sxs-lookup"><span data-stu-id="249a0-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="249a0-119">Obtenha o ambiente do aplicativo em `IWebAssemblyHostEnvironment` um componente `Environment` injetando e lendo a propriedade:</span><span class="sxs-lookup"><span data-stu-id="249a0-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

### <a name="configuration"></a><span data-ttu-id="249a0-120">Configuração</span><span class="sxs-lookup"><span data-stu-id="249a0-120">Configuration</span></span>

<span data-ttu-id="249a0-121">A partir da versão ASP.NET Core 3.2 Preview 3, o Blazor WebAssembly suporta a configuração a partir de:</span><span class="sxs-lookup"><span data-stu-id="249a0-121">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="249a0-122">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="249a0-122">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="249a0-123">*wwwroot/appsettings. {ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="249a0-123">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="249a0-124">Adicionar um *arquivo appsettings.json* na pasta *wwwroot:*</span><span class="sxs-lookup"><span data-stu-id="249a0-124">Add an *appsettings.json* file in the *wwwroot* folder:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="249a0-125">Injete uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="249a0-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> <span data-ttu-id="249a0-126">A configuração em um aplicativo Blazor WebAssembly é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="249a0-126">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="249a0-127">**Não armazene segredos de aplicativos ou credenciais na configuração.**</span><span class="sxs-lookup"><span data-stu-id="249a0-127">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="249a0-128">Os arquivos de configuração são armazenados em cache para uso off-line.</span><span class="sxs-lookup"><span data-stu-id="249a0-128">Configuration files are cached for offline use.</span></span> <span data-ttu-id="249a0-129">Com [pwAs (Progressive Web Applications, aplicativos da Web progressivos),](xref:blazor/progressive-web-app)você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="249a0-129">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="249a0-130">Editar arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="249a0-130">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="249a0-131">Os usuários têm versões armazenadas em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="249a0-131">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="249a0-132">Os arquivos *service-worker.js* e *service-worker-assets.js* do PWA devem ser reconstruídos na compilação, que sinaliza para o aplicativo na próxima visita on-line do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="249a0-132">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="249a0-133">Para obter mais informações sobre como as atualizações <xref:blazor/progressive-web-app#background-updates>em segundo plano são tratadas pelos PWAs, consulte .</span><span class="sxs-lookup"><span data-stu-id="249a0-133">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="249a0-134">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="249a0-134">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="249a0-135">Reflita o estado de conexão na UI</span><span class="sxs-lookup"><span data-stu-id="249a0-135">Reflect the connection state in the UI</span></span>

<span data-ttu-id="249a0-136">Quando o cliente detecta que a conexão foi perdida, uma ui padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="249a0-136">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="249a0-137">Se a reconexão falhar, o usuário tem a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="249a0-137">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="249a0-138">Para personalizar a ia de `id` usuário, defina um elemento com um of `components-reconnect-modal` na `<body>` página *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="249a0-138">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="249a0-139">A tabela a seguir descreve as classes `components-reconnect-modal` CSS aplicadas ao elemento.</span><span class="sxs-lookup"><span data-stu-id="249a0-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="249a0-140">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="249a0-140">CSS class</span></span>                       | <span data-ttu-id="249a0-141">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="249a0-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="249a0-142">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="249a0-142">A lost connection.</span></span> <span data-ttu-id="249a0-143">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="249a0-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="249a0-144">Mostre o modal.</span><span class="sxs-lookup"><span data-stu-id="249a0-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="249a0-145">Uma conexão ativa é restabelecida ao servidor.</span><span class="sxs-lookup"><span data-stu-id="249a0-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="249a0-146">Esconda o modal.</span><span class="sxs-lookup"><span data-stu-id="249a0-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="249a0-147">A reconexão falhou, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="249a0-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="249a0-148">Para tentar reconectar, ligue . `window.Blazor.reconnect()`</span><span class="sxs-lookup"><span data-stu-id="249a0-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="249a0-149">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="249a0-149">Reconnection rejected.</span></span> <span data-ttu-id="249a0-150">O servidor foi alcançado, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="249a0-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="249a0-151">Para recarregar o `location.reload()`aplicativo, ligue .</span><span class="sxs-lookup"><span data-stu-id="249a0-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="249a0-152">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="249a0-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="249a0-153">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="249a0-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="249a0-154">O cliente está desconectado tempo suficiente para que o servidor abandone o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="249a0-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="249a0-155">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="249a0-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="249a0-156">O servidor é reiniciado ou o processo de trabalhador do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="249a0-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="249a0-157">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="249a0-157">Render mode</span></span>

<span data-ttu-id="249a0-158">Os aplicativos do Blazor Server são configurados por padrão para pré-renderizar a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="249a0-158">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="249a0-159">Isso está configurado na página *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="249a0-159">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="249a0-160">`RenderMode`configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="249a0-160">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="249a0-161">É pré-renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="249a0-161">Is prerendered into the page.</span></span>
* <span data-ttu-id="249a0-162">É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.</span><span class="sxs-lookup"><span data-stu-id="249a0-162">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="249a0-163">Descrição</span><span class="sxs-lookup"><span data-stu-id="249a0-163">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="249a0-164">Renderiza o componente em HTML estático e Blazor inclui um marcador para um aplicativo Server.</span><span class="sxs-lookup"><span data-stu-id="249a0-164">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="249a0-165">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="249a0-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="249a0-166">Renderiza um marcador Blazor para um aplicativo do Servidor.</span><span class="sxs-lookup"><span data-stu-id="249a0-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="249a0-167">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="249a0-167">Output from the component isn't included.</span></span> <span data-ttu-id="249a0-168">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="249a0-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="249a0-169">Torna o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="249a0-169">Renders the component into static HTML.</span></span> |

<span data-ttu-id="249a0-170">A renderização de componentes do servidor de uma página HTML estática não é suportada.</span><span class="sxs-lookup"><span data-stu-id="249a0-170">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="249a0-171">Renderizar componentes interativos imponentes a partir de páginas e visualizações de Navalha</span><span class="sxs-lookup"><span data-stu-id="249a0-171">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="249a0-172">Componentes interativos imponentes podem ser adicionados a uma página ou exibição de Navalha.</span><span class="sxs-lookup"><span data-stu-id="249a0-172">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="249a0-173">Quando a página ou exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="249a0-173">When the page or view renders:</span></span>

* <span data-ttu-id="249a0-174">O componente é pré-renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="249a0-174">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="249a0-175">O estado de componente inicial usado para pré-renderização é perdido.</span><span class="sxs-lookup"><span data-stu-id="249a0-175">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="249a0-176">Novo estado componente é SignalR criado quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="249a0-176">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="249a0-177">A página Navalha `Counter` a seguir renderiza um componente:</span><span class="sxs-lookup"><span data-stu-id="249a0-177">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="249a0-178">Renderizar componentes não interativos de páginas e visualizações de Navalha</span><span class="sxs-lookup"><span data-stu-id="249a0-178">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="249a0-179">Na página Navalha `Counter` a seguir, o componente é renderizado estáticamente com um valor inicial especificado usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="249a0-179">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="249a0-180">Uma `MyComponent` vez que é renderizado estáticamente, o componente não pode ser interativo.</span><span class="sxs-lookup"><span data-stu-id="249a0-180">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="249a0-181">Configure SignalR o Blazor cliente para aplicativos do Servidor</span><span class="sxs-lookup"><span data-stu-id="249a0-181">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="249a0-182">Às vezes, você precisa SignalR configurar Blazor o cliente usado pelos aplicativos do Servidor.</span><span class="sxs-lookup"><span data-stu-id="249a0-182">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="249a0-183">Por exemplo, você pode querer configurar SignalR o login no cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="249a0-183">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="249a0-184">Para configurar SignalR o cliente no arquivo *Páginas/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="249a0-184">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="249a0-185">Adicione `autostart="false"` um atributo `<script>` à `blazor.server.js` tag para o script.</span><span class="sxs-lookup"><span data-stu-id="249a0-185">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="249a0-186">Ligue `Blazor.start` e passe em um objeto SignalR de configuração que especifica o construtor.</span><span class="sxs-lookup"><span data-stu-id="249a0-186">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
