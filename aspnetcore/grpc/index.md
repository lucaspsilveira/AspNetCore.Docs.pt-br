---
title: Introdução ao gRPC no .NET Core
author: juntaoluo
description: Saiba mais sobre os serviços de gRPC com a pilha do ASP.NET Core e o servidor Kestrel.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/index
ms.openlocfilehash: 9f3a2041059c1d890ce72ce5f2a88151253d9bd9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404178"
---
# <a name="introduction-to-grpc-on-net-core"></a>Introdução ao gRPC no .NET Core

Por [John Luo](https://github.com/juntaoluo) e [James Newton – King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) é uma estrutura de RPC (chamada de procedimento remoto) de alto desempenho independente de linguagem.

Os principais benefícios de gRPC são:
* Estrutura de RPC leve, moderna e de alto desempenho.
* Desenvolvimento da API de primeiro contrato, usando buffers de protocolo, por padrão, permitindo implementações independente de linguagem.
* As ferramentas disponíveis para várias linguagens gerarem clientes e servidores fortemente tipados.
* Dá suporte ao cliente, servidor e chamadas bi-direcionais de streaming.
* Uso de rede reduzida com a serialização binária Protobuf.

Esses benefícios tornam o gRPC ideal para:
* Microsserviços leves em que a eficiência é crítica.
* Sistemas poliglotas nos quais múltiplas linguagens são necessárias para o desenvolvimento.
* Serviços ponto a ponto em tempo real que precisam lidar com solicitações ou respostas de streaming.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>Suporte de ferramentas C# para arquivos. proto

o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API. Serviços e mensagens são definidos em arquivos * \* . proto* :

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Os tipos .NET para serviços, clientes e mensagens são gerados automaticamente com a inclusão de arquivos * \* . proto* em um projeto:

* Adicione uma referência de pacote ao pacote [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .
* Adicione arquivos * \* . proto* ao `<Protobuf>` grupo de itens.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Para obter mais informações sobre o suporte de ferramentas do gRPC, consulte <xref:grpc/basics> .

## <a name="grpc-services-on-aspnet-core"></a>serviços gRPCs no ASP.NET Core

os serviços gRPCs podem ser hospedados em ASP.NET Core. Os serviços têm integração total com recursos populares de ASP.NET Core, como registro em log, injeção de dependência (DI), autenticação e autorização.

O modelo de projeto de serviço gRPC fornece um serviço inicial:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService`herda do `GreeterBase` tipo, que é gerado a partir do `Greeter` serviço no arquivo * \* . proto* . O serviço torna-se acessível para clientes no *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Para saber mais sobre os serviços gRPCs no ASP.NET Core, consulte <xref:grpc/aspnetcore> .

## <a name="call-grpc-services-with-a-net-client"></a>Chamar serviços gRPCs com um cliente .NET

Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de arquivos * \* . proto* ](xref:grpc/basics#generated-c-assets). O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no arquivo * \* . proto* .

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Um cliente gRPC é criado usando um canal, que representa uma conexão de vida longa para um serviço gRPC. Um canal pode ser criado usando `GrpcChannel.ForAddress` .

Para obter mais informações sobre a criação de clientes e a chamada de métodos de serviço diferentes, consulte <xref:grpc/client> .

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
