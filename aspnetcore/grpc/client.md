---
title: Chamar os serviços gRPC com o cliente .NET
author: jamesnk
description: Saiba como ligar para os serviços gRPC com o cliente .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
uid: grpc/client
ms.openlocfilehash: aefa52a5c4c66178c5978aebd4cd9b00559c7f54
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791557"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="135ee-103">Chamar os serviços gRPC com o cliente .NET</span><span class="sxs-lookup"><span data-stu-id="135ee-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="135ee-104">Uma biblioteca de clientes .NET gRPC está disponível no pacote [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet.</span><span class="sxs-lookup"><span data-stu-id="135ee-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="135ee-105">Este documento explica como:</span><span class="sxs-lookup"><span data-stu-id="135ee-105">This document explains how to:</span></span>

* <span data-ttu-id="135ee-106">Configure um cliente gRPC para chamar serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="135ee-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="135ee-107">Faça chamadas gRPC para métodos de streaming de servidores, streaming de clientes e bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="135ee-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="135ee-108">Configurar cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="135ee-108">Configure gRPC client</span></span>

<span data-ttu-id="135ee-109">os clientes gRPC são tipos de clientes concretos gerados [a partir de \* \*arquivos .proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="135ee-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="135ee-110">O cliente gRPC concreto tem métodos que se traduzem para o serviço gRPC no \* \*arquivo .proto.\*</span><span class="sxs-lookup"><span data-stu-id="135ee-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="135ee-111">Um cliente gRPC é criado a partir de um canal.</span><span class="sxs-lookup"><span data-stu-id="135ee-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="135ee-112">Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="135ee-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="135ee-113">Um canal representa uma conexão de longa duração a um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="135ee-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="135ee-114">Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço.</span><span class="sxs-lookup"><span data-stu-id="135ee-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="135ee-115">Por exemplo, `HttpClient` o usado para fazer chamadas, o tamanho máximo de `GrpcChannelOptions` envio e `GrpcChannel.ForAddress`recebimento de mensagens e o registro podem ser especificados e usados com .</span><span class="sxs-lookup"><span data-stu-id="135ee-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="135ee-116">Para obter uma lista completa de opções, consulte as opções de [configuração do cliente](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="135ee-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="135ee-117">Desempenho e uso do canal e do cliente:</span><span class="sxs-lookup"><span data-stu-id="135ee-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="135ee-118">Criar um canal pode ser uma operação cara.</span><span class="sxs-lookup"><span data-stu-id="135ee-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="135ee-119">Reutilizar um canal para chamadas gRPC proporciona benefícios de desempenho.</span><span class="sxs-lookup"><span data-stu-id="135ee-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="135ee-120">os clientes gRPC são criados com canais.</span><span class="sxs-lookup"><span data-stu-id="135ee-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="135ee-121">Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.</span><span class="sxs-lookup"><span data-stu-id="135ee-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="135ee-122">Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="135ee-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="135ee-123">Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.</span><span class="sxs-lookup"><span data-stu-id="135ee-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="135ee-124">Os clientes criados a partir do canal podem fazer várias chamadas simultâneas.</span><span class="sxs-lookup"><span data-stu-id="135ee-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="135ee-125">`GrpcChannel.ForAddress`não é a única opção para criar um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="135ee-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="135ee-126">Se chamar os serviços gRPC de um aplicativo ASP.NET Core, considere [a integração de fábrica do cliente gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="135ee-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="135ee-127">A integração `HttpClientFactory` gRPC com oferece uma alternativa centralizada para a criação de clientes gRPC.</span><span class="sxs-lookup"><span data-stu-id="135ee-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="135ee-128">A configuração adicional é necessária para [chamar serviços gRPC inseguros com o cliente .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="135ee-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="135ee-129">A chamada gRPC sobre `Grpc.Net.Client` HTTP/2 com não é suportada no Momento em Xamarin.</span><span class="sxs-lookup"><span data-stu-id="135ee-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="135ee-130">Estamos trabalhando para melhorar o suporte http/2 em uma futura versão xamarin.</span><span class="sxs-lookup"><span data-stu-id="135ee-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="135ee-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) e [gRPC-Web](xref:grpc/browser) são alternativas viáveis que funcionam hoje.</span><span class="sxs-lookup"><span data-stu-id="135ee-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="135ee-132">Faça chamadas gRPC</span><span class="sxs-lookup"><span data-stu-id="135ee-132">Make gRPC calls</span></span>

<span data-ttu-id="135ee-133">Uma chamada gRPC é iniciada chamando um método no cliente.</span><span class="sxs-lookup"><span data-stu-id="135ee-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="135ee-134">O cliente gRPC lidará com a serialização de mensagens e abordará a chamada gRPC para o serviço correto.</span><span class="sxs-lookup"><span data-stu-id="135ee-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="135ee-135">o gRPC possui diferentes tipos de métodos.</span><span class="sxs-lookup"><span data-stu-id="135ee-135">gRPC has different types of methods.</span></span> <span data-ttu-id="135ee-136">A forma como o cliente é usado para fazer uma chamada gRPC depende do tipo de método chamado.</span><span class="sxs-lookup"><span data-stu-id="135ee-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="135ee-137">Os tipos de método gRPC são:</span><span class="sxs-lookup"><span data-stu-id="135ee-137">The gRPC method types are:</span></span>

* <span data-ttu-id="135ee-138">Unário</span><span class="sxs-lookup"><span data-stu-id="135ee-138">Unary</span></span>
* <span data-ttu-id="135ee-139">Streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="135ee-139">Server streaming</span></span>
* <span data-ttu-id="135ee-140">Streaming de clientes</span><span class="sxs-lookup"><span data-stu-id="135ee-140">Client streaming</span></span>
* <span data-ttu-id="135ee-141">Streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="135ee-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="135ee-142">Chamada unary</span><span class="sxs-lookup"><span data-stu-id="135ee-142">Unary call</span></span>

<span data-ttu-id="135ee-143">Uma chamada não-ary começa com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="135ee-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="135ee-144">Uma mensagem de resposta é retornada quando o serviço termina.</span><span class="sxs-lookup"><span data-stu-id="135ee-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="135ee-145">Cada método de serviço não-ary no arquivo \* \*.proto\* resultará em dois métodos .NET no tipo de cliente gRPC concreto para chamar o método: um método assíncrono e um método de bloqueio.</span><span class="sxs-lookup"><span data-stu-id="135ee-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="135ee-146">Por exemplo, `GreeterClient` há duas maneiras de chamar: `SayHello`</span><span class="sxs-lookup"><span data-stu-id="135ee-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="135ee-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` chama o serviço assíncronamente.</span><span class="sxs-lookup"><span data-stu-id="135ee-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="135ee-148">Pode ser aguardado.</span><span class="sxs-lookup"><span data-stu-id="135ee-148">Can be awaited.</span></span>
* <span data-ttu-id="135ee-149">`GreeterClient.SayHello`- `Greeter.SayHello` chama serviço e bloqueia até completar.</span><span class="sxs-lookup"><span data-stu-id="135ee-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="135ee-150">Não use em código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="135ee-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="135ee-151">Chamada de streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="135ee-151">Server streaming call</span></span>

<span data-ttu-id="135ee-152">Uma chamada de streaming de servidor começa com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="135ee-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="135ee-153">`ResponseStream.MoveNext()`lê mensagens transmitidas do serviço.</span><span class="sxs-lookup"><span data-stu-id="135ee-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="135ee-154">A chamada de streaming `ResponseStream.MoveNext()` `false`do servidor é concluída quando retorna .</span><span class="sxs-lookup"><span data-stu-id="135ee-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="135ee-155">Ao usar c # 8 `await foreach` ou posterior, a sintaxe pode ser usada para ler mensagens.</span><span class="sxs-lookup"><span data-stu-id="135ee-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="135ee-156">O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de resposta:</span><span class="sxs-lookup"><span data-stu-id="135ee-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="135ee-157">Chamada de streaming do cliente</span><span class="sxs-lookup"><span data-stu-id="135ee-157">Client streaming call</span></span>

<span data-ttu-id="135ee-158">Uma chamada de streaming do cliente começa *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="135ee-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="135ee-159">O cliente pode optar por `RequestStream.WriteAsync`enviar mensagens com .</span><span class="sxs-lookup"><span data-stu-id="135ee-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="135ee-160">Quando o cliente terminar de `RequestStream.CompleteAsync` enviar mensagens, deve ser chamado para notificar o serviço.</span><span class="sxs-lookup"><span data-stu-id="135ee-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="135ee-161">A chamada é concluída quando o serviço retorna uma mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="135ee-161">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="135ee-162">Chamada de streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="135ee-162">Bi-directional streaming call</span></span>

<span data-ttu-id="135ee-163">Uma chamada de streaming bidirecional começa *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="135ee-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="135ee-164">O cliente pode optar por `RequestStream.WriteAsync`enviar mensagens com .</span><span class="sxs-lookup"><span data-stu-id="135ee-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="135ee-165">As mensagens transmitidas do `ResponseStream.MoveNext()` serviço `ResponseStream.ReadAllAsync()`são acessíveis com ou .</span><span class="sxs-lookup"><span data-stu-id="135ee-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="135ee-166">A chamada de streaming bidirecional `ResponseStream` está completa quando não há mais mensagens.</span><span class="sxs-lookup"><span data-stu-id="135ee-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

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
```

<span data-ttu-id="135ee-167">Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens um para o outro a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="135ee-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="135ee-168">A melhor lógica do cliente para interagir com uma chamada bidirecional varia dependendo da lógica de serviço.</span><span class="sxs-lookup"><span data-stu-id="135ee-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="135ee-169">Acesse os reboques gRPC</span><span class="sxs-lookup"><span data-stu-id="135ee-169">Access gRPC trailers</span></span>

<span data-ttu-id="135ee-170">As chamadas gRPC podem retornar reboques gRPC.</span><span class="sxs-lookup"><span data-stu-id="135ee-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="135ee-171">Os reboques gRPC são usados para fornecer metadados de nome/valor sobre uma chamada.</span><span class="sxs-lookup"><span data-stu-id="135ee-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="135ee-172">Os reboques fornecem funcionalidade semelhante aos cabeçalhos HTTP, mas são recebidos no final da chamada.</span><span class="sxs-lookup"><span data-stu-id="135ee-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="135ee-173">os reboques gRPC `GetTrailers()`são acessíveis usando, o que retorna uma coleção de metadados.</span><span class="sxs-lookup"><span data-stu-id="135ee-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="135ee-174">Os reboques são devolvidos após a resposta estar completa, portanto, você deve aguardar todas as mensagens de resposta antes de acessar os trailers.</span><span class="sxs-lookup"><span data-stu-id="135ee-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="135ee-175">Chamadas de streaming unary `ResponseAsync` e `GetTrailers()`cliente devem aguardar antes de chamar:</span><span class="sxs-lookup"><span data-stu-id="135ee-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="135ee-176">As chamadas de streaming do servidor e bidirecionais devem terminar de aguardar o fluxo de resposta antes de ligar: `GetTrailers()`</span><span class="sxs-lookup"><span data-stu-id="135ee-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="135ee-177">os reboques gRPC `RpcException`também são acessíveis a partir de .</span><span class="sxs-lookup"><span data-stu-id="135ee-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="135ee-178">Um serviço pode retornar reboques juntamente com um status gRPC não-OK.</span><span class="sxs-lookup"><span data-stu-id="135ee-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="135ee-179">Nesta situação, os reboques são recuperados da exceção lançada pelo cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="135ee-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="135ee-180">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="135ee-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
