---
title: Migrar serviços do gRPC do C Core para o ASP.NET Core
author: juntaoluo
description: Aprenda a mover um aplicativo gRPC baseado em C-core existente para ser executado em cima de ASP.NET pilha Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664132"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrar serviços do gRPC do C Core para o ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

Devido à implementação da pilha subjacente, nem todos os recursos funcionam da mesma forma entre aplicativos [gRPC baseados em C-core](https://grpc.io/blog/grpc-stacks) e ASP.NET aplicativos baseados em Core. Este documento destaca as principais diferenças para a migração entre as duas pilhas.

## <a name="grpc-service-implementation-lifetime"></a>vida útil da implementação do serviço gRPC

Na pilha ASP.NET Core, os serviços gRPC, por padrão, são criados com uma [vida útil escopo](xref:fundamentals/dependency-injection#service-lifetimes). Em contraste, o gRPC C-core por padrão se liga a um serviço com uma [vida útil de singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Uma vida útil escopo permite que a implementação do serviço resolva outros serviços com vidas escopo. Por exemplo, uma vida útil `DbContext` escopo também pode resolver a partir do recipiente DI através da injeção de construtor. Usando vida útil escopo:

* Uma nova instância da implementação do serviço é construída para cada solicitação.
* Não é possível compartilhar estado entre solicitações via membros de instância sobre o tipo de implementação.
* A expectativa é armazenar estados compartilhados em um serviço de singleton no contêiner DI. Os estados compartilhados armazenados são resolvidos na construtora da implementação do serviço gRPC.

Para obter mais informações sobre <xref:fundamentals/dependency-injection#service-lifetimes>a vida útil do serviço, consulte .

### <a name="add-a-singleton-service"></a>Adicionar um serviço singleton

Para facilitar a transição de uma implementação gRPC C-core para ASP.NET Core, é possível alterar a vida útil da implementação do serviço de escopo para singleton. Isso envolve adicionar uma instância da implementação do serviço ao contêiner DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

No entanto, uma implementação de serviço com uma vida útil de singleton não é mais capaz de resolver serviços escopo através de injeção de construtor.

## <a name="configure-grpc-services-options"></a>Configurar opções de serviços gRPC

Em aplicativos baseados em C-core, `grpc.max_send_message_length` configurações `ChannelOption` como `grpc.max_receive_message_length` e são configuradas ao [construir a instância do servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

Em ASP.NET Core, o gRPC fornece configuração através do `GrpcServiceOptions` tipo. Por exemplo, um serviço gRPC é o tamanho máximo `AddGrpc`da mensagem recebida pode ser configurado via . O exemplo a `MaxReceiveMessageSize` seguir altera o padrão de 4 MB para 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Para obter mais informações <xref:grpc/configuration>sobre a configuração, consulte .

## <a name="logging"></a>Registro em log

Os aplicativos baseados em `GrpcEnvironment` C-core dependem do para [configurar o logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) para fins de depuração. A ASP.NET Pilha Core fornece essa funcionalidade através da [API de registro](xref:fundamentals/logging/index). Por exemplo, um logger pode ser adicionado ao serviço gRPC via injeção de construtor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Os aplicativos baseados em C-core configuram HTTPS através da [propriedade Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Um conceito semelhante é usado para configurar servidores no ASP.NET Core. Por exemplo, o Kestrel usa [a configuração de ponto final](xref:fundamentals/servers/kestrel#endpoint-configuration) para essa funcionalidade.

## <a name="grpc-interceptors-vs-middleware"></a>interceptores gRPC vs Middleware

ASP.NET [O middleware Core](xref:fundamentals/middleware/index) oferece funcionalidades semelhantes em comparação com interceptadores em aplicativos gRPC baseados em C-core. ASP.NET middleware e interceptadores Core são conceitualmente semelhantes. Ambos:

* São usados para construir um gasoduto que lida com uma solicitação gRPC.
* Permitir que o trabalho seja realizado antes ou depois do próximo componente no pipeline.
* Fornecer acesso `HttpContext`a:
  * No middleware `HttpContext` o é um parâmetro.
  * Nos interceptores `HttpContext` pode ser acessado `ServerCallContext` usando o `ServerCallContext.GetHttpContext` parâmetro com o método de extensão. Observe que este recurso é específico para interceptadores em execução no ASP.NET Core.

diferenças do interceptor gRPC em relação ao ASP.NET Core Middleware:

* Interceptadores:
  * Operar na camada gRPC de abstração usando o [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Fornecer acesso a:
    * A mensagem desserializada enviada para uma chamada.
    * A mensagem sendo devolvida da chamada antes de ser serializada.
  * Pode pegar e lidar com exceções lançadas de serviços gRPC.
* Middleware:
  * Funciona antes dos interceptadores gRPC.
  * Opera nas mensagens HTTP/2 subjacentes.
  * Só é possível acessar bytes dos fluxos de solicitação e resposta.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
