---
title: Ligue para os serviços gRPC com o cliente .NET
author: jamesnk
description: Saiba como ligar para os serviços gRPC com o cliente .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667170"
---
# <a name="call-grpc-services-with-the-net-client"></a>Ligue para os serviços gRPC com o cliente .NET

Uma biblioteca de clientes .NET gRPC está disponível no pacote [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet. Este documento explica como:

* Configure um cliente gRPC para chamar serviços gRPC.
* Faça chamadas gRPC para métodos de streaming de servidores, streaming de clientes e bidirecionais.

## <a name="configure-grpc-client"></a>Configurar cliente gRPC

os clientes gRPC são tipos de clientes concretos gerados [a partir de * \*arquivos .proto* ](xref:grpc/basics#generated-c-assets). O cliente gRPC concreto tem métodos que se traduzem para o serviço gRPC no * \*arquivo .proto.*

Um cliente gRPC é criado a partir de um canal. Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Um canal representa uma conexão de longa duração a um serviço gRPC. Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço. Por exemplo, `HttpClient` o usado para fazer chamadas, o tamanho máximo de `GrpcChannelOptions` envio e `GrpcChannel.ForAddress`recebimento de mensagens e o registro podem ser especificados e usados com . Para obter uma lista completa de opções, consulte as opções de [configuração do cliente](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Desempenho e uso do canal e do cliente:

* Criar um canal pode ser uma operação cara. Reutilizar um canal para chamadas gRPC proporciona benefícios de desempenho.
* os clientes gRPC são criados com canais. Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.
* Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.
* Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.
* Os clientes criados a partir do canal podem fazer várias chamadas simultâneas.

`GrpcChannel.ForAddress`não é a única opção para criar um cliente gRPC. Se você está chamando os serviços gRPC de um aplicativo ASP.NET Core, considere [a integração da fábrica do cliente gRPC](xref:grpc/clientfactory). A integração `HttpClientFactory` gRPC com oferece uma alternativa centralizada para a criação de clientes gRPC.

> [!NOTE]
> A configuração adicional é necessária para [chamar serviços gRPC inseguros com o cliente .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

> [!NOTE]
> A chamada gRPC sobre `Grpc.Net.Client` HTTP/2 com não é suportada no Momento em Xamarin. Estamos trabalhando para melhorar o suporte http/2 em uma futura versão xamarin. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) e [gRPC-Web](xref:grpc/browser) são alternativas viáveis que funcionam hoje.

## <a name="make-grpc-calls"></a>Faça chamadas gRPC

Uma chamada gRPC é iniciada chamando um método no cliente. O cliente gRPC lidará com a serialização de mensagens e abordará a chamada gRPC para o serviço correto.

o gRPC possui diferentes tipos de métodos. A forma como você usa o cliente para fazer uma chamada gRPC depende do tipo de método que você está chamando. Os tipos de método gRPC são:

* Unário
* Streaming de servidor
* Streaming de clientes
* Streaming bidirecional

### <a name="unary-call"></a>Chamada unary

Uma chamada não-ary começa com o cliente enviando uma mensagem de solicitação. Uma mensagem de resposta é retornada quando o serviço termina.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Cada método de serviço não-ary no arquivo * \*.proto* resultará em dois métodos .NET no tipo de cliente gRPC concreto para chamar o método: um método assíncrono e um método de bloqueio. Por exemplo, `GreeterClient` há duas maneiras de chamar: `SayHello`

* `GreeterClient.SayHelloAsync`- `Greeter.SayHello` chama o serviço assíncronamente. Pode ser aguardado.
* `GreeterClient.SayHello`- `Greeter.SayHello` chama serviço e bloqueia até completar. Não use em código assíncrono.

### <a name="server-streaming-call"></a>Chamada de streaming de servidor

Uma chamada de streaming de servidor começa com o cliente enviando uma mensagem de solicitação. `ResponseStream.MoveNext()`lê mensagens transmitidas do serviço. A chamada de streaming `ResponseStream.MoveNext()` `false`do servidor é concluída quando retorna .

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

Se você estiver usando C# `await foreach` 8 ou posterior, a sintaxe pode ser usada para ler mensagens. O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de resposta:

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a>Chamada de streaming do cliente

Uma chamada de streaming do cliente começa *sem* que o cliente envie uma mensagem. O cliente pode optar por `RequestStream.WriteAsync`enviar mensagens com . Quando o cliente terminar `RequestStream.CompleteAsync` de enviar mensagens deve ser chamado para notificar o serviço. A chamada é concluída quando o serviço retorna uma mensagem de resposta.

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a>Chamada de streaming bidirecional

Uma chamada de streaming bidirecional começa *sem* que o cliente envie uma mensagem. O cliente pode optar por `RequestStream.WriteAsync`enviar mensagens com . As mensagens transmitidas do `ResponseStream.MoveNext()` serviço `ResponseStream.ReadAllAsync()`são acessíveis com ou . A chamada de streaming bidirecional `ResponseStream` está completa quando não há mais mensagens.

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens um para o outro a qualquer momento. A melhor lógica do cliente para interagir com uma chamada bidirecional varia dependendo da lógica de serviço.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
