---
title: Introdução ao gRPC no .NET Core
author: juntaoluo
description: Saiba mais sobre os serviços de gRPC com a pilha do ASP.NET Core e o servidor Kestrel.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667310"
---
# <a name="introduction-to-grpc-on-net-core"></a>Introdução ao gRPC no .NET Core

Por [John Luo](https://github.com/juntaoluo) e [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) é uma estrutura de RPC (Chamada de Procedimento Remoto) de linguagem independente de alto desempenho.

Os principais benefícios de gRPC são:
* Moderna, de alto desempenho, estrutura RPC leve.
* Desenvolvimento da API de primeiro contrato, usando buffers de protocolo, por padrão, permitindo implementações independente de linguagem.
* As ferramentas disponíveis para várias linguagens gerarem clientes e servidores fortemente tipados.
* Dá suporte ao cliente, servidor e chamadas bi-direcionais de streaming.
* Uso de rede reduzida com a serialização binária Protobuf.

Esses benefícios tornam o gRPC ideal para:
* Microsserviços leves em que a eficiência é crítica.
* Sistemas poliglotas nos quais múltiplas linguagens são necessárias para o desenvolvimento.
* Serviços ponto a ponto em tempo real que precisam lidar com solicitações ou respostas de streaming.

## <a name="c-tooling-support-for-proto-files"></a>C# Suporte de ferramentas para arquivos .proto

o gRPC utiliza uma abordagem de contrato-primeiro para o desenvolvimento de API. Os serviços e as mensagens são definidos em * \*arquivos .proto:*

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

Os tipos .NET para serviços, clientes * \** e mensagens são gerados automaticamente incluindo arquivos .proto em um projeto:

* Adicione uma referência de pacote ao pacote [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
* Adicione * \*arquivos .proto* ao `<Protobuf>` grupo de itens.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Para obter mais informações sobre o <xref:grpc/basics>suporte de ferramentas gRPC, consulte .

## <a name="grpc-services-on-aspnet-core"></a>serviços gRPC no núcleo ASP.NET

Os serviços gRPC podem ser hospedados no ASP.NET Core. Os serviços têm total integração com recursos populares ASP.NET Core, como registro, injeção de dependência (DI), autenticação e autorização.

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

`GreeterService`herda do `GreeterBase` tipo, que é `Greeter` gerado a partir do serviço no * \*arquivo .proto.* O serviço é acessível aos clientes em *Startup.cs:*

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Para saber mais sobre os serviços <xref:grpc/aspnetcore>gRPC no ASP.NET Core, consulte .

## <a name="call-grpc-services-with-a-net-client"></a>Ligue para os serviços gRPC com um cliente .NET

os clientes gRPC são tipos de clientes concretos gerados [a partir de * \*arquivos .proto* ](xref:grpc/basics#generated-c-assets). O cliente gRPC concreto tem métodos que se traduzem para o serviço gRPC no * \*arquivo .proto.*

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Um cliente gRPC é criado usando um canal, o que representa uma conexão de longa duração a um serviço gRPC. Um canal pode ser `GrpcChannel.ForAddress`criado usando .

Para obter mais informações sobre como criar clientes e chamar diferentes métodos de serviço, consulte <xref:grpc/client>.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
