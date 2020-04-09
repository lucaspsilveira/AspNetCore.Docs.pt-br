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
# <a name="grpc-client-factory-integration-in-net-core"></a>integração de fábrica do cliente gRPC no .NET Core

A integração `HttpClientFactory` gRPC com oferece uma maneira centralizada de criar clientes gRPC. Ele pode ser usado como uma alternativa à [configuração de instâncias de cliente sionidos gRPC](xref:grpc/client). A integração de fábrica está disponível no pacote [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.

A fábrica oferece os seguintes benefícios:

* Fornece um local central para configurar instâncias lógicas do cliente gRPC
* Gerencia a vida útil do subjacente`HttpClientMessageHandler`
* Propagação automática de prazo e cancelamento em um ASP.NET serviço Core gRPC

## <a name="register-grpc-clients"></a>Registre clientes gRPC

Para registrar um cliente gRPC, o método de extensão genérico `AddGrpcClient` pode ser usado dentro, `Startup.ConfigureServices`especificando a classe cliente digitada gRPC e o endereço do serviço:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

O tipo de cliente gRPC é registrado como transitório com injeção de dependência (DI). O cliente agora pode ser injetado e consumido diretamente em tipos criados por DI. ASP.NET controladores, SignalR hubs e serviços gRPC do Core são lugares onde os clientes gRPC podem ser injetados automaticamente:

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

## <a name="configure-httpclient"></a>Configurar httpclient

`HttpClientFactory`cria `HttpClient` o usado pelo cliente gRPC. Os `HttpClientFactory` métodos padrão podem ser usados para adicionar middleware `HttpClientHandler` de `HttpClient`solicitação de saída ou para configurar o subjacente do :

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

Para obter mais informações, consulte [Fazer solicitações HTTP usando IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configurar canal e interceptadores

os métodos específicos do gRPC estão disponíveis para:

* Configure o canal subjacente de um cliente gRPC.
* Adicione `Interceptor` instâncias que o cliente usará ao fazer chamadas gRPC.

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

## <a name="deadline-and-cancellation-propagation"></a>Propagação de prazos e cancelamentos

Os clientes gRPC criados pela fábrica em um `EnableCallContextPropagation()` serviço gRPC podem ser configurados para propagar automaticamente o prazo e o token de cancelamento para chamadas infantis. O `EnableCallContextPropagation()` método de extensão está disponível no pacote [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.

A propagação do contexto de chamada funciona lendo o token de prazo e cancelamento do contexto atual de solicitação gRPC e propagando-os automaticamente para chamadas de saída feitas pelo cliente gRPC. A propagação do contexto de chamada é uma excelente forma de garantir que cenários complexos e aninhados de gRPC sempre propagaro prazo e cancelamento.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Para obter mais informações sobre prazos e cancelamento de RPC, consulte [o ciclo de vida da RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
