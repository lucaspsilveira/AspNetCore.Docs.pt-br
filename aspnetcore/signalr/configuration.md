---
title: configuração SignalR do Núcleo ASP.NET
author: bradygaster
description: Saiba como configurar aplicativos SignalR ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223979"
---
# <a name="aspnet-core-signalr-configuration"></a>Configuração do ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) `AddJsonProtocol`pode ser adicionado após `Startup.ConfigureServices` [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in . O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto. A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto é um objeto que pode ser usado para configurar serialização de argumentos e valores de devolução. Para obter mais informações, consulte a [documentação System.Text.Json](/dotnet/api/system.text.json).

Como exemplo, para configurar o serializador para não alterar o invólucro de nomes de propriedade, em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Não é possível configurar a serialização JSON no cliente JavaScript no momento.

### <a name="switch-to-newtonsoftjson"></a>Mude para Newtonsoft.Json

Se você precisar `Newtonsoft.Json` de recursos que `System.Text.Json`não são suportados em , consulte [Switch para Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opções de serialização do MessagePack

A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve opções para configurar hubs SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor considerará o cliente desconectado se não tiver recebido uma mensagem (incluindo manter-se vivo) neste intervalo. Pode levar mais tempo do que esse intervalo de tempo para que o cliente seja realmente marcado desconectado, devido à forma como isso é implementado. O valor recomendado é `KeepAliveInterval` o dobro do valor.|
| `HandshakeTimeout` | 15 s | Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada. Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 s | Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente. O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos suportados por este hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub. O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais. |
| `StreamBufferCapacity` | `10` | O número máximo de itens que podem ser protegidos para fluxos de upload do cliente. Se esse limite for atingido, o processamento das invocações será bloqueado até que o servidor processe itens de fluxo.|
| `MaximumReceiveMessageSize` | 32 KB | Tamanho máximo de uma única mensagem de hub de entrada. |

As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções avançadas de configuração HTTP

Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o buffer do servidor buffers antes de aplicar backpressure. Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar backpressure, mas pode aumentar o consumo de memória. |
| `AuthorizationData` | Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor buffers antes de observar a pressão de fundo. Aumentar esse valor permite que o servidor tampone mensagens maiores mais rapidamente sem esperar a pressão de fundo, mas pode aumentar o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Veja abaixo. | Opções adicionais específicas para o transporte de votação longa. |
| `WebSockets` | Veja abaixo. | Opções adicionais específicas para o transporte de WebSockets. |

O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa. A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência. |

O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções de cliente

As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript). Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.

### <a name="configure-logging"></a>Configurar o registro em log

O registro é configurado no `ConfigureLogging` Cliente .NET usando o método. Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor. Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.

> [!NOTE]
> Para registrar provedores de registro, você deve instalar os pacotes necessários. Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.

Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet. Ligue `AddConsole` para o método de extensão:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, `configureLogging` existe um método semelhante. Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas. Os logs são gravados na janela do console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Em vez `LogLevel` de um valor, `string` você também pode fornecer um valor representando um nome de nível de log. Isso é útil ao configurar o login do SignalR em `LogLevel` ambientes onde você não tem acesso às constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

A tabela a seguir lista os níveis de registro disponíveis. O valor que `configureLogging` você fornece para definir o nível **mínimo** de registro que será registrado. As mensagens registradas neste nível, **ou os níveis listados após ela na tabela,** serão registradas.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**ou**`information` | `LogLevel.Information` |
| `warn`**ou**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.

Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).

O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro. É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro. O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript). Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Nesta versão do cliente Java, os websockets são os únicos transportes disponíveis.

No cliente Java, o transporte `withTransport` é selecionado `HttpHubConnectionBuilder`com o método no . O cliente Java é padrão ao usar o transporte WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> O cliente SignalR Java ainda não suporta retorno de transporte.

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado. No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets). Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .

No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) . Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configure opções de tempo-out e mantenha-se viva

Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |
| `HandshakeTimeout` | 15 s | Tempo para aperto de mão inicial do servidor. Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript). Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 s | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado. |

No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |
| `withHandshakeResponseTimeout` | 15 s | Tempo para aperto de mão inicial do servidor. Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento. Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado. |

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opção .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS para enviar para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP para enviar a cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais para enviar a cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | Apenas WebSockets. O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento. Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP. Não usado para conexões WebSocket. Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro. Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância. **Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP para usar ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets. Isso permite o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais de WebSocket. Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opção JavaScript | Valor Padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opção Java | Valor Padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |
| `withHeader` `withHeaders` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP. |

---

No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que pode ser adicionado após [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método. O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto. A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto `JsonSerializerSettings` é um objeto JSON.NET que pode ser usado para configurar serialização de argumentos e valores de devolução. Para obter mais informações, consulte a [documentação JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Como exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez `Startup.ConfigureServices`dos nomes padrão "camelCase", use o código a seguir em :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Não é possível configurar a serialização JSON no cliente JavaScript no momento.

### <a name="messagepack-serialization-options"></a>Opções de serialização do MessagePack

A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve opções para configurar hubs SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor considerará o cliente desconectado se não tiver recebido uma mensagem (incluindo manter-se vivo) neste intervalo. Pode levar mais tempo do que esse intervalo de tempo para que o cliente seja realmente marcado desconectado, devido à forma como isso é implementado. O valor recomendado é `KeepAliveInterval` o dobro do valor.|
| `HandshakeTimeout` | 15 s | Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada. Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 s | Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente. O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos suportados por este hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub. O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais. |

As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções avançadas de configuração HTTP

Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o servidor buffers. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode impactar negativamente o consumo de memória. |
| `AuthorizationData` | Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor buffers. Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode impactar negativamente o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Veja abaixo. | Opções adicionais específicas para o transporte de votação longa. |
| `WebSockets` | Veja abaixo. | Opções adicionais específicas para o transporte de WebSockets. |

O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa. A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência. |

O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções de cliente

As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript). Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.

### <a name="configure-logging"></a>Configurar o registro em log

O registro é configurado no `ConfigureLogging` Cliente .NET usando o método. Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor. Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.

> [!NOTE]
> Para registrar provedores de registro, você deve instalar os pacotes necessários. Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.

Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet. Ligue `AddConsole` para o método de extensão:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, `configureLogging` existe um método semelhante. Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas. Os logs são gravados na janela do console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.

Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).

O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro. É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro. O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript). Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Nesta versão do cliente Java, os websockets são os únicos transportes disponíveis.

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado. No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets). Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .

No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) . Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configure opções de tempo-out e mantenha-se viva

Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |
| `HandshakeTimeout` | 15 s | Tempo para aperto de mão inicial do servidor. Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript). Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 s | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado. |

No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |
| `withHandshakeResponseTimeout` | 15 s | Tempo para aperto de mão inicial do servidor. Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento. Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado. |

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opção .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS para enviar para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP para enviar a cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais para enviar a cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | Apenas WebSockets. O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento. Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP. Não usado para conexões WebSocket. Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro. Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância. **Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP para usar ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets. Isso permite o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais de WebSocket. Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opção JavaScript | Valor Padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opção Java | Valor Padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |
| `withHeader` `withHeaders` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP. |

---

No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que pode ser adicionado após [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método. O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto. A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto `JsonSerializerSettings` é um objeto JSON.NET que pode ser usado para configurar serialização de argumentos e valores de devolução. Para obter mais informações, consulte a [documentação JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Como exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez `Startup.ConfigureServices`dos nomes padrão "camelCase", use o código a seguir em :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Não é possível configurar a serialização JSON no cliente JavaScript no momento.

### <a name="messagepack-serialization-options"></a>Opções de serialização do MessagePack

A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve opções para configurar hubs SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 s | Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada. Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 s | Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente. O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos suportados por este hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub. O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais. |

As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções avançadas de configuração HTTP

Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o servidor buffers. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode impactar negativamente o consumo de memória. |
| `AuthorizationData` | Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor buffers. Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode impactar negativamente o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Veja abaixo. | Opções adicionais específicas para o transporte de votação longa. |
| `WebSockets` | Veja abaixo. | Opções adicionais específicas para o transporte de WebSockets. |

O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa. A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência. |

O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções de cliente

As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript). Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.

### <a name="configure-logging"></a>Configurar o registro em log

O registro é configurado no `ConfigureLogging` Cliente .NET usando o método. Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor. Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.

> [!NOTE]
> Para registrar provedores de registro, você deve instalar os pacotes necessários. Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.

Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet. Ligue `AddConsole` para o método de extensão:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, `configureLogging` existe um método semelhante. Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas. Os logs são gravados na janela do console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.

Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).

O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro. É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro. O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript). Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado. No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets). Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .

No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) . Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configure opções de tempo-out e mantenha-se viva

Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |
| `HandshakeTimeout` | 15 s | Tempo para aperto de mão inicial do servidor. Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript). Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue. |

# <a name="java"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo para atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo. O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor, para dar tempo para que os pings cheguem. |
| `withHandshakeResponseTimeout` | 15 s | Tempo para aperto de mão inicial do servidor. Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento. Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede. Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opção .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS para enviar para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP para enviar a cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais para enviar a cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | Apenas WebSockets. O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento. Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP. Não usado para conexões WebSocket. Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro. Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância. **Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP para usar ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets. Isso permite o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais de WebSocket. Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opção JavaScript | Valor Padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opção Java | Valor Padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina `true` isso para pular a etapa de negociação. **Somente suportado quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR. |
| `withHeader` `withHeaders` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP. |

---

No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
