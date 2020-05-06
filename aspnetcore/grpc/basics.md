---
title: Serviços do gRPC com C#
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774750"
---
# <a name="grpc-services-with-c"></a>serviços gRPCs com C\#

Este documento descreve os conceitos necessários para escrever aplicativos [gRPC](https://grpc.io/docs/guides/) em C#. Os tópicos abordados aqui se aplicam a aplicativos gRPC baseados em [C-Core](https://grpc.io/blog/grpc-stacks)e em ASP.NET Core.

## <a name="proto-file"></a>arquivo proto

o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API. Os buffers de protocolo (protobuf) são usados como o IDL (linguagem de design de interface) por padrão. O * \*arquivo. proto* contém:

* A definição do serviço gRPC.
* As mensagens enviadas entre clientes e servidores.

Para obter mais informações sobre a sintaxe de arquivos protobuf, consulte a [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Por exemplo, considere o arquivo *Greet. proto* usado em [introdução ao serviço gRPC](xref:tutorials/grpc/grpc-start):

* Define um `Greeter` serviço.
* O `Greeter` serviço define uma `SayHello` chamada.
* `SayHello`envia uma `HelloRequest` mensagem e recebe uma `HelloReply` mensagem:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Adicionar um arquivo. proto a um aplicativo\# C

O arquivo * \*. proto* está incluído em um projeto adicionando-o ao grupo `<Protobuf>` de itens:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>Suporte de ferramentas C# para arquivos. proto

O pacote de ferramentas [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessário para gerar os ativos do C# a partir de * \*arquivos. proto* . Os ativos gerados (arquivos):

* São gerados conforme necessário sempre que o projeto é compilado.
* Não são adicionados ao projeto ou verificados no controle do código-fonte.
* É um artefato de compilação contido no diretório *obj* .

Esse pacote é exigido pelos projetos servidor e cliente. O `Grpc.AspNetCore` metapacote inclui uma referência a `Grpc.Tools`. Projetos de servidor podem `Grpc.AspNetCore` adicionar usando o Gerenciador de pacotes no Visual Studio ou adicionando `<PackageReference>` um ao arquivo de projeto:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Projetos de cliente devem fazer `Grpc.Tools` referência direta ao lado dos outros pacotes necessários para usar o cliente gRPC. O pacote de ferramentas não é necessário em tempo de execução, portanto, a `PrivateAssets="All"`dependência é marcada com:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Ativos do C# gerados

O pacote de ferramentas gera os tipos de C# que representam as mensagens definidas nos arquivos * \*. proto* incluídos.

Para ativos do lado do servidor, um tipo de base de serviço abstrato é gerado. O tipo base contém as definições de todas as chamadas gRPC contidas no arquivo *. proto* . Crie uma implementação de serviço concreto que derive desse tipo base e implemente a lógica para as chamadas gRPC. Para o `greet.proto`, o exemplo descrito anteriormente, um tipo `GreeterBase` abstrato que contém um método `SayHello` virtual é gerado. Uma implementação `GreeterService` concreta substitui o método e implementa a lógica que manipula a chamada gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Para ativos do lado do cliente, um tipo de cliente concreto é gerado. As chamadas gRPC no arquivo *. proto* são convertidas em métodos no tipo concreto, que pode ser chamado. Para o `greet.proto`, o exemplo descrito anteriormente, um tipo `GreeterClient` concreto é gerado. Chame `GreeterClient.SayHelloAsync` para iniciar uma chamada de gRPC para o servidor.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Por padrão, os ativos do cliente e do servidor são gerados para cada `<Protobuf>` * \*arquivo. proto* incluído no grupo de itens. Para garantir que apenas os ativos do servidor sejam gerados em um projeto de `GrpcServices` servidor, o atributo `Server`é definido como.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Da mesma forma, o atributo é `Client` definido como em projetos de cliente.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
