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
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="dbba7-103">Migrar serviços do gRPC do C Core para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbba7-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="dbba7-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="dbba7-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="dbba7-105">Devido à implementação da pilha subjacente, nem todos os recursos funcionam da mesma forma entre aplicativos [gRPC baseados em C-core](https://grpc.io/blog/grpc-stacks) e ASP.NET aplicativos baseados em Core.</span><span class="sxs-lookup"><span data-stu-id="dbba7-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="dbba7-106">Este documento destaca as principais diferenças para a migração entre as duas pilhas.</span><span class="sxs-lookup"><span data-stu-id="dbba7-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="dbba7-107">vida útil da implementação do serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="dbba7-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="dbba7-108">Na pilha ASP.NET Core, os serviços gRPC, por padrão, são criados com uma [vida útil escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="dbba7-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="dbba7-109">Em contraste, o gRPC C-core por padrão se liga a um serviço com uma [vida útil de singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="dbba7-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="dbba7-110">Uma vida útil escopo permite que a implementação do serviço resolva outros serviços com vidas escopo.</span><span class="sxs-lookup"><span data-stu-id="dbba7-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="dbba7-111">Por exemplo, uma vida útil `DbContext` escopo também pode resolver a partir do recipiente DI através da injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="dbba7-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="dbba7-112">Usando vida útil escopo:</span><span class="sxs-lookup"><span data-stu-id="dbba7-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="dbba7-113">Uma nova instância da implementação do serviço é construída para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="dbba7-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="dbba7-114">Não é possível compartilhar estado entre solicitações via membros de instância sobre o tipo de implementação.</span><span class="sxs-lookup"><span data-stu-id="dbba7-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="dbba7-115">A expectativa é armazenar estados compartilhados em um serviço de singleton no contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="dbba7-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="dbba7-116">Os estados compartilhados armazenados são resolvidos na construtora da implementação do serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="dbba7-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="dbba7-117">Para obter mais informações sobre <xref:fundamentals/dependency-injection#service-lifetimes>a vida útil do serviço, consulte .</span><span class="sxs-lookup"><span data-stu-id="dbba7-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="dbba7-118">Adicionar um serviço singleton</span><span class="sxs-lookup"><span data-stu-id="dbba7-118">Add a singleton service</span></span>

<span data-ttu-id="dbba7-119">Para facilitar a transição de uma implementação gRPC C-core para ASP.NET Core, é possível alterar a vida útil da implementação do serviço de escopo para singleton.</span><span class="sxs-lookup"><span data-stu-id="dbba7-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="dbba7-120">Isso envolve adicionar uma instância da implementação do serviço ao contêiner DI:</span><span class="sxs-lookup"><span data-stu-id="dbba7-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="dbba7-121">No entanto, uma implementação de serviço com uma vida útil de singleton não é mais capaz de resolver serviços escopo através de injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="dbba7-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="dbba7-122">Configurar opções de serviços gRPC</span><span class="sxs-lookup"><span data-stu-id="dbba7-122">Configure gRPC services options</span></span>

<span data-ttu-id="dbba7-123">Em aplicativos baseados em C-core, `grpc.max_send_message_length` configurações `ChannelOption` como `grpc.max_receive_message_length` e são configuradas ao [construir a instância do servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="dbba7-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="dbba7-124">Em ASP.NET Core, o gRPC fornece configuração através do `GrpcServiceOptions` tipo.</span><span class="sxs-lookup"><span data-stu-id="dbba7-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="dbba7-125">Por exemplo, um serviço gRPC é o tamanho máximo `AddGrpc`da mensagem recebida pode ser configurado via .</span><span class="sxs-lookup"><span data-stu-id="dbba7-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="dbba7-126">O exemplo a `MaxReceiveMessageSize` seguir altera o padrão de 4 MB para 16 MB:</span><span class="sxs-lookup"><span data-stu-id="dbba7-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="dbba7-127">Para obter mais informações <xref:grpc/configuration>sobre a configuração, consulte .</span><span class="sxs-lookup"><span data-stu-id="dbba7-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="dbba7-128">Registro em log</span><span class="sxs-lookup"><span data-stu-id="dbba7-128">Logging</span></span>

<span data-ttu-id="dbba7-129">Os aplicativos baseados em `GrpcEnvironment` C-core dependem do para [configurar o logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) para fins de depuração.</span><span class="sxs-lookup"><span data-stu-id="dbba7-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="dbba7-130">A ASP.NET Pilha Core fornece essa funcionalidade através da [API de registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="dbba7-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="dbba7-131">Por exemplo, um logger pode ser adicionado ao serviço gRPC via injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="dbba7-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="dbba7-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="dbba7-132">HTTPS</span></span>

<span data-ttu-id="dbba7-133">Os aplicativos baseados em C-core configuram HTTPS através da [propriedade Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="dbba7-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="dbba7-134">Um conceito semelhante é usado para configurar servidores no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbba7-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="dbba7-135">Por exemplo, o Kestrel usa [a configuração de ponto final](xref:fundamentals/servers/kestrel#endpoint-configuration) para essa funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="dbba7-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="dbba7-136">interceptores gRPC vs Middleware</span><span class="sxs-lookup"><span data-stu-id="dbba7-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="dbba7-137">ASP.NET [O middleware Core](xref:fundamentals/middleware/index) oferece funcionalidades semelhantes em comparação com interceptadores em aplicativos gRPC baseados em C-core.</span><span class="sxs-lookup"><span data-stu-id="dbba7-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="dbba7-138">ASP.NET middleware e interceptadores Core são conceitualmente semelhantes.</span><span class="sxs-lookup"><span data-stu-id="dbba7-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="dbba7-139">Ambos:</span><span class="sxs-lookup"><span data-stu-id="dbba7-139">Both:</span></span>

* <span data-ttu-id="dbba7-140">São usados para construir um gasoduto que lida com uma solicitação gRPC.</span><span class="sxs-lookup"><span data-stu-id="dbba7-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="dbba7-141">Permitir que o trabalho seja realizado antes ou depois do próximo componente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="dbba7-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="dbba7-142">Fornecer acesso `HttpContext`a:</span><span class="sxs-lookup"><span data-stu-id="dbba7-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="dbba7-143">No middleware `HttpContext` o é um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="dbba7-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="dbba7-144">Nos interceptores `HttpContext` pode ser acessado `ServerCallContext` usando o `ServerCallContext.GetHttpContext` parâmetro com o método de extensão.</span><span class="sxs-lookup"><span data-stu-id="dbba7-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="dbba7-145">Observe que este recurso é específico para interceptadores em execução no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbba7-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="dbba7-146">diferenças do interceptor gRPC em relação ao ASP.NET Core Middleware:</span><span class="sxs-lookup"><span data-stu-id="dbba7-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="dbba7-147">Interceptadores:</span><span class="sxs-lookup"><span data-stu-id="dbba7-147">Interceptors:</span></span>
  * <span data-ttu-id="dbba7-148">Operar na camada gRPC de abstração usando o [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="dbba7-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="dbba7-149">Fornecer acesso a:</span><span class="sxs-lookup"><span data-stu-id="dbba7-149">Provide access to:</span></span>
    * <span data-ttu-id="dbba7-150">A mensagem desserializada enviada para uma chamada.</span><span class="sxs-lookup"><span data-stu-id="dbba7-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="dbba7-151">A mensagem sendo devolvida da chamada antes de ser serializada.</span><span class="sxs-lookup"><span data-stu-id="dbba7-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="dbba7-152">Pode pegar e lidar com exceções lançadas de serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="dbba7-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="dbba7-153">Middleware:</span><span class="sxs-lookup"><span data-stu-id="dbba7-153">Middleware:</span></span>
  * <span data-ttu-id="dbba7-154">Funciona antes dos interceptadores gRPC.</span><span class="sxs-lookup"><span data-stu-id="dbba7-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="dbba7-155">Opera nas mensagens HTTP/2 subjacentes.</span><span class="sxs-lookup"><span data-stu-id="dbba7-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="dbba7-156">Só é possível acessar bytes dos fluxos de solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="dbba7-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbba7-157">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dbba7-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
