---
title: ASP.NET Core SignalR cliente .net
author: bradygaster
description: Informações sobre o SignalR cliente .net ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 54e86479b9f9f0acc861769f9ab78958f79acfd3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400135"
---
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR cliente .net

O ASP.NET Core SignalR biblioteca de cliente .net permite que você se comunique com SignalR hubs de aplicativos .net.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

O exemplo de código neste artigo é um aplicativo do WPF que usa o SignalR cliente .net ASP.NET Core.

## <a name="install-the-signalr-net-client-package"></a>Instalar o SignalR pacote do cliente .net

O [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)O pacote do cliente é necessário para que os clientes .net se conectem aos SignalR hubs.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para instalar a biblioteca de cliente, execute o seguinte comando na janela do **console do Gerenciador de pacotes** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Para instalar a biblioteca de cliente, execute o seguinte comando em um shell de comando:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Conectar-se a um hub

Para estabelecer uma conexão, crie uma `HubConnectionBuilder` chamada e `Build` . A URL do Hub, o protocolo, o tipo de transporte, o nível de log, os cabeçalhos e outras opções podem ser configurados durante a criação de uma conexão. Configure as opções necessárias inserindo qualquer um dos `HubConnectionBuilder` métodos no `Build` . Inicie a conexão com `StartAsync` .

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Tratar conexão perdida

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconectar automaticamente

O <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> pode ser configurado para reconectar-se automaticamente usando o `WithAutomaticReconnect` método no <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Ele não se reconectará automaticamente por padrão.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

Sem parâmetros, `WithAutomaticReconnect()` o configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.

Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o `HubConnectionState.Reconnecting` estado e acionará o `Reconnecting` evento.  Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário. Aplicativos não interativos podem começar a enfileirar ou descartar mensagens.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o `Connected` estado e acionará o `Reconnected` evento. Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida e remover todas as mensagens na fila.

Como a conexão é totalmente nova no servidor, um novo `ConnectionId` será fornecido para os `Reconnected` manipuladores de eventos.

> [!WARNING]
> O `Reconnected` parâmetro do manipulador de eventos `connectionId` será NULL se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()`não configurará o `HubConnection` para tentar falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Disconnected` estado e acionará o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento. Isso oferece uma oportunidade de tentar reiniciar a conexão manualmente ou informar aos usuários que a conexão foi permanentemente perdida.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, o `WithAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconectar imediatamente após a perda da conexão. Isso também é verdadeiro para a configuração padrão.

Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.

Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.

O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira falha de tentativa de reconexão. Na configuração padrão, haveria mais uma tentativa de reconexão em outros 30 segundos.

Se você quiser ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, o `WithAutomaticReconnect` aceitará um objeto que implementa a `IRetryPolicy` interface, que tem um único método chamado `NextRetryDelay` .

`NextRetryDelay`usa um único argumento com o tipo `RetryContext` . O `RetryContext` tem três propriedades: `PreviousRetryCount` , `ElapsedTime` e `RetryReason` , que são a `long` , a `TimeSpan` e uma `Exception` respectivamente. Antes da primeira tentativa de reconexão, `PreviousRetryCount` e `ElapsedTime` será zero, e `RetryReason` será a exceção que causou a perda da conexão. Após cada tentativa de repetição com falha, o `PreviousRetryCount` será incrementado em um, `ElapsedTime` será atualizado para refletir a quantidade de tempo gasto reconectando até o momento, e o `RetryReason` será a exceção que causou a falha da última tentativa de reconexão.

`NextRetryDelay`deve retornar um TimeSpan que representa o tempo de espera antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de reconectar.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconectar manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes do 3,0, o cliente .NET do SignalR não se reconecta automaticamente. Você deve escrever um código que reconectará o cliente manualmente.

::: moniker-end

Use o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento para responder a uma conexão perdida. Por exemplo, talvez você queira automatizar a reconexão.

O `Closed` evento requer um delegado que retorne um `Task` , que permite que o código assíncrono seja executado sem usar o `async void` . Para satisfazer a assinatura de delegado em um `Closed` manipulador de eventos executado de forma síncrona, retorne `Task.CompletedTask` :

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

O principal motivo para o suporte assíncrono é que você pode reiniciar a conexão. Iniciar uma conexão é uma ação assíncrona.

Em um `Closed` manipulador que reinicia a conexão, considere aguardar um atraso aleatório para evitar sobrecarregar o servidor, conforme mostrado no exemplo a seguir:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Métodos do hub de chamadas do cliente

`InvokeAsync`chama métodos no Hub. Passe o nome do método de Hub e quaisquer argumentos definidos no método de Hub para `InvokeAsync` . SignalRé assíncrona, portanto, use `async` e `await` ao fazer as chamadas.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

O `InvokeAsync` método retorna um `Task` que é concluído quando o método de servidor retorna. O valor de retorno, se houver, é fornecido como o resultado do `Task` . Todas as exceções geradas pelo método no servidor produzem uma falha `Task` . Use a `await` sintaxe para aguardar a conclusão do método de servidor e a `try...catch` sintaxe para lidar com erros.

O `SendAsync` método retorna um `Task` que é concluído quando a mensagem é enviada ao servidor. Nenhum valor de retorno é fornecido, pois isso `Task` não aguarda até que o método de servidor seja concluído. Todas as exceções geradas no cliente durante o envio da mensagem produzem uma falha `Task` . Use `await` e `try...catch` sintaxe para lidar com erros de envio.

> [!NOTE]
> Se você estiver usando SignalR o serviço do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente. Para obter mais informações, consulte a [ SignalR documentação do serviço](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do Hub

Defina os métodos que o Hub chama usando `connection.On` após a compilação, mas antes de iniciar a conexão.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

O código anterior em `connection.On` é executado quando o código do lado do servidor o chama usando o `SendAsync` método.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Registro em log e tratamento de erros

Tratar erros com uma instrução try-catch. Inspecione o `Exception` objeto para determinar a ação apropriada a ser tomada após ocorrer um erro.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Documentação sem servidor do serviço do Azure SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
