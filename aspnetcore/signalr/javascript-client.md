---
title: ASP.NET Core SignalR cliente JavaScript
author: bradygaster
description: Visão geral de ASP.NET Core SignalR cliente JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: cb2b3ddc3eba2d6e1ea91c1e7f6715ffa9ad1b08
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756009"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR cliente JavaScript

Por [Rachel Appel](https://twitter.com/rachelappel)

A SignalR biblioteca de cliente ASP.NET Core JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Instalar o SignalR pacote do cliente

A SignalR biblioteca de cliente JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) . As seções a seguir descrevem diferentes maneiras de instalar a biblioteca de cliente.

### <a name="install-with-npm"></a>Instalar com NPM

Se estiver usando o Visual Studio, execute os seguintes comandos no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz. Para Visual Studio Code, execute os seguintes comandos do **terminal integrado**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

NPM instala o conteúdo do pacote na *pasta \\ @microsoft\signalr\dist\browser node_modules* . Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* . Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

NPM instala o conteúdo do pacote na *pasta \\ @aspnet\signalr\dist\browser node_modules* . Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* . Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .

::: moniker-end

Referencie o SignalR cliente JavaScript no `<script>` elemento. Por exemplo:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Usar uma CDN (rede de distribuição de conteúdo)

Para usar a biblioteca de cliente sem o pré-requisito NPM, faça referência a uma cópia hospedada em CDN da biblioteca de cliente. Por exemplo:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

A biblioteca de cliente está disponível no seguinte CDNs:

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

### <a name="install-with-libman"></a>Instalar com LibMan

[LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos de biblioteca de cliente específicos da biblioteca de cliente hospedada na CDN. Por exemplo, adicione apenas o arquivo JavaScript reduzidos ao projeto. Para obter detalhes sobre essa abordagem, consulte [Adicionar a SignalR biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Conectar-se a um hub

O código a seguir cria e inicia uma conexão. O nome do Hub não diferencia maiúsculas de minúsculas.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a>Conexões entre origens

Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada. No entanto, há ocasiões em que uma conexão com outro domínio é necessária.

Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão. Para permitir uma solicitação entre origens, habilite-a na `Startup` classe.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Métodos do hub de chamadas do cliente

Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). O `invoke` método aceita dois argumentos:

* O nome do método de Hub. No exemplo a seguir, o nome do método no Hub é `SendMessage` .
* Quaisquer argumentos definidos no método Hub. No exemplo a seguir, o nome do argumento é `message` . O código de exemplo usa a sintaxe de função de seta que tem suporte nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Se você estiver usando SignalR o serviço do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente. Para obter mais informações, consulte a [ SignalR documentação do serviço](/azure/azure-signalr/signalr-concept-serverless-development-config).

O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript. O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna. Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro. Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).

O `send` método retorna um JavaScript `Promise` . O `Promise` é resolvido quando a mensagem é enviada ao servidor. Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro. Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).

> [!NOTE]
> `send`O uso de não aguarda até que o servidor tenha recebido a mensagem. Consequentemente, não é possível retornar dados ou erros do servidor.

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do Hub

Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do `HubConnection` .

* O nome do método de cliente JavaScript. No exemplo a seguir, o nome do método é `ReceiveMessage` .
* Argumentos que o Hub passa para o método. No exemplo a seguir, o valor do argumento é `message` .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRdetermina qual método de cliente chamar correspondendo o nome do método e os argumentos definidos em `SendAsync` e `connection.on` .

> [!NOTE]
> Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` After `on` . Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.

## <a name="error-handling-and-logging"></a>Registro em log e tratamento de erros

Encadear um `catch` método ao final do `start` método para manipular erros do lado do cliente. Use `console.error` para gerar erros de saída para o console do navegador.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita. As mensagens são registradas com o nível de log especificado e superior. Os níveis de log disponíveis são os seguintes:

* `signalR.LogLevel.Error`: Mensagens de erro. Registra `Error` somente mensagens.
* `signalR.LogLevel.Warning`: Mensagens de aviso sobre erros potenciais. Logs `Warning` e `Error` mensagens.
* `signalR.LogLevel.Information`: Mensagens de status sem erros. Logs `Information` , `Warning` e `Error` mensagens.
* `signalR.LogLevel.Trace`: Rastrear mensagens. Registra tudo, incluindo dados transportados entre o Hub e o cliente.

Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log. As mensagens são registradas no console do navegador.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Reconectar clientes

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconectar automaticamente

O cliente JavaScript para SignalR pode ser configurado para reconectar-se automaticamente usando o `withAutomaticReconnect` método em [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ele não se reconectará automaticamente por padrão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Sem parâmetros, `withAutomaticReconnect()` o configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.

Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o `HubConnectionState.Reconnecting` estado e acionará seus `onreconnecting` retornos de chamada em vez de fazer a transição para o `Disconnected` estado e disparar seus `onclose` retornos de chamada como um `HubConnection` sem reconexão automática configurada. Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o `Connected` estado e acionará seus `onreconnected` retornos de chamada. Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida.

Como a conexão é totalmente nova no servidor, um novo `connectionId` será fornecido para o retorno de `onreconnected` chamada.

> [!WARNING]
> O `onreconnected` parâmetro do retorno de chamada `connectionId` será indefinido se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`não configurará o `HubConnection` para tentar falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:

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

Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Disconnected` estado e acionará seus retornos de chamada [fechamento](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . Isso fornece uma oportunidade de informar aos usuários que a conexão foi permanentemente perdida e recomenda atualizar a página:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, o `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconectar imediatamente após a perda da conexão. Isso também é verdadeiro para a configuração padrão.

Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.

Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.

O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira tentativa de reconexão, em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.

Se você quiser ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, o `withAutomaticReconnect` aceitará um objeto que implementa a `IRetryPolicy` interface, que tem um único método chamado `nextRetryDelayInMilliseconds` .

`nextRetryDelayInMilliseconds`usa um único argumento com o tipo `RetryContext` . O `RetryContext` tem três propriedades: `previousRetryCount` , `elapsedMilliseconds` e `retryReason` que são a `number` , a `number` e uma `Error` respectivamente. Antes da primeira tentativa de reconexão, `previousRetryCount` e `elapsedMilliseconds` será zero, e o `retryReason` será o erro que causou a perda da conexão. Após cada tentativa de repetição com falha, o `previousRetryCount` será incrementado em um, `elapsedMilliseconds` será atualizado para refletir a quantidade de tempo gasto reconectando até agora em milissegundos, e o `retryReason` será o erro que causou a falha da última tentativa de reconexão.

`nextRetryDelayInMilliseconds`deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de reconectar.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconectar manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes de 3,0, o cliente JavaScript para SignalR não se reconectar automaticamente. Você deve escrever um código que reconectará o cliente manualmente.

::: moniker-end

O código a seguir demonstra uma abordagem de reconexão manual típica:

1. Uma função (nesse caso, a `start` função) é criada para iniciar a conexão.
1. Chame a `start` função no manipulador de eventos da conexão `onclose` .

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.

## <a name="additional-resources"></a>Recursos adicionais

* [Referência da API JavaScript](/javascript/api/?view=signalr-js-latest)
* [Tutorial do JavaScript](xref:tutorials/signalr)
* [Tutorial do webpack e TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Solicitações entre origens (CORS)](xref:security/cors)
* [Documentação sem servidor do serviço do Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
