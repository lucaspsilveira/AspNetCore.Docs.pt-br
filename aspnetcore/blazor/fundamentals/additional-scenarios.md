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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 726aafd2bf5d3469c30ebce1e4eea8ed8ec8d58e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103561"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor configuração do modelo de hospedagem

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este artigo aborda a configuração do modelo de hospedagem.

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalRnegociação entre origens para autenticação

*Esta seção aplica-se ao Blazor Webassembly.*

Para configurar SignalR o cliente subjacente do para enviar credenciais, como cookies ou cabeçalhos de autenticação http:

* Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> em solicitações de [busca](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) de origem cruzada:

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Refletir o estado da conexão na interface do usuário

*Esta seção aplica-se ao Blazor servidor do.*

Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente.

Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da página *_Host. cshtml* Razor :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.

| Classe CSS                       | Indicando&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Uma conexão perdida. O cliente está tentando se reconectar. Mostrar o modal. |
| `components-reconnect-hide`     | Uma conexão ativa é restabelecida com o servidor. Ocultar a janela restrita. |
| `components-reconnect-failed`   | Falha na reconexão, provavelmente devido a uma falha de rede. Para tentar a reconexão, chame `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Reconexão rejeitada. O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido. Para recarregar o aplicativo, chame `location.reload()` . Esse estado de conexão pode resultar quando:<ul><li>Ocorre uma falha no circuito do lado do servidor.</li><li>O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário. As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</li><li>O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</li></ul> |

## <a name="render-mode"></a>Modo de renderização

*Esta seção aplica-se ao Blazor servidor do.*

BlazorOs aplicativos de servidor são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso é configurado na página *_Host. cshtml* Razor :

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Descrição |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor aplicativo de servidor. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o componente em HTML estático. |

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurar o SignalR cliente para Blazor aplicativos de servidor

*Esta seção aplica-se ao Blazor servidor do.*

Às vezes, você precisa configurar o SignalR cliente usado por Blazor aplicativos de servidor. Por exemplo, talvez você queira configurar o registro em log no SignalR cliente para diagnosticar um problema de conexão.

Para configurar o SignalR cliente no arquivo *Pages/_Host. cshtml* :

* Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.
* Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/index>
