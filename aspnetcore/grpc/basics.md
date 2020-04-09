---
title: Serviços do gRPC com C#
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPC com C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664202"
---
# <a name="grpc-services-with-c"></a>serviços gRPC com C\#

Este documento descreve os conceitos necessários para escrever aplicativos [gRPC](https://grpc.io/docs/guides/) em C#. Os tópicos aqui abordados aplicam-se tanto aos aplicativos gRPC baseados em [C-core](https://grpc.io/blog/grpc-stacks)quanto ASP.NET baseados em Core.

## <a name="proto-file"></a>proto arquivo

o gRPC utiliza uma abordagem de contrato-primeiro para o desenvolvimento de API. Buffers de protocolo (protobuf) são usados como iDL (Interface Design Language) por padrão. O arquivo * \*.proto* contém:

* A definição do serviço gRPC.
* As mensagens enviadas entre clientes e servidores.

Para obter mais informações sobre a sintaxe dos arquivos protobuf, consulte a [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Por exemplo, considere o arquivo *greet.proto* usado em [Iniciar com o serviço gRPC](xref:tutorials/grpc/grpc-start):

* Define um `Greeter` serviço.
* O `Greeter` serviço define `SayHello` uma chamada.
* `SayHello`envia `HelloRequest` uma mensagem `HelloReply` e recebe uma mensagem:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Adicione um arquivo .proto\# a um aplicativo C

O * \*arquivo .proto* está incluído em um `<Protobuf>` projeto adicionando-o ao grupo de itens:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C# Suporte de ferramentas para arquivos .proto

O pacote de ferramentas [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessário para gerar os ativos C# a partir de * \*arquivos .proto.* Os ativos gerados (arquivos):

* São gerados conforme a necessidade cada vez que o projeto é construído.
* Não são adicionados ao projeto ou verificados no controle de origem.
* São um artefato de construção contido no *diretório obj.*

Este pacote é exigido tanto pelo servidor quanto pelos projetos do cliente. O `Grpc.AspNetCore` metapacote inclui uma `Grpc.Tools`referência a . Os projetos `Grpc.AspNetCore` do servidor podem adicionar usando o `<PackageReference>` Gerenciador de pacotes no Visual Studio ou adicionando um ao arquivo do projeto:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Os projetos do `Grpc.Tools` cliente devem fazer referência direta aos outros pacotes necessários para usar o cliente gRPC. O pacote de ferramentas não é necessário em tempo de `PrivateAssets="All"`execução, de modo que a dependência é marcada com:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Ativos C# gerados

O pacote de ferramentas gera os tipos C# representando as mensagens definidas nos arquivos * \*.proto* incluídos.

Para ativos do lado do servidor, um tipo de base de serviço abstrato é gerado. O tipo base contém as definições de todas as chamadas gRPC contidas no arquivo *.proto.* Crie uma implementação concreta de serviço que deriva desse tipo de base e implemente a lógica para as chamadas gRPC. Para `greet.proto`o , o exemplo descrito `GreeterBase` anteriormente, `SayHello` um tipo abstrato que contém um método virtual é gerado. Uma implementação `GreeterService` concreta substitui o método e implementa a lógica de manuseio da chamada gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Para ativos do lado do cliente, um tipo de cliente concreto é gerado. As chamadas gRPC no arquivo *.proto* são traduzidas em métodos sobre o tipo concreto, que podem ser chamados. Para `greet.proto`o , o exemplo descrito `GreeterClient` anteriormente, um tipo de concreto é gerado. Chamada `GreeterClient.SayHelloAsync` para iniciar uma chamada gRPC para o servidor.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Por padrão, os ativos do servidor e do cliente `<Protobuf>` são gerados para cada * \*arquivo .proto* incluído no grupo de itens. Para garantir que apenas os ativos do `GrpcServices` servidor sejam `Server`gerados em um projeto de servidor, o atributo é definido como .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Da mesma forma, o `Client` atributo é definido em projetos de clientes.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
