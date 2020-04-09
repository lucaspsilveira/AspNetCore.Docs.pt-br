---
title: integração de fábrica do cliente gRPC no .NET Core
author: jamesnk
description: Saiba como criar clientes gRPC usando a fábrica do cliente.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667163"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="61676-103">integração de fábrica do cliente gRPC no .NET Core</span><span class="sxs-lookup"><span data-stu-id="61676-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="61676-104">A integração `HttpClientFactory` gRPC com oferece uma maneira centralizada de criar clientes gRPC.</span><span class="sxs-lookup"><span data-stu-id="61676-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="61676-105">Ele pode ser usado como uma alternativa à [configuração de instâncias de cliente sionidos gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="61676-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="61676-106">A integração de fábrica está disponível no pacote [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="61676-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="61676-107">A fábrica oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="61676-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="61676-108">Fornece um local central para configurar instâncias lógicas do cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="61676-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="61676-109">Gerencia a vida útil do subjacente`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="61676-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="61676-110">Propagação automática de prazo e cancelamento em um ASP.NET serviço Core gRPC</span><span class="sxs-lookup"><span data-stu-id="61676-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="61676-111">Registre clientes gRPC</span><span class="sxs-lookup"><span data-stu-id="61676-111">Register gRPC clients</span></span>

<span data-ttu-id="61676-112">Para registrar um cliente gRPC, o método de extensão genérico `AddGrpcClient` pode ser usado dentro, `Startup.ConfigureServices`especificando a classe cliente digitada gRPC e o endereço do serviço:</span><span class="sxs-lookup"><span data-stu-id="61676-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="61676-113">O tipo de cliente gRPC é registrado como transitório com injeção de dependência (DI).</span><span class="sxs-lookup"><span data-stu-id="61676-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="61676-114">O cliente agora pode ser injetado e consumido diretamente em tipos criados por DI.</span><span class="sxs-lookup"><span data-stu-id="61676-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="61676-115">ASP.NET controladores, SignalR hubs e serviços gRPC do Core são lugares onde os clientes gRPC podem ser injetados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="61676-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="61676-116">Configurar httpclient</span><span class="sxs-lookup"><span data-stu-id="61676-116">Configure HttpClient</span></span>

<span data-ttu-id="61676-117">`HttpClientFactory`cria `HttpClient` o usado pelo cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="61676-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="61676-118">Os `HttpClientFactory` métodos padrão podem ser usados para adicionar middleware `HttpClientHandler` de `HttpClient`solicitação de saída ou para configurar o subjacente do :</span><span class="sxs-lookup"><span data-stu-id="61676-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="61676-119">Para obter mais informações, consulte [Fazer solicitações HTTP usando IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="61676-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="61676-120">Configurar canal e interceptadores</span><span class="sxs-lookup"><span data-stu-id="61676-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="61676-121">os métodos específicos do gRPC estão disponíveis para:</span><span class="sxs-lookup"><span data-stu-id="61676-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="61676-122">Configure o canal subjacente de um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="61676-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="61676-123">Adicione `Interceptor` instâncias que o cliente usará ao fazer chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="61676-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="61676-124">Propagação de prazos e cancelamentos</span><span class="sxs-lookup"><span data-stu-id="61676-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="61676-125">Os clientes gRPC criados pela fábrica em um `EnableCallContextPropagation()` serviço gRPC podem ser configurados para propagar automaticamente o prazo e o token de cancelamento para chamadas infantis.</span><span class="sxs-lookup"><span data-stu-id="61676-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="61676-126">O `EnableCallContextPropagation()` método de extensão está disponível no pacote [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="61676-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="61676-127">A propagação do contexto de chamada funciona lendo o token de prazo e cancelamento do contexto atual de solicitação gRPC e propagando-os automaticamente para chamadas de saída feitas pelo cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="61676-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="61676-128">A propagação do contexto de chamada é uma excelente forma de garantir que cenários complexos e aninhados de gRPC sempre propagaro prazo e cancelamento.</span><span class="sxs-lookup"><span data-stu-id="61676-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="61676-129">Para obter mais informações sobre prazos e cancelamento de RPC, consulte [o ciclo de vida da RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="61676-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61676-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="61676-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
