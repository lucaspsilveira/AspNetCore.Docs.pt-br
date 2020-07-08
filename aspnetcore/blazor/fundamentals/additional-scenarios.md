---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre cenários adicionais para ASP.NET Core Blazor configuração do modelo de hospedagem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: e62cb2ab865fbf57166d5ec3d1344183c00c2095
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059832"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="6f9ef-103">ASP.NET Core Blazor configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="6f9ef-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="6f9ef-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f9ef-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6f9ef-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="6f9ef-106">negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="6f9ef-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="6f9ef-107">*Esta seção aplica-se a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="6f9ef-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="6f9ef-108">Para configurar SignalR o cliente subjacente do para enviar credenciais, como cookies ou cabeçalhos de autenticação http:</span><span class="sxs-lookup"><span data-stu-id="6f9ef-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="6f9ef-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="6f9ef-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="6f9ef-110">Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:</span><span class="sxs-lookup"><span data-stu-id="6f9ef-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="6f9ef-111">Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="6f9ef-112">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="6f9ef-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="6f9ef-113">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="6f9ef-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6f9ef-114">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="6f9ef-115">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="6f9ef-116">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="6f9ef-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="6f9ef-117">Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="6f9ef-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="6f9ef-118">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="6f9ef-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="6f9ef-119">CSS class</span></span>                       | <span data-ttu-id="6f9ef-120">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="6f9ef-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="6f9ef-121">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-121">A lost connection.</span></span> <span data-ttu-id="6f9ef-122">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="6f9ef-123">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="6f9ef-124">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="6f9ef-125">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="6f9ef-126">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="6f9ef-127">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="6f9ef-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="6f9ef-128">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-128">Reconnection rejected.</span></span> <span data-ttu-id="6f9ef-129">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="6f9ef-130">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="6f9ef-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="6f9ef-131">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="6f9ef-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="6f9ef-132">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="6f9ef-133">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="6f9ef-134">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="6f9ef-135">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="6f9ef-136">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="6f9ef-136">Render mode</span></span>

<span data-ttu-id="6f9ef-137">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="6f9ef-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="6f9ef-138">os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="6f9ef-139">Isso é configurado na `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="6f9ef-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="6f9ef-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="6f9ef-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="6f9ef-141">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="6f9ef-142">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="6f9ef-143">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="6f9ef-143">Render mode</span></span> | <span data-ttu-id="6f9ef-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="6f9ef-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="6f9ef-145">Renderiza o componente em HTML estático e inclui um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="6f9ef-146">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="6f9ef-147">Renderiza um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="6f9ef-148">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-148">Output from the component isn't included.</span></span> <span data-ttu-id="6f9ef-149">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="6f9ef-150">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="6f9ef-151">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="6f9ef-152">Configurar o SignalR cliente para Blazor Server aplicativos</span><span class="sxs-lookup"><span data-stu-id="6f9ef-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="6f9ef-153">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="6f9ef-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="6f9ef-154">Às vezes, você precisa configurar o SignalR cliente usado por Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="6f9ef-155">Por exemplo, talvez você queira configurar o registro em log no SignalR cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="6f9ef-156">Para configurar o SignalR cliente no `Pages/_Host.cshtml` arquivo:</span><span class="sxs-lookup"><span data-stu-id="6f9ef-156">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="6f9ef-157">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="6f9ef-158">Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.</span><span class="sxs-lookup"><span data-stu-id="6f9ef-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6f9ef-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6f9ef-159">Additional resources</span></span>

* <xref:fundamentals/logging/index>
