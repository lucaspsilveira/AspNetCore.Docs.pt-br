---
title: Considerações de segurança no gRPC para ASP.NET Core
author: jamesnk
description: Saiba mais sobre as considerações de segurança para o gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667317"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="bc8e4-103">Considerações de segurança no gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc8e4-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="bc8e4-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="bc8e4-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="bc8e4-105">Este artigo fornece informações sobre como proteger o gRPC com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="bc8e4-106">Segurança de transporte</span><span class="sxs-lookup"><span data-stu-id="bc8e4-106">Transport security</span></span>

<span data-ttu-id="bc8e4-107">as mensagens gRPC são enviadas e recebidas usando HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="bc8e4-108">Recomendamos:</span><span class="sxs-lookup"><span data-stu-id="bc8e4-108">We recommend:</span></span>

* <span data-ttu-id="bc8e4-109">[O TLS (Transport Layer Security, segurança da camada de transporte)](https://tools.ietf.org/html/rfc5246) é usado para proteger mensagens em aplicativos gRPC de produção.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="bc8e4-110">Os serviços gRPC só devem ouvir e responder por portas protegidas.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="bc8e4-111">O TLS está configurado em Kestrel.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="bc8e4-112">Para obter mais informações sobre a configuração de pontos finais do Kestrel, consulte [configuração de ponto final do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="bc8e4-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="bc8e4-113">Exceções</span><span class="sxs-lookup"><span data-stu-id="bc8e4-113">Exceptions</span></span>

<span data-ttu-id="bc8e4-114">As mensagens de exceção são geralmente consideradas dados confidenciais que não devem ser revelados a um cliente.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="bc8e4-115">Por padrão, o gRPC não envia os detalhes de uma exceção lançada por um serviço gRPC para o cliente.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="bc8e4-116">Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="bc8e4-117">A entrega de mensagens de exceção ao cliente pode ser substituída (por exemplo, em desenvolvimento ou teste) com [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="bc8e4-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="bc8e4-118">As mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="bc8e4-119">Limites de tamanho da mensagem</span><span class="sxs-lookup"><span data-stu-id="bc8e4-119">Message size limits</span></span>

<span data-ttu-id="bc8e4-120">Mensagens recebidas para clientes e serviços gRPC são carregadas na memória.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="bc8e4-121">Os limites de tamanho da mensagem são um mecanismo para ajudar a evitar que o gRPC consuma recursos excessivos.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="bc8e4-122">O gRPC usa limites de tamanho por mensagem para gerenciar mensagens recebidas e saídas.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="bc8e4-123">Por padrão, o gRPC limita as mensagens recebidas a 4 MB.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="bc8e4-124">Não há limite para mensagens de saída.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="bc8e4-125">No servidor, os limites de mensagem gRPC podem `AddGrpc`ser configurados para todos os serviços em um aplicativo com :</span><span class="sxs-lookup"><span data-stu-id="bc8e4-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="bc8e4-126">Os limites também podem ser configurados para um serviço individual usando `AddServiceOptions<TService>`.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="bc8e4-127">Para obter mais informações sobre a configuração dos limites de tamanho da mensagem, consulte [a configuração gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="bc8e4-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="bc8e4-128">Validação do certificado do cliente</span><span class="sxs-lookup"><span data-stu-id="bc8e4-128">Client certificate validation</span></span>

<span data-ttu-id="bc8e4-129">[Os certificados do cliente](https://tools.ietf.org/html/rfc5246#section-7.4.4) são inicialmente validados quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="bc8e4-130">Por padrão, a Kestrel não realiza validação adicional do certificado de cliente de uma conexão.</span><span class="sxs-lookup"><span data-stu-id="bc8e4-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="bc8e4-131">Recomendamos que os serviços gRPC protegidos por certificados de cliente saem do pacote [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth)</span><span class="sxs-lookup"><span data-stu-id="bc8e4-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="bc8e4-132">ASP.NET autenticação da certificação Core realizará validação adicional em um certificado de cliente, incluindo:</span><span class="sxs-lookup"><span data-stu-id="bc8e4-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="bc8e4-133">O certificado tem um uso de chave estendida válido (EKU)</span><span class="sxs-lookup"><span data-stu-id="bc8e4-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="bc8e4-134">Está dentro de seu prazo de validade</span><span class="sxs-lookup"><span data-stu-id="bc8e4-134">Is within its validity period</span></span>
* <span data-ttu-id="bc8e4-135">Verifique a revogação do certificado</span><span class="sxs-lookup"><span data-stu-id="bc8e4-135">Check certificate revocation</span></span>
