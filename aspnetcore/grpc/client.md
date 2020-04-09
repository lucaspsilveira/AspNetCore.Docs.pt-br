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
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="832d9-103">Ligue para os serviços gRPC com o cliente .NET</span><span class="sxs-lookup"><span data-stu-id="832d9-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="832d9-104">Uma biblioteca de clientes .NET gRPC está disponível no pacote [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet.</span><span class="sxs-lookup"><span data-stu-id="832d9-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="832d9-105">Este documento explica como:</span><span class="sxs-lookup"><span data-stu-id="832d9-105">This document explains how to:</span></span>

* <span data-ttu-id="832d9-106">Configure um cliente gRPC para chamar serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="832d9-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="832d9-107">Faça chamadas gRPC para métodos de streaming de servidores, streaming de clientes e bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="832d9-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="832d9-108">Configurar cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="832d9-108">Configure gRPC client</span></span>

<span data-ttu-id="832d9-109">os clientes gRPC são tipos de clientes concretos gerados [a partir de \* \*arquivos .proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="832d9-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="832d9-110">O cliente gRPC concreto tem métodos que se traduzem para o serviço gRPC no \* \*arquivo .proto.\*</span><span class="sxs-lookup"><span data-stu-id="832d9-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="832d9-111">Um cliente gRPC é criado a partir de um canal.</span><span class="sxs-lookup"><span data-stu-id="832d9-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="832d9-112">Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="832d9-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="832d9-113">Um canal representa uma conexão de longa duração a um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="832d9-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="832d9-114">Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço.</span><span class="sxs-lookup"><span data-stu-id="832d9-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="832d9-115">Por exemplo, `HttpClient` o usado para fazer chamadas, o tamanho máximo de `GrpcChannelOptions` envio e `GrpcChannel.ForAddress`recebimento de mensagens e o registro podem ser especificados e usados com .</span><span class="sxs-lookup"><span data-stu-id="832d9-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="832d9-116">Para obter uma lista completa de opções, consulte as opções de [configuração do cliente](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="832d9-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="832d9-117">Desempenho e uso do canal e do cliente:</span><span class="sxs-lookup"><span data-stu-id="832d9-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="832d9-118">Criar um canal pode ser uma operação cara.</span><span class="sxs-lookup"><span data-stu-id="832d9-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="832d9-119">Reutilizar um canal para chamadas gRPC proporciona benefícios de desempenho.</span><span class="sxs-lookup"><span data-stu-id="832d9-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="832d9-120">os clientes gRPC são criados com canais.</span><span class="sxs-lookup"><span data-stu-id="832d9-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="832d9-121">Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.</span><span class="sxs-lookup"><span data-stu-id="832d9-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="832d9-122">Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="832d9-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="832d9-123">Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.</span><span class="sxs-lookup"><span data-stu-id="832d9-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="832d9-124">Os clientes criados a partir do canal podem fazer várias chamadas simultâneas.</span><span class="sxs-lookup"><span data-stu-id="832d9-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="832d9-125">`GrpcChannel.ForAddress`não é a única opção para criar um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="832d9-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="832d9-126">Se você está chamando os serviços gRPC de um aplicativo ASP.NET Core, considere [a integração da fábrica do cliente gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="832d9-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="832d9-127">A integração `HttpClientFactory` gRPC com oferece uma alternativa centralizada para a criação de clientes gRPC.</span><span class="sxs-lookup"><span data-stu-id="832d9-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="832d9-128">A configuração adicional é necessária para [chamar serviços gRPC inseguros com o cliente .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="832d9-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="832d9-129">A chamada gRPC sobre `Grpc.Net.Client` HTTP/2 com não é suportada no Momento em Xamarin.</span><span class="sxs-lookup"><span data-stu-id="832d9-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="832d9-130">Estamos trabalhando para melhorar o suporte http/2 em uma futura versão xamarin.</span><span class="sxs-lookup"><span data-stu-id="832d9-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="832d9-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) e [gRPC-Web](xref:grpc/browser) são alternativas viáveis que funcionam hoje.</span><span class="sxs-lookup"><span data-stu-id="832d9-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="832d9-132">Faça chamadas gRPC</span><span class="sxs-lookup"><span data-stu-id="832d9-132">Make gRPC calls</span></span>

<span data-ttu-id="832d9-133">Uma chamada gRPC é iniciada chamando um método no cliente.</span><span class="sxs-lookup"><span data-stu-id="832d9-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="832d9-134">O cliente gRPC lidará com a serialização de mensagens e abordará a chamada gRPC para o serviço correto.</span><span class="sxs-lookup"><span data-stu-id="832d9-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="832d9-135">o gRPC possui diferentes tipos de métodos.</span><span class="sxs-lookup"><span data-stu-id="832d9-135">gRPC has different types of methods.</span></span> <span data-ttu-id="832d9-136">A forma como você usa o cliente para fazer uma chamada gRPC depende do tipo de método que você está chamando.</span><span class="sxs-lookup"><span data-stu-id="832d9-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="832d9-137">Os tipos de método gRPC são:</span><span class="sxs-lookup"><span data-stu-id="832d9-137">The gRPC method types are:</span></span>

* <span data-ttu-id="832d9-138">Unário</span><span class="sxs-lookup"><span data-stu-id="832d9-138">Unary</span></span>
* <span data-ttu-id="832d9-139">Streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="832d9-139">Server streaming</span></span>
* <span data-ttu-id="832d9-140">Streaming de clientes</span><span class="sxs-lookup"><span data-stu-id="832d9-140">Client streaming</span></span>
* <span data-ttu-id="832d9-141">Streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="832d9-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="832d9-142">Chamada unary</span><span class="sxs-lookup"><span data-stu-id="832d9-142">Unary call</span></span>

<span data-ttu-id="832d9-143">Uma chamada não-ary começa com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="832d9-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="832d9-144">Uma mensagem de resposta é retornada quando o serviço termina.</span><span class="sxs-lookup"><span data-stu-id="832d9-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="832d9-145">Cada método de serviço não-ary no arquivo \* \*.proto\* resultará em dois métodos .NET no tipo de cliente gRPC concreto para chamar o método: um método assíncrono e um método de bloqueio.</span><span class="sxs-lookup"><span data-stu-id="832d9-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="832d9-146">Por exemplo, `GreeterClient` há duas maneiras de chamar: `SayHello`</span><span class="sxs-lookup"><span data-stu-id="832d9-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="832d9-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` chama o serviço assíncronamente.</span><span class="sxs-lookup"><span data-stu-id="832d9-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="832d9-148">Pode ser aguardado.</span><span class="sxs-lookup"><span data-stu-id="832d9-148">Can be awaited.</span></span>
* <span data-ttu-id="832d9-149">`GreeterClient.SayHello`- `Greeter.SayHello` chama serviço e bloqueia até completar.</span><span class="sxs-lookup"><span data-stu-id="832d9-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="832d9-150">Não use em código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="832d9-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="832d9-151">Chamada de streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="832d9-151">Server streaming call</span></span>

<span data-ttu-id="832d9-152">Uma chamada de streaming de servidor começa com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="832d9-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="832d9-153">`ResponseStream.MoveNext()`lê mensagens transmitidas do serviço.</span><span class="sxs-lookup"><span data-stu-id="832d9-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="832d9-154">A chamada de streaming `ResponseStream.MoveNext()` `false`do servidor é concluída quando retorna .</span><span class="sxs-lookup"><span data-stu-id="832d9-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="832d9-155">Se você estiver usando C# `await foreach` 8 ou posterior, a sintaxe pode ser usada para ler mensagens.</span><span class="sxs-lookup"><span data-stu-id="832d9-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="832d9-156">O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de resposta:</span><span class="sxs-lookup"><span data-stu-id="832d9-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="832d9-157">Chamada de streaming do cliente</span><span class="sxs-lookup"><span data-stu-id="832d9-157">Client streaming call</span></span>

<span data-ttu-id="832d9-158">Uma chamada de streaming do cliente começa *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="832d9-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="832d9-159">O cliente pode optar por `RequestStream.WriteAsync`enviar mensagens com .</span><span class="sxs-lookup"><span data-stu-id="832d9-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="832d9-160">Quando o cliente terminar `RequestStream.CompleteAsync` de enviar mensagens deve ser chamado para notificar o serviço.</span><span class="sxs-lookup"><span data-stu-id="832d9-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="832d9-161">A chamada é concluída quando o serviço retorna uma mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="832d9-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="832d9-162">Chamada de streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="832d9-162">Bi-directional streaming call</span></span>

<span data-ttu-id="832d9-163">Uma chamada de streaming bidirecional começa *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="832d9-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="832d9-164">O cliente pode optar por `RequestStream.WriteAsync`enviar mensagens com .</span><span class="sxs-lookup"><span data-stu-id="832d9-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="832d9-165">As mensagens transmitidas do `ResponseStream.MoveNext()` serviço `ResponseStream.ReadAllAsync()`são acessíveis com ou .</span><span class="sxs-lookup"><span data-stu-id="832d9-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="832d9-166">A chamada de streaming bidirecional `ResponseStream` está completa quando não há mais mensagens.</span><span class="sxs-lookup"><span data-stu-id="832d9-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="832d9-167">Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens um para o outro a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="832d9-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="832d9-168">A melhor lógica do cliente para interagir com uma chamada bidirecional varia dependendo da lógica de serviço.</span><span class="sxs-lookup"><span data-stu-id="832d9-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="832d9-169">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="832d9-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
