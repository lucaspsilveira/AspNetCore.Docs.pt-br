---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPC com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667625"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="d6e28-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6e28-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="d6e28-104">Este documento mostra como começar com os serviços gRPC usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6e28-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d6e28-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d6e28-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d6e28-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6e28-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d6e28-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d6e28-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d6e28-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d6e28-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="d6e28-109">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6e28-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="d6e28-110">[Exibir ou baixar o código de amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) [(como baixar).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d6e28-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d6e28-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6e28-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d6e28-112">Consulte [Comece com os serviços gRPC](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="d6e28-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d6e28-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d6e28-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d6e28-114">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="d6e28-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="d6e28-115">Adicione serviços gRPC a um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6e28-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="d6e28-116">gRPC requer o pacote [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="d6e28-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="d6e28-117">Configurar gRPC</span><span class="sxs-lookup"><span data-stu-id="d6e28-117">Configure gRPC</span></span>

<span data-ttu-id="d6e28-118">Em *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="d6e28-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="d6e28-119">o gRPC é `AddGrpc` habilitado com o método.</span><span class="sxs-lookup"><span data-stu-id="d6e28-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="d6e28-120">Cada serviço gRPC é adicionado ao `MapGrpcService` pipeline de roteamento através do método.</span><span class="sxs-lookup"><span data-stu-id="d6e28-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d6e28-121">ASP.NET Os middlewares e recursos do Core compartilham o pipeline de roteamento, portanto, um aplicativo pode ser configurado para atender manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="d6e28-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="d6e28-122">Os manipuladores de solicitação adicionais, como os controladores MVC, trabalham em paralelo com os serviços gRPC configurados.</span><span class="sxs-lookup"><span data-stu-id="d6e28-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="d6e28-123">Configurar Kestrel</span><span class="sxs-lookup"><span data-stu-id="d6e28-123">Configure Kestrel</span></span>

<span data-ttu-id="d6e28-124">Pontos finais do Kestrel gRPC:</span><span class="sxs-lookup"><span data-stu-id="d6e28-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="d6e28-125">Requeira HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d6e28-125">Require HTTP/2.</span></span>
* <span data-ttu-id="d6e28-126">Deve ser protegido com [TLS (Transport Layer Security, segurança da camada de transporte)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="d6e28-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="d6e28-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d6e28-127">HTTP/2</span></span>

<span data-ttu-id="d6e28-128">gRPC requer HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d6e28-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="d6e28-129">gRPC para ASP.NET Core valida `HTTP/2` [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is .</span><span class="sxs-lookup"><span data-stu-id="d6e28-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="d6e28-130">O Kestrel [suporta HTTP/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="d6e28-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="d6e28-131">Os pontos finais do Kestrel são configurados para suportar conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="d6e28-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="d6e28-132">TLS</span><span class="sxs-lookup"><span data-stu-id="d6e28-132">TLS</span></span>

<span data-ttu-id="d6e28-133">Os pontos finais kestrel usados para gRPC devem ser fixados com TLS.</span><span class="sxs-lookup"><span data-stu-id="d6e28-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="d6e28-134">Em desenvolvimento, um ponto final protegido com TLS é criado automaticamente quando `https://localhost:5001` o certificado de desenvolvimento ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="d6e28-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="d6e28-135">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="d6e28-135">No configuration is required.</span></span> <span data-ttu-id="d6e28-136">Um `https` prefixo verifica se o ponto final do Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="d6e28-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="d6e28-137">Na produção, o TLS deve ser explicitamente configurado.</span><span class="sxs-lookup"><span data-stu-id="d6e28-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="d6e28-138">No exemplo de *appsettings.json* a seguir, um ponto final HTTP/2 protegido com TLS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="d6e28-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="d6e28-139">Alternativamente, os pontos finais do Kestrel podem ser configurados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d6e28-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="d6e28-140">Negociação de protocolo</span><span class="sxs-lookup"><span data-stu-id="d6e28-140">Protocol negotiation</span></span>

<span data-ttu-id="d6e28-141">O TLS é usado para mais do que garantir a comunicação.</span><span class="sxs-lookup"><span data-stu-id="d6e28-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="d6e28-142">O aperto de mão do TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto final suporta vários protocolos.</span><span class="sxs-lookup"><span data-stu-id="d6e28-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="d6e28-143">Esta negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d6e28-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="d6e28-144">Se um ponto final HTTP/2 estiver configurado sem TLS, os [protocolos ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto final devem ser definidos como `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="d6e28-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="d6e28-145">Um ponto final com vários protocolos (por exemplo) `HttpProtocols.Http1AndHttp2`não pode ser usado sem O TLS porque não há negociação.</span><span class="sxs-lookup"><span data-stu-id="d6e28-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="d6e28-146">Todas as conexões ao padrão de ponto final não seguro para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="d6e28-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="d6e28-147">Para obter mais informações sobre como ativar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto final do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="d6e28-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="d6e28-148">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="d6e28-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="d6e28-149">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="d6e28-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="d6e28-150">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="d6e28-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="d6e28-151">Integração com ASP.NET PRINCIPAIS APIs</span><span class="sxs-lookup"><span data-stu-id="d6e28-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="d6e28-152">Os serviços gRPC têm acesso total aos recursos do ASP.NET Core, como [Injeção de Dependência](xref:fundamentals/dependency-injection) (DI) e [Registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d6e28-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d6e28-153">Por exemplo, a implementação do serviço pode resolver um serviço de logger a partir do contêiner DI através do construtor:</span><span class="sxs-lookup"><span data-stu-id="d6e28-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="d6e28-154">Por padrão, a implementação do serviço gRPC pode resolver outros serviços DI com qualquer vida útil (Singleton, Scoped ou Transient).</span><span class="sxs-lookup"><span data-stu-id="d6e28-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="d6e28-155">Resolver httpcontext em métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="d6e28-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="d6e28-156">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, host, cabeçalho e reboques.</span><span class="sxs-lookup"><span data-stu-id="d6e28-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="d6e28-157">O acesso `ServerCallContext` é através do argumento passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="d6e28-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="d6e28-158">`ServerCallContext`não fornece acesso `HttpContext` total a todas as APIs ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d6e28-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="d6e28-159">O `GetHttpContext` método de extensão `HttpContext` fornece acesso total à mensagem HTTP/2 subjacente em ASP.NET APIs:</span><span class="sxs-lookup"><span data-stu-id="d6e28-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="d6e28-160">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d6e28-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
