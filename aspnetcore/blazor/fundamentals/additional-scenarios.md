---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre cenários adicionais para ASP.NET Core Blazor configuração do modelo de hospedagem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b28e4e43b88fcf8eab9e8959142cca21223c57ff
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239628"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor configuração do modelo de hospedagem

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este artigo aborda a configuração do modelo de hospedagem.

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalRnegociação entre origens para autenticação

*Esta seção aplica-se a Blazor WebAssembly .*

Para configurar SignalR o cliente subjacente do para enviar credenciais, como cookies ou cabeçalhos de autenticação http:

* Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

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

* Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Refletir o estado da conexão na interface do usuário

*Esta seção aplica-se a Blazor Server .*

Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente.

Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.

| Classe CSS                       | Indicando&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Uma conexão perdida. O cliente está tentando se reconectar. Mostrar o modal. |
| `components-reconnect-hide`     | Uma conexão ativa é restabelecida com o servidor. Ocultar a janela restrita. |
| `components-reconnect-failed`   | Falha na reconexão, provavelmente devido a uma falha de rede. Para tentar a reconexão, chame `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Reconexão rejeitada. O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido. Para recarregar o aplicativo, chame `location.reload()` . Esse estado de conexão pode resultar quando:<ul><li>Ocorre uma falha no circuito do lado do servidor.</li><li>O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário. As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</li><li>O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</li></ul> |

## <a name="render-mode"></a>Modo de renderização

*Esta seção aplica-se a Blazor Server .*

Blazor Serveros aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso é configurado na `_Host.cshtml` Razor página:

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

| Modo de renderização | Descrição |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o componente em HTML estático e inclui um marcador para um Blazor Server aplicativo. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor Server aplicativo. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o componente em HTML estático. |

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurar o SignalR cliente para Blazor Server aplicativos

*Esta seção aplica-se a Blazor Server .*

Configure o SignalR cliente usado por Blazor Server aplicativos no `Pages/_Host.cshtml` arquivo. Coloque um script que chame `Blazor.start` após o `_framework/blazor.server.js` script e dentro da `</body>` marca.

### <a name="logging"></a>Registro em log

Para configurar o SignalR log do cliente:

* Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.
* Passe um objeto de configuração ( `configureSignalR` ) que chama `configureLogging` com o nível de log no Client Builder.

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

No exemplo anterior, `information` é equivalente a um nível de log de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Modificar o manipulador de reconexão

Os eventos de conexão de circuito do manipulador de reconexão podem ser modificados para comportamentos personalizados, como:

* Para notificar o usuário se a conexão for descartada.
* Para executar o registro em log (do cliente) quando um circuito é conectado.

Para modificar os eventos de conexão:

* Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.
* Registre retornos de chamada para alterações de conexão para conexões descartadas ( `onConnectionDown` ) e conexões estabelecidas/restabelecidas ( `onConnectionUp` ). **Ambos** `onConnectionDown` e `onConnectionUp` deve ser especificado.

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Ajustar a contagem de repetição de reconexão e o intervalo

Para ajustar a contagem de repetição de reconexão e o intervalo:

* Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.
* Defina o número de repetições ( `maxRetries` ) e o período em milissegundos permitidos para cada tentativa de repetição ( `retryIntervalMilliseconds` ).

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

### <a name="hide-or-replace-the-reconnection-display"></a>Ocultar ou substituir a exibição de reconexão

Para ocultar a exibição da reconexão:

* Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.
* Defina o manipulador de reconexão `_reconnectionDisplay` como um objeto vazio ( `{}` ou `new Object()` ).

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

Para substituir a exibição da reconexão, defina `_reconnectionDisplay` no exemplo anterior como o elemento para exibição:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

O espaço reservado `{ELEMENT ID}` é a ID do elemento HTML a ser exibido.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/index>
