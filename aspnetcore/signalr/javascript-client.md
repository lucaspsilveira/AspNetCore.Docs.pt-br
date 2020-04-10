---
title: ASP.NET SignalR cliente JavaScript do Núcleo
author: bradygaster
description: Visão geral do SignalR ASP.NET cliente JavaScript do Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994579"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR cliente JavaScript do Núcleo

Por [Rachel Appel](https://twitter.com/rachelappel)

A biblioteca SignalR do cliente JavaScript ASP.NET permite que os desenvolvedores chamem o código do hub do lado do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Instale SignalR o pacote cliente

A SignalR biblioteca cliente JavaScript é entregue como um pacote [npm.](https://www.npmjs.com/) As seções a seguir descrevem diferentes maneiras de instalar a biblioteca do cliente.

### <a name="install-with-npm"></a>Instale com npm

Se estiver usando o Visual Studio, execute os seguintes comandos do **Console do Gerenciador** de Pacotes enquanto estiver na pasta raiz. Para visual studio code, execute os seguintes comandos a partir do **Terminal Integrado**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm instala o conteúdo do pacote na pasta *node_modules.\\ * Crie uma nova pasta chamada *signalr* sob a pasta *wwwroot\\lib.* Copie o arquivo *signalr.js* para a pasta *wwwroot\lib\signalr.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm instala o conteúdo do pacote na pasta *node_modules.\\ * Crie uma nova pasta chamada *signalr* sob a pasta *wwwroot\\lib.* Copie o arquivo *signalr.js* para a pasta *wwwroot\lib\signalr.*

::: moniker-end

Consulte SignalR o cliente JavaScript no `<script>` elemento. Por exemplo:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Use uma CDN (Content Delivery Network, rede de entrega de conteúdo)

Para usar a biblioteca do cliente sem o pré-requisito npm, consulte uma cópia hospedada em CDN da biblioteca do cliente. Por exemplo:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

A biblioteca do cliente está disponível nas seguintes CDNs:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Instale com libman

[O LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos específicos da biblioteca de clientes da biblioteca de clientes hospedada no CDN. Por exemplo, adicione apenas o arquivo JavaScript minizado ao projeto. Para obter detalhes sobre essa abordagem, consulte [Adicionar a biblioteca do SignalR cliente](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Conecte-se a um hub

O código a seguir cria e inicia uma conexão. O nome do hub é caso insensível.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Conexões de origem cruzada

Normalmente, os navegadores carregam conexões do mesmo domínio da página solicitada. No entanto, há ocasiões em que uma conexão com outro domínio é necessária.

Para evitar que um site mal-intencionado leia dados confidenciais de outro site, [as conexões de origem cruzada](xref:security/cors) são desativadas por padrão. Para permitir uma solicitação de origem `Startup` cruzada, habilite-a na classe.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Métodos de call hub do cliente

Os clientes JavaScript chamam métodos públicos em hubs através do método de [invocação](/javascript/api/%40aspnet/signalr/hubconnection#invoke) do [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). O `invoke` método aceita dois argumentos:

* O nome do método hub. No exemplo a seguir, o nome `SendMessage`do método no hub é .
* Quaisquer argumentos definidos no método do hub. No exemplo a seguir, `message`o nome do argumento é . O código de exemplo usa sintaxe de função seta que é suportada nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Se você estiver usando SignalR o Azure Service no *modo Sem Servidor,* não poderá chamar métodos de hub de um cliente. Para obter mais informações, consulte a [ SignalR documentação](/azure/azure-signalr/signalr-concept-serverless-development-config)do Serviço .

O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript . O `Promise` é resolvido com o valor de devolução (se houver) quando o método no servidor retorna. Se o método no servidor lançar `Promise` um erro, o é rejeitado com a mensagem de erro. Use `then` os `catch` métodos `Promise` em si para `await` lidar com esses casos (ou sintaxe).

O `send` método retorna `Promise`um JavaScript . O `Promise` é resolvido quando a mensagem foi enviada para o servidor. Se houver um erro no `Promise` envio da mensagem, o é rejeitado com a mensagem de erro. Use `then` os `catch` métodos `Promise` em si para `await` lidar com esses casos (ou sintaxe).

> [!NOTE]
> O `send` uso não espera até que o servidor receba a mensagem. Consequentemente, não é possível retornar dados ou erros do servidor.

## <a name="call-client-methods-from-hub"></a>Ligue para os métodos do cliente a partir do hub

Para receber mensagens do hub, defina um `HubConnection`método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do .

* O nome do método cliente JavaScript. No exemplo a seguir, `ReceiveMessage`o nome do método é .
* Argumentos o hub passa para o método. No exemplo a seguir, `message`o valor do argumento é .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

O código `connection.on` anterior é executado quando o código do lado do servidor o chama usando o método [SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRdetermina qual método de cliente chamar, combinando o `SendAsync` `connection.on`nome do método e os argumentos definidos em e .

> [!NOTE]
> Como uma prática recomendada, chame `HubConnection` o `on`método [de início](/javascript/api/%40aspnet/signalr/hubconnection#start) no after . Isso garante que seus manipuladores estejam registrados antes que todas as mensagens sejam recebidas.

## <a name="error-handling-and-logging"></a>Registro em log e tratamento de erros

Acorrente um `catch` método `start` até o final do método para lidar com erros do lado do cliente. Use `console.error` para output erros no console do navegador.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Configurar o rastreamento de log do lado do cliente passando por um logger e tipo de evento para registrar quando a conexão é feita. As mensagens são registradas com o nível de registro especificado e superior. Os níveis de registro disponíveis são os seguintes:

* `signalR.LogLevel.Error`&ndash; Mensagens de erro. Registra `Error` apenas mensagens.
* `signalR.LogLevel.Warning`&ndash; Mensagens de aviso sobre possíveis erros. Logs `Warning`e `Error` mensagens.
* `signalR.LogLevel.Information`&ndash; Mensagens de status sem erros. Logs `Information` `Warning`e `Error` mensagens.
* `signalR.LogLevel.Trace`&ndash; Rastreie mensagens. Registra tudo, incluindo dados transportados entre hub e cliente.

Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log. As mensagens são registradas no console do navegador.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Reconecte clientes

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconecte-se automaticamente

O cliente JavaScript para SignalR pode ser configurado `withAutomaticReconnect` para reconectar automaticamente usando o método no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ele não se reconectará automaticamente por padrão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Sem quaisquer `withAutomaticReconnect()` parâmetros, configura o cliente para esperar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas fracassadas.

Antes de iniciar qualquer `HubConnection` tentativa de `HubConnectionState.Reconnecting` reconexão, `onreconnecting` a vontade de fazer `Disconnected` a transição `onclose` para o estado `HubConnection` e disparar seus retornos de chamada em vez de fazer a transição para o estado e acionar seus retornos de chamada como um sem reconexão automática configurada. Isso oferece uma oportunidade para avisar os usuários de que a conexão foi perdida e desativar elementos de IU.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Se o cliente se reconectar com sucesso `HubConnection` em suas `Connected` quatro primeiras `onreconnected` tentativas, o fará a transição de volta para o estado e disparará seus retornos de chamada. Isso oferece uma oportunidade de informar aos usuários que a conexão foi restabelecida.

Uma vez que a conexão parece `connectionId` inteiramente nova para `onreconnected` o servidor, uma nova será fornecida ao retorno de chamada.

> [!WARNING]
> O `onreconnected` parâmetro do `connectionId` retorno de chamada será `HubConnection` indefinido se o foi configurado para pular a [negociação](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`não configurará as `HubConnection` falhas iniciais de inicializar para tentar novamente, portanto, as falhas de início precisam ser tratadas manualmente:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Se o cliente não se reconectar com sucesso `HubConnection` em suas `Disconnected` quatro primeiras tentativas, a transição para o estado e dispara seus retornos de chamada [em close.](/javascript/api/%40aspnet/signalr/hubconnection#onclose) Isso oferece uma oportunidade de informar aos usuários que a conexão foi perdida permanentemente e recomendar a atualização da página:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Para configurar um número personalizado de tentativas de reconexão antes `withAutomaticReconnect` de desconectar ou alterar o tempo de reconexão, aceita uma matriz de números representando o atraso em milissegundos para esperar antes de iniciar cada tentativa de reconexão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

O exemplo anterior `HubConnection` configura o início de tentativa de reconexão imediatamente após a conexão ser perdida. Isso também é verdade para a configuração padrão.

Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente em vez de esperar 2 segundos como seria na configuração padrão.

Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão começará em 10 segundos, o que é novamente como a configuração padrão.

O comportamento personalizado então diverge novamente do comportamento padrão, parando após a terceira falha de tentativa de reconexão em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.

Se você quiser ainda mais controle sobre o tempo `withAutomaticReconnect` e o número `IRetryPolicy` de tentativas automáticas de `nextRetryDelayInMilliseconds`reconexão, aceita um objeto implementando a interface, que tem um único método chamado .

`nextRetryDelayInMilliseconds`tem um único argumento `RetryContext`com o tipo . As `RetryContext` três `previousRetryCount`propriedades: `elapsedMilliseconds` `retryReason` , e `number`que `number` são `Error` a , a e uma respectivamente. Antes da primeira tentativa `previousRetryCount` de `elapsedMilliseconds` reconexão, `retryReason` ambos e será zero, e o será o Erro que fez com que a conexão fosse perdida. Após cada tentativa de `previousRetryCount` repetição falhada, `elapsedMilliseconds` será incrementada por uma, será atualizada para refletir o `retryReason` tempo gasto reconectando-se até agora em milissegundos, e o será o Erro que causou a última tentativa de reconexão a falhar.

`nextRetryDelayInMilliseconds`deve retornar um número representando o número de milissegundos para `null` esperar `HubConnection` antes da próxima tentativa de reconexão ou se a devida parada de reconectar.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Alternativamente, você pode escrever código que reconectará seu cliente manualmente, conforme demonstrado em [Reconectar manualmente](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconecte manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes do 3.0, o SignalR cliente JavaScript para não se reconectar automaticamente. Você deve escrever código que reconectará seu cliente manualmente.

::: moniker-end

O código a seguir demonstra uma abordagem típica de reconexão manual:

1. Uma função (neste caso, a `start` função) é criada para iniciar a conexão.
1. Ligue `start` para a função `onclose` no manipulador de eventos da conexão.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Uma implementação do mundo real usaria um back-off exponencial ou tentaria novamente um número especificado de vezes antes de desistir.

## <a name="additional-resources"></a>Recursos adicionais

* [Referência da API de JavaScript](/javascript/api/?view=signalr-js-latest)
* [Tutorial javaScript](xref:tutorials/signalr)
* [Tutorial do WebPack e TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [.NET cliente](xref:signalr/dotnet-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Solicitações de Origem Cruzada (CORS)](xref:security/cors)
* [Documentação SignalR sem servidor do Azure Service](/azure/azure-signalr/signalr-concept-serverless-development-config)
