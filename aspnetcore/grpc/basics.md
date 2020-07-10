---
title: Serviços do gRPC com C#
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a99e5c78e291469bdce5c2b5cfa6eec86caa9735
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176292"
---
# <a name="grpc-services-with-c"></a>serviços gRPCs com C\#

Este documento descreve os conceitos necessários para escrever aplicativos [gRPC](https://grpc.io/docs/guides/) em C#. Os tópicos abordados aqui se aplicam a aplicativos gRPC baseados em [C-Core](https://grpc.io/blog/grpc-stacks)e em ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>arquivo proto

o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API. Os buffers de protocolo (protobuf) são usados como o IDL (linguagem de design de interface) por padrão. O arquivo * \* . proto* contém:

* A definição do serviço gRPC.
* As mensagens enviadas entre clientes e servidores.

Para obter mais informações sobre a sintaxe de arquivos protobuf, consulte a [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Por exemplo, considere o arquivo *Greet. proto* usado em [introdução ao serviço gRPC](xref:tutorials/grpc/grpc-start):

* Define um `Greeter` serviço.
* O `Greeter` serviço define uma `SayHello` chamada.
* `SayHello`envia uma `HelloRequest` mensagem e recebe uma `HelloReply` mensagem:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Adicionar um arquivo. proto a um \# aplicativo C

O arquivo * \* . proto* está incluído em um projeto adicionando-o ao `<Protobuf>` grupo de itens:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Por padrão, uma `<Protobuf>` referência gera um cliente concreto e uma classe base de serviço. O atributo do elemento Reference `GrpcServices` pode ser usado para limitar a geração de ativos C#. `GrpcServices`As opções válidas são:

* `Both`(padrão quando não presente)
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a>Suporte de ferramentas C# para arquivos. proto

O pacote de ferramentas [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessário para gerar os ativos do C# a partir de arquivos * \* . proto* . Os ativos gerados (arquivos):

* São gerados conforme necessário sempre que o projeto é compilado.
* Não são adicionados ao projeto ou verificados no controle do código-fonte.
* É um artefato de compilação contido no diretório *obj* .

Esse pacote é exigido pelos projetos servidor e cliente. O `Grpc.AspNetCore` metapacote inclui uma referência a `Grpc.Tools` . Projetos de servidor podem adicionar `Grpc.AspNetCore` usando o Gerenciador de pacotes no Visual Studio ou adicionando um `<PackageReference>` ao arquivo de projeto:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Projetos de cliente devem fazer referência direta ao `Grpc.Tools` lado dos outros pacotes necessários para usar o cliente gRPC. O pacote de ferramentas não é necessário em tempo de execução, portanto, a dependência é marcada com `PrivateAssets="All"` :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Ativos do C# gerados

O pacote de ferramentas gera os tipos de C# que representam as mensagens definidas nos arquivos * \* . proto* incluídos.

Para ativos do lado do servidor, um tipo de base de serviço abstrato é gerado. O tipo base contém as definições de todas as chamadas gRPC contidas no arquivo *. proto* . Crie uma implementação de serviço concreto que derive desse tipo base e implemente a lógica para as chamadas gRPC. Para o `greet.proto` , o exemplo descrito anteriormente, um `GreeterBase` tipo abstrato que contém um `SayHello` método virtual é gerado. Uma implementação concreta `GreeterService` substitui o método e implementa a lógica que manipula a chamada gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Para ativos do lado do cliente, um tipo de cliente concreto é gerado. As chamadas gRPC no arquivo *. proto* são convertidas em métodos no tipo concreto, que pode ser chamado. Para o `greet.proto` , o exemplo descrito anteriormente, um `GreeterClient` tipo concreto é gerado. Chame `GreeterClient.SayHelloAsync` para iniciar uma chamada de gRPC para o servidor.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Por padrão, os ativos do cliente e do servidor são gerados para cada arquivo * \* . proto* incluído no `<Protobuf>` grupo de itens. Para garantir que apenas os ativos do servidor sejam gerados em um projeto de servidor, o `GrpcServices` atributo é definido como `Server` .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Da mesma forma, o atributo é definido como `Client` em projetos de cliente.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
