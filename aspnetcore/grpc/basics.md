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
# <a name="grpc-services-with-c"></a><span data-ttu-id="c63c8-103">serviços gRPC com C\#</span><span class="sxs-lookup"><span data-stu-id="c63c8-103">gRPC services with C\#</span></span>

<span data-ttu-id="c63c8-104">Este documento descreve os conceitos necessários para escrever aplicativos [gRPC](https://grpc.io/docs/guides/) em C#.</span><span class="sxs-lookup"><span data-stu-id="c63c8-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="c63c8-105">Os tópicos aqui abordados aplicam-se tanto aos aplicativos gRPC baseados em [C-core](https://grpc.io/blog/grpc-stacks)quanto ASP.NET baseados em Core.</span><span class="sxs-lookup"><span data-stu-id="c63c8-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="c63c8-106">proto arquivo</span><span class="sxs-lookup"><span data-stu-id="c63c8-106">proto file</span></span>

<span data-ttu-id="c63c8-107">o gRPC utiliza uma abordagem de contrato-primeiro para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="c63c8-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="c63c8-108">Buffers de protocolo (protobuf) são usados como iDL (Interface Design Language) por padrão.</span><span class="sxs-lookup"><span data-stu-id="c63c8-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="c63c8-109">O arquivo \* \*.proto\* contém:</span><span class="sxs-lookup"><span data-stu-id="c63c8-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="c63c8-110">A definição do serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="c63c8-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="c63c8-111">As mensagens enviadas entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="c63c8-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="c63c8-112">Para obter mais informações sobre a sintaxe dos arquivos protobuf, consulte a [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="c63c8-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="c63c8-113">Por exemplo, considere o arquivo *greet.proto* usado em [Iniciar com o serviço gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="c63c8-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="c63c8-114">Define um `Greeter` serviço.</span><span class="sxs-lookup"><span data-stu-id="c63c8-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="c63c8-115">O `Greeter` serviço define `SayHello` uma chamada.</span><span class="sxs-lookup"><span data-stu-id="c63c8-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="c63c8-116">`SayHello`envia `HelloRequest` uma mensagem `HelloReply` e recebe uma mensagem:</span><span class="sxs-lookup"><span data-stu-id="c63c8-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="c63c8-117">Adicione um arquivo .proto\# a um aplicativo C</span><span class="sxs-lookup"><span data-stu-id="c63c8-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="c63c8-118">O \* \*arquivo .proto\* está incluído em um `<Protobuf>` projeto adicionando-o ao grupo de itens:</span><span class="sxs-lookup"><span data-stu-id="c63c8-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="c63c8-119">C# Suporte de ferramentas para arquivos .proto</span><span class="sxs-lookup"><span data-stu-id="c63c8-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="c63c8-120">O pacote de ferramentas [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessário para gerar os ativos C# a partir de \* \*arquivos .proto.\*</span><span class="sxs-lookup"><span data-stu-id="c63c8-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="c63c8-121">Os ativos gerados (arquivos):</span><span class="sxs-lookup"><span data-stu-id="c63c8-121">The generated assets (files):</span></span>

* <span data-ttu-id="c63c8-122">São gerados conforme a necessidade cada vez que o projeto é construído.</span><span class="sxs-lookup"><span data-stu-id="c63c8-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="c63c8-123">Não são adicionados ao projeto ou verificados no controle de origem.</span><span class="sxs-lookup"><span data-stu-id="c63c8-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="c63c8-124">São um artefato de construção contido no *diretório obj.*</span><span class="sxs-lookup"><span data-stu-id="c63c8-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="c63c8-125">Este pacote é exigido tanto pelo servidor quanto pelos projetos do cliente.</span><span class="sxs-lookup"><span data-stu-id="c63c8-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="c63c8-126">O `Grpc.AspNetCore` metapacote inclui uma `Grpc.Tools`referência a .</span><span class="sxs-lookup"><span data-stu-id="c63c8-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="c63c8-127">Os projetos `Grpc.AspNetCore` do servidor podem adicionar usando o `<PackageReference>` Gerenciador de pacotes no Visual Studio ou adicionando um ao arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="c63c8-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="c63c8-128">Os projetos do `Grpc.Tools` cliente devem fazer referência direta aos outros pacotes necessários para usar o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="c63c8-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="c63c8-129">O pacote de ferramentas não é necessário em tempo de `PrivateAssets="All"`execução, de modo que a dependência é marcada com:</span><span class="sxs-lookup"><span data-stu-id="c63c8-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="c63c8-130">Ativos C# gerados</span><span class="sxs-lookup"><span data-stu-id="c63c8-130">Generated C# assets</span></span>

<span data-ttu-id="c63c8-131">O pacote de ferramentas gera os tipos C# representando as mensagens definidas nos arquivos \* \*.proto\* incluídos.</span><span class="sxs-lookup"><span data-stu-id="c63c8-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="c63c8-132">Para ativos do lado do servidor, um tipo de base de serviço abstrato é gerado.</span><span class="sxs-lookup"><span data-stu-id="c63c8-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="c63c8-133">O tipo base contém as definições de todas as chamadas gRPC contidas no arquivo *.proto.*</span><span class="sxs-lookup"><span data-stu-id="c63c8-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="c63c8-134">Crie uma implementação concreta de serviço que deriva desse tipo de base e implemente a lógica para as chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="c63c8-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="c63c8-135">Para `greet.proto`o , o exemplo descrito `GreeterBase` anteriormente, `SayHello` um tipo abstrato que contém um método virtual é gerado.</span><span class="sxs-lookup"><span data-stu-id="c63c8-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="c63c8-136">Uma implementação `GreeterService` concreta substitui o método e implementa a lógica de manuseio da chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="c63c8-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="c63c8-137">Para ativos do lado do cliente, um tipo de cliente concreto é gerado.</span><span class="sxs-lookup"><span data-stu-id="c63c8-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="c63c8-138">As chamadas gRPC no arquivo *.proto* são traduzidas em métodos sobre o tipo concreto, que podem ser chamados.</span><span class="sxs-lookup"><span data-stu-id="c63c8-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="c63c8-139">Para `greet.proto`o , o exemplo descrito `GreeterClient` anteriormente, um tipo de concreto é gerado.</span><span class="sxs-lookup"><span data-stu-id="c63c8-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="c63c8-140">Chamada `GreeterClient.SayHelloAsync` para iniciar uma chamada gRPC para o servidor.</span><span class="sxs-lookup"><span data-stu-id="c63c8-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="c63c8-141">Por padrão, os ativos do servidor e do cliente `<Protobuf>` são gerados para cada \* \*arquivo .proto\* incluído no grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="c63c8-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="c63c8-142">Para garantir que apenas os ativos do `GrpcServices` servidor sejam `Server`gerados em um projeto de servidor, o atributo é definido como .</span><span class="sxs-lookup"><span data-stu-id="c63c8-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="c63c8-143">Da mesma forma, o `Client` atributo é definido em projetos de clientes.</span><span class="sxs-lookup"><span data-stu-id="c63c8-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="c63c8-144">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c63c8-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
