---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre cenários adicionais para ASP.NET Core Blazor configuração do modelo de hospedagem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 236dffd829bcd7c30ae1145242ce07cd8e9857e6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402943"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="a8a88-103">ASP.NET Core Blazor configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="a8a88-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="a8a88-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a8a88-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a8a88-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="a8a88-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="a8a88-106">negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="a8a88-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="a8a88-107">*Esta seção aplica-se a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="a8a88-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="a8a88-108">Para configurar SignalR o cliente subjacente do para enviar credenciais, como cookies ou cabeçalhos de autenticação http:</span><span class="sxs-lookup"><span data-stu-id="a8a88-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="a8a88-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="a8a88-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="a8a88-110">Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:</span><span class="sxs-lookup"><span data-stu-id="a8a88-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="a8a88-111">Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="a8a88-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="a8a88-112">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="a8a88-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="a8a88-113">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="a8a88-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="a8a88-114">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="a8a88-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="a8a88-115">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="a8a88-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="a8a88-116">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="a8a88-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="a8a88-117">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="a8a88-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="a8a88-118">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="a8a88-118">CSS class</span></span>                       | <span data-ttu-id="a8a88-119">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="a8a88-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="a8a88-120">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="a8a88-120">A lost connection.</span></span> <span data-ttu-id="a8a88-121">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="a8a88-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="a8a88-122">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="a8a88-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="a8a88-123">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="a8a88-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="a8a88-124">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="a8a88-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="a8a88-125">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="a8a88-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="a8a88-126">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="a8a88-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="a8a88-127">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="a8a88-127">Reconnection rejected.</span></span> <span data-ttu-id="a8a88-128">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="a8a88-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="a8a88-129">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="a8a88-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="a8a88-130">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="a8a88-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="a8a88-131">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="a8a88-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="a8a88-132">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="a8a88-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="a8a88-133">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="a8a88-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="a8a88-134">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="a8a88-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="a8a88-135">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="a8a88-135">Render mode</span></span>

<span data-ttu-id="a8a88-136">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="a8a88-136">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="a8a88-137">os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="a8a88-137"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="a8a88-138">Isso é configurado na `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="a8a88-138">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="a8a88-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="a8a88-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="a8a88-140">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="a8a88-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="a8a88-141">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="a8a88-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="a8a88-142">Descrição</span><span class="sxs-lookup"><span data-stu-id="a8a88-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="a8a88-143">Renderiza o componente em HTML estático e inclui um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8a88-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a8a88-144">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8a88-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="a8a88-145">Renderiza um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8a88-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a8a88-146">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="a8a88-146">Output from the component isn't included.</span></span> <span data-ttu-id="a8a88-147">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8a88-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="a8a88-148">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="a8a88-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="a8a88-149">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="a8a88-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="a8a88-150">Configurar o SignalR cliente para Blazor Server aplicativos</span><span class="sxs-lookup"><span data-stu-id="a8a88-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="a8a88-151">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="a8a88-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="a8a88-152">Às vezes, você precisa configurar o SignalR cliente usado por Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a8a88-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="a8a88-153">Por exemplo, talvez você queira configurar o registro em log no SignalR cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="a8a88-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="a8a88-154">Para configurar o SignalR cliente no `Pages/_Host.cshtml` arquivo:</span><span class="sxs-lookup"><span data-stu-id="a8a88-154">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="a8a88-155">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="a8a88-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="a8a88-156">Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.</span><span class="sxs-lookup"><span data-stu-id="a8a88-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a8a88-157">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a8a88-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
