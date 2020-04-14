---
title: configuração SignalR do Núcleo ASP.NET
author: bradygaster
description: Saiba como configurar aplicativos SignalR ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228134"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="b57db-103">Configuração do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="b57db-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b57db-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b57db-105">ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b57db-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b57db-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="b57db-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b57db-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="b57db-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b57db-108">`AddJsonProtocol`pode ser adicionado após `Startup.ConfigureServices` [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in .</span><span class="sxs-lookup"><span data-stu-id="b57db-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b57db-109">O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto.</span><span class="sxs-lookup"><span data-stu-id="b57db-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b57db-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto é um objeto que pode ser usado para configurar serialização de argumentos e valores de devolução.</span><span class="sxs-lookup"><span data-stu-id="b57db-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b57db-111">Para obter mais informações, consulte a [documentação System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b57db-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b57db-112">Como exemplo, para configurar o serializador para não alterar o invólucro de nomes de propriedade, em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b57db-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="b57db-113">No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b57db-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b57db-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="b57db-115">Não é possível configurar a serialização JSON no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b57db-116">Mude para Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="b57db-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b57db-117">Se você precisar `Newtonsoft.Json` de recursos que `System.Text.Json`não são suportados em , consulte [Switch para Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b57db-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b57db-118">Opções de serialização do MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-118">MessagePack serialization options</span></span>

<span data-ttu-id="b57db-119">A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="b57db-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b57db-120">Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b57db-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-121">Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b57db-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="b57db-122">Configure server options</span></span>

<span data-ttu-id="b57db-123">A tabela a seguir descreve opções para configurar hubs SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b57db-124">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-124">Option</span></span> | <span data-ttu-id="b57db-125">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-125">Default Value</span></span> | <span data-ttu-id="b57db-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b57db-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-127">30 seconds</span></span> | <span data-ttu-id="b57db-128">O servidor considerará o cliente desconectado se não tiver recebido uma mensagem (incluindo manter-se vivo) neste intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b57db-129">Pode levar mais tempo do que esse intervalo de tempo para que o cliente seja realmente marcado desconectado, devido à forma como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="b57db-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b57db-130">O valor recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="b57db-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b57db-131">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-131">15 seconds</span></span> | <span data-ttu-id="b57db-132">Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="b57db-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b57db-133">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-134">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b57db-135">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-135">15 seconds</span></span> | <span data-ttu-id="b57db-136">Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="b57db-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b57db-137">Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b57db-138">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="b57db-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b57db-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b57db-139">All installed protocols</span></span> | <span data-ttu-id="b57db-140">Protocolos suportados por este hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-140">Protocols supported by this hub.</span></span> <span data-ttu-id="b57db-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="b57db-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b57db-142">Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b57db-143">O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b57db-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b57db-144">O número máximo de itens que podem ser protegidos para fluxos de upload do cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b57db-145">Se esse limite for atingido, o processamento das invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="b57db-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b57db-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-146">32 KB</span></span> | <span data-ttu-id="b57db-147">Tamanho máximo de uma única mensagem de hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="b57db-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b57db-148">As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .</span><span class="sxs-lookup"><span data-stu-id="b57db-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="b57db-149">As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="b57db-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b57db-150">Opções avançadas de configuração HTTP</span><span class="sxs-lookup"><span data-stu-id="b57db-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b57db-151">Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b57db-152">Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="b57db-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="b57db-153">A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b57db-154">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-154">Option</span></span> | <span data-ttu-id="b57db-155">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-155">Default Value</span></span> | <span data-ttu-id="b57db-156">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b57db-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-157">32 KB</span></span> | <span data-ttu-id="b57db-158">O número máximo de bytes recebidos do cliente que o buffer do servidor buffers antes de aplicar backpressure.</span><span class="sxs-lookup"><span data-stu-id="b57db-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b57db-159">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar backpressure, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b57db-160">Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b57db-161">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b57db-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-162">32 KB</span></span> | <span data-ttu-id="b57db-163">O número máximo de bytes enviados pelo aplicativo que o servidor buffers antes de observar a pressão de fundo.</span><span class="sxs-lookup"><span data-stu-id="b57db-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b57db-164">Aumentar esse valor permite que o servidor tampone mensagens maiores mais rapidamente sem esperar a pressão de fundo, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b57db-165">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="b57db-165">All Transports are enabled.</span></span> | <span data-ttu-id="b57db-166">Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="b57db-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b57db-167">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-167">See below.</span></span> | <span data-ttu-id="b57db-168">Opções adicionais específicas para o transporte de votação longa.</span><span class="sxs-lookup"><span data-stu-id="b57db-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b57db-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-169">See below.</span></span> | <span data-ttu-id="b57db-170">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="b57db-171">0</span><span class="sxs-lookup"><span data-stu-id="b57db-171">0</span></span> | <span data-ttu-id="b57db-172">Especifique a versão mínima do protocolo de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="b57db-173">Isso é usado para limitar os clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="b57db-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="b57db-174">O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b57db-175">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-175">Option</span></span> | <span data-ttu-id="b57db-176">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-176">Default Value</span></span> | <span data-ttu-id="b57db-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b57db-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-178">90 seconds</span></span> | <span data-ttu-id="b57db-179">A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="b57db-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b57db-180">A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="b57db-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b57db-181">O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b57db-182">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-182">Option</span></span> | <span data-ttu-id="b57db-183">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-183">Default Value</span></span> | <span data-ttu-id="b57db-184">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b57db-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-185">5 seconds</span></span> | <span data-ttu-id="b57db-186">Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="b57db-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b57db-187">Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b57db-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b57db-188">O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b57db-189">Configurar opções de cliente</span><span class="sxs-lookup"><span data-stu-id="b57db-189">Configure client options</span></span>

<span data-ttu-id="b57db-190">As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b57db-191">Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.</span><span class="sxs-lookup"><span data-stu-id="b57db-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b57db-192">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="b57db-192">Configure logging</span></span>

<span data-ttu-id="b57db-193">O registro é configurado no `ConfigureLogging` Cliente .NET usando o método.</span><span class="sxs-lookup"><span data-stu-id="b57db-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b57db-194">Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b57db-195">Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b57db-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-196">Para registrar provedores de registro, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="b57db-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b57db-197">Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="b57db-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b57db-198">Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="b57db-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b57db-199">Ligue `AddConsole` para o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b57db-200">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="b57db-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b57db-201">Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="b57db-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b57db-202">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="b57db-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b57db-203">Em vez `LogLevel` de um valor, `string` você também pode fornecer um valor representando um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="b57db-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b57db-204">Isso é útil ao configurar o login do SignalR em `LogLevel` ambientes onde você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="b57db-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b57db-205">A tabela a seguir lista os níveis de registro disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b57db-205">The following table lists the available log levels.</span></span> <span data-ttu-id="b57db-206">O valor que `configureLogging` você fornece para definir o nível **mínimo** de registro que será registrado.</span><span class="sxs-lookup"><span data-stu-id="b57db-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b57db-207">As mensagens registradas neste nível, **ou os níveis listados após ela na tabela,** serão registradas.</span><span class="sxs-lookup"><span data-stu-id="b57db-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b57db-208">String</span><span class="sxs-lookup"><span data-stu-id="b57db-208">String</span></span>                      | <span data-ttu-id="b57db-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b57db-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b57db-210">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="b57db-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b57db-211">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="b57db-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b57db-212">Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="b57db-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b57db-213">Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b57db-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b57db-214">O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b57db-215">É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b57db-216">O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="b57db-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b57db-217">Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="b57db-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b57db-218">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="b57db-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b57db-219">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b57db-219">Configure allowed transports</span></span>

<span data-ttu-id="b57db-220">Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b57db-221">Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b57db-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b57db-222">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="b57db-222">All transports are enabled by default.</span></span>

<span data-ttu-id="b57db-223">Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:</span><span class="sxs-lookup"><span data-stu-id="b57db-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b57db-224">No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b57db-225">Nesta versão do cliente Java, os websockets são os únicos transportes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b57db-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b57db-226">No cliente Java, o transporte `withTransport` é selecionado `HttpHubConnectionBuilder`com o método no .</span><span class="sxs-lookup"><span data-stu-id="b57db-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b57db-227">O cliente Java é padrão ao usar o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b57db-228">O cliente SignalR Java ainda não suporta retorno de transporte.</span><span class="sxs-lookup"><span data-stu-id="b57db-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b57db-229">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="b57db-229">Configure bearer authentication</span></span>

<span data-ttu-id="b57db-230">Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b57db-231">No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="b57db-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b57db-232">No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets).</span><span class="sxs-lookup"><span data-stu-id="b57db-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b57db-233">Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .</span><span class="sxs-lookup"><span data-stu-id="b57db-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b57db-234">No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="b57db-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b57db-235">No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :</span><span class="sxs-lookup"><span data-stu-id="b57db-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="b57db-236">No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b57db-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b57db-237">Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="b57db-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b57db-238">Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b57db-239">Configure opções de tempo-out e mantenha-se viva</span><span class="sxs-lookup"><span data-stu-id="b57db-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b57db-240">Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="b57db-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-241">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-242">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-242">Option</span></span> | <span data-ttu-id="b57db-243">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-243">Default value</span></span> | <span data-ttu-id="b57db-244">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b57db-245">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-246">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-246">Timeout for server activity.</span></span> <span data-ttu-id="b57db-247">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-248">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-249">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b57db-250">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-250">15 seconds</span></span> | <span data-ttu-id="b57db-251">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-252">Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-253">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-254">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b57db-255">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-255">15 seconds</span></span> | <span data-ttu-id="b57db-256">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-257">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-258">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b57db-259">No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="b57db-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b57db-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-261">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-261">Option</span></span> | <span data-ttu-id="b57db-262">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-262">Default value</span></span> | <span data-ttu-id="b57db-263">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b57db-264">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-265">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-265">Timeout for server activity.</span></span> <span data-ttu-id="b57db-266">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b57db-267">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-268">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b57db-269">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b57db-270">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-271">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-272">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-273">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-273">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-274">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-274">Option</span></span> | <span data-ttu-id="b57db-275">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-275">Default value</span></span> | <span data-ttu-id="b57db-276">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b57db-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b57db-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b57db-278">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-279">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-279">Timeout for server activity.</span></span> <span data-ttu-id="b57db-280">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-281">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-282">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b57db-283">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-283">15 seconds</span></span> | <span data-ttu-id="b57db-284">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-285">Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-286">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-287">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b57db-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b57db-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b57db-289">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b57db-290">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-291">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-292">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b57db-293">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-293">Configure additional options</span></span>

<span data-ttu-id="b57db-294">Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="b57db-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-295">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-296">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="b57db-296">.NET Option</span></span> |  <span data-ttu-id="b57db-297">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-297">Default value</span></span> | <span data-ttu-id="b57db-298">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b57db-299">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b57db-300">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-301">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-302">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b57db-303">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-303">Empty</span></span> | <span data-ttu-id="b57db-304">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="b57db-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b57db-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-305">Empty</span></span> | <span data-ttu-id="b57db-306">Uma coleção de cookies HTTP para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b57db-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-307">Empty</span></span> | <span data-ttu-id="b57db-308">Credenciais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b57db-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-309">5 seconds</span></span> | <span data-ttu-id="b57db-310">Apenas WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-310">WebSockets only.</span></span> <span data-ttu-id="b57db-311">O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="b57db-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b57db-312">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b57db-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b57db-313">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-313">Empty</span></span> | <span data-ttu-id="b57db-314">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b57db-315">Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b57db-316">Não usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="b57db-317">Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b57db-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b57db-318">Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="b57db-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b57db-319">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="b57db-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b57db-320">Um proxy HTTP para usar ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b57db-321">Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b57db-322">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="b57db-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b57db-323">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b57db-324">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="b57db-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b57db-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-326">Opção JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-326">JavaScript Option</span></span> | <span data-ttu-id="b57db-327">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-327">Default Value</span></span> | <span data-ttu-id="b57db-328">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b57db-329">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b57db-330">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-331">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-332">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-333">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-333">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-334">Opção Java</span><span class="sxs-lookup"><span data-stu-id="b57db-334">Java Option</span></span> | <span data-ttu-id="b57db-335">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-335">Default Value</span></span> | <span data-ttu-id="b57db-336">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b57db-337">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b57db-338">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-339">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-340">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b57db-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b57db-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b57db-342">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-342">Empty</span></span> | <span data-ttu-id="b57db-343">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b57db-344">No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="b57db-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b57db-345">No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b57db-346">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b57db-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b57db-347">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b57db-348">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b57db-349">ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b57db-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b57db-350">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="b57db-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b57db-351">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="b57db-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b57db-352">`AddJsonProtocol`pode ser adicionado após `Startup.ConfigureServices` [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in .</span><span class="sxs-lookup"><span data-stu-id="b57db-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b57db-353">O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto.</span><span class="sxs-lookup"><span data-stu-id="b57db-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b57db-354">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto é um objeto que pode ser usado para configurar serialização de argumentos e valores de devolução.</span><span class="sxs-lookup"><span data-stu-id="b57db-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b57db-355">Para obter mais informações, consulte a [documentação System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b57db-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b57db-356">Como exemplo, para configurar o serializador para não alterar o invólucro de nomes de propriedade, em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b57db-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="b57db-357">No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b57db-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b57db-358">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="b57db-359">Não é possível configurar a serialização JSON no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b57db-360">Mude para Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="b57db-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b57db-361">Se você precisar `Newtonsoft.Json` de recursos que `System.Text.Json`não são suportados em , consulte [Switch para Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b57db-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b57db-362">Opções de serialização do MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-362">MessagePack serialization options</span></span>

<span data-ttu-id="b57db-363">A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="b57db-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b57db-364">Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b57db-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-365">Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b57db-366">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="b57db-366">Configure server options</span></span>

<span data-ttu-id="b57db-367">A tabela a seguir descreve opções para configurar hubs SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b57db-368">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-368">Option</span></span> | <span data-ttu-id="b57db-369">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-369">Default Value</span></span> | <span data-ttu-id="b57db-370">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b57db-371">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-371">30 seconds</span></span> | <span data-ttu-id="b57db-372">O servidor considerará o cliente desconectado se não tiver recebido uma mensagem (incluindo manter-se vivo) neste intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b57db-373">Pode levar mais tempo do que esse intervalo de tempo para que o cliente seja realmente marcado desconectado, devido à forma como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="b57db-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b57db-374">O valor recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="b57db-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b57db-375">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-375">15 seconds</span></span> | <span data-ttu-id="b57db-376">Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="b57db-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b57db-377">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-378">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b57db-379">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-379">15 seconds</span></span> | <span data-ttu-id="b57db-380">Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="b57db-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b57db-381">Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b57db-382">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="b57db-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b57db-383">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b57db-383">All installed protocols</span></span> | <span data-ttu-id="b57db-384">Protocolos suportados por este hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-384">Protocols supported by this hub.</span></span> <span data-ttu-id="b57db-385">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="b57db-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b57db-386">Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b57db-387">O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b57db-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b57db-388">O número máximo de itens que podem ser protegidos para fluxos de upload do cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b57db-389">Se esse limite for atingido, o processamento das invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="b57db-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b57db-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-390">32 KB</span></span> | <span data-ttu-id="b57db-391">Tamanho máximo de uma única mensagem de hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="b57db-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b57db-392">As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .</span><span class="sxs-lookup"><span data-stu-id="b57db-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="b57db-393">As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="b57db-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b57db-394">Opções avançadas de configuração HTTP</span><span class="sxs-lookup"><span data-stu-id="b57db-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b57db-395">Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b57db-396">Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="b57db-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="b57db-397">A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b57db-398">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-398">Option</span></span> | <span data-ttu-id="b57db-399">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-399">Default Value</span></span> | <span data-ttu-id="b57db-400">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b57db-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-401">32 KB</span></span> | <span data-ttu-id="b57db-402">O número máximo de bytes recebidos do cliente que o buffer do servidor buffers antes de aplicar backpressure.</span><span class="sxs-lookup"><span data-stu-id="b57db-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b57db-403">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar backpressure, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b57db-404">Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b57db-405">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b57db-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-406">32 KB</span></span> | <span data-ttu-id="b57db-407">O número máximo de bytes enviados pelo aplicativo que o servidor buffers antes de observar a pressão de fundo.</span><span class="sxs-lookup"><span data-stu-id="b57db-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b57db-408">Aumentar esse valor permite que o servidor tampone mensagens maiores mais rapidamente sem esperar a pressão de fundo, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b57db-409">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="b57db-409">All Transports are enabled.</span></span> | <span data-ttu-id="b57db-410">Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="b57db-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b57db-411">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-411">See below.</span></span> | <span data-ttu-id="b57db-412">Opções adicionais específicas para o transporte de votação longa.</span><span class="sxs-lookup"><span data-stu-id="b57db-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b57db-413">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-413">See below.</span></span> | <span data-ttu-id="b57db-414">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b57db-415">O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b57db-416">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-416">Option</span></span> | <span data-ttu-id="b57db-417">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-417">Default Value</span></span> | <span data-ttu-id="b57db-418">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b57db-419">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-419">90 seconds</span></span> | <span data-ttu-id="b57db-420">A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="b57db-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b57db-421">A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="b57db-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b57db-422">O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b57db-423">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-423">Option</span></span> | <span data-ttu-id="b57db-424">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-424">Default Value</span></span> | <span data-ttu-id="b57db-425">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b57db-426">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-426">5 seconds</span></span> | <span data-ttu-id="b57db-427">Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="b57db-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b57db-428">Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b57db-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b57db-429">O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b57db-430">Configurar opções de cliente</span><span class="sxs-lookup"><span data-stu-id="b57db-430">Configure client options</span></span>

<span data-ttu-id="b57db-431">As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b57db-432">Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.</span><span class="sxs-lookup"><span data-stu-id="b57db-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b57db-433">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="b57db-433">Configure logging</span></span>

<span data-ttu-id="b57db-434">O registro é configurado no `ConfigureLogging` Cliente .NET usando o método.</span><span class="sxs-lookup"><span data-stu-id="b57db-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b57db-435">Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b57db-436">Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b57db-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-437">Para registrar provedores de registro, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="b57db-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b57db-438">Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="b57db-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b57db-439">Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="b57db-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b57db-440">Ligue `AddConsole` para o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b57db-441">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="b57db-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b57db-442">Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="b57db-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b57db-443">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="b57db-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b57db-444">Em vez `LogLevel` de um valor, `string` você também pode fornecer um valor representando um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="b57db-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b57db-445">Isso é útil ao configurar o login do SignalR em `LogLevel` ambientes onde você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="b57db-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b57db-446">A tabela a seguir lista os níveis de registro disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b57db-446">The following table lists the available log levels.</span></span> <span data-ttu-id="b57db-447">O valor que `configureLogging` você fornece para definir o nível **mínimo** de registro que será registrado.</span><span class="sxs-lookup"><span data-stu-id="b57db-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b57db-448">As mensagens registradas neste nível, **ou os níveis listados após ela na tabela,** serão registradas.</span><span class="sxs-lookup"><span data-stu-id="b57db-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b57db-449">String</span><span class="sxs-lookup"><span data-stu-id="b57db-449">String</span></span>                      | <span data-ttu-id="b57db-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b57db-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b57db-451">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="b57db-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b57db-452">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="b57db-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b57db-453">Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="b57db-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b57db-454">Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b57db-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b57db-455">O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b57db-456">É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b57db-457">O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="b57db-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b57db-458">Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="b57db-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b57db-459">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="b57db-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b57db-460">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b57db-460">Configure allowed transports</span></span>

<span data-ttu-id="b57db-461">Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b57db-462">Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b57db-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b57db-463">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="b57db-463">All transports are enabled by default.</span></span>

<span data-ttu-id="b57db-464">Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:</span><span class="sxs-lookup"><span data-stu-id="b57db-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b57db-465">No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b57db-466">Nesta versão do cliente Java, os websockets são os únicos transportes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b57db-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b57db-467">No cliente Java, o transporte `withTransport` é selecionado `HttpHubConnectionBuilder`com o método no .</span><span class="sxs-lookup"><span data-stu-id="b57db-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b57db-468">O cliente Java é padrão ao usar o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b57db-469">O cliente SignalR Java ainda não suporta retorno de transporte.</span><span class="sxs-lookup"><span data-stu-id="b57db-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b57db-470">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="b57db-470">Configure bearer authentication</span></span>

<span data-ttu-id="b57db-471">Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b57db-472">No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="b57db-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b57db-473">No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets).</span><span class="sxs-lookup"><span data-stu-id="b57db-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b57db-474">Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .</span><span class="sxs-lookup"><span data-stu-id="b57db-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b57db-475">No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="b57db-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b57db-476">No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :</span><span class="sxs-lookup"><span data-stu-id="b57db-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="b57db-477">No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b57db-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b57db-478">Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="b57db-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b57db-479">Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b57db-480">Configure opções de tempo-out e mantenha-se viva</span><span class="sxs-lookup"><span data-stu-id="b57db-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b57db-481">Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="b57db-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-482">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-483">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-483">Option</span></span> | <span data-ttu-id="b57db-484">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-484">Default value</span></span> | <span data-ttu-id="b57db-485">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b57db-486">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-487">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-487">Timeout for server activity.</span></span> <span data-ttu-id="b57db-488">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-489">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-490">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b57db-491">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-491">15 seconds</span></span> | <span data-ttu-id="b57db-492">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-493">Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-494">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-495">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b57db-496">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-496">15 seconds</span></span> | <span data-ttu-id="b57db-497">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-498">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-499">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b57db-500">No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="b57db-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b57db-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-502">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-502">Option</span></span> | <span data-ttu-id="b57db-503">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-503">Default value</span></span> | <span data-ttu-id="b57db-504">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b57db-505">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-506">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-506">Timeout for server activity.</span></span> <span data-ttu-id="b57db-507">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b57db-508">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-509">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b57db-510">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b57db-511">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-512">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-513">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-514">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-514">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-515">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-515">Option</span></span> | <span data-ttu-id="b57db-516">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-516">Default value</span></span> | <span data-ttu-id="b57db-517">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b57db-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b57db-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b57db-519">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-520">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-520">Timeout for server activity.</span></span> <span data-ttu-id="b57db-521">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-522">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-523">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b57db-524">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-524">15 seconds</span></span> | <span data-ttu-id="b57db-525">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-526">Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-527">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-528">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b57db-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b57db-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b57db-530">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b57db-531">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-532">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-533">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b57db-534">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-534">Configure additional options</span></span>

<span data-ttu-id="b57db-535">Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="b57db-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-536">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-537">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="b57db-537">.NET Option</span></span> |  <span data-ttu-id="b57db-538">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-538">Default value</span></span> | <span data-ttu-id="b57db-539">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b57db-540">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b57db-541">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-542">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-543">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b57db-544">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-544">Empty</span></span> | <span data-ttu-id="b57db-545">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="b57db-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b57db-546">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-546">Empty</span></span> | <span data-ttu-id="b57db-547">Uma coleção de cookies HTTP para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b57db-548">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-548">Empty</span></span> | <span data-ttu-id="b57db-549">Credenciais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b57db-550">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-550">5 seconds</span></span> | <span data-ttu-id="b57db-551">Apenas WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-551">WebSockets only.</span></span> <span data-ttu-id="b57db-552">O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="b57db-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b57db-553">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b57db-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b57db-554">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-554">Empty</span></span> | <span data-ttu-id="b57db-555">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b57db-556">Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b57db-557">Não usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="b57db-558">Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b57db-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b57db-559">Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="b57db-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b57db-560">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="b57db-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b57db-561">Um proxy HTTP para usar ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b57db-562">Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b57db-563">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="b57db-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b57db-564">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b57db-565">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="b57db-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b57db-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-567">Opção JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-567">JavaScript Option</span></span> | <span data-ttu-id="b57db-568">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-568">Default Value</span></span> | <span data-ttu-id="b57db-569">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b57db-570">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b57db-571">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-572">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-573">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-574">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-574">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-575">Opção Java</span><span class="sxs-lookup"><span data-stu-id="b57db-575">Java Option</span></span> | <span data-ttu-id="b57db-576">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-576">Default Value</span></span> | <span data-ttu-id="b57db-577">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b57db-578">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b57db-579">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-580">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-581">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b57db-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b57db-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b57db-583">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-583">Empty</span></span> | <span data-ttu-id="b57db-584">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b57db-585">No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="b57db-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b57db-586">No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b57db-587">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b57db-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b57db-588">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b57db-589">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b57db-590">ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b57db-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b57db-591">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="b57db-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b57db-592">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que pode ser adicionado após [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="b57db-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b57db-593">O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto.</span><span class="sxs-lookup"><span data-stu-id="b57db-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b57db-594">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto `JsonSerializerSettings` é um objeto JSON.NET que pode ser usado para configurar serialização de argumentos e valores de devolução.</span><span class="sxs-lookup"><span data-stu-id="b57db-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b57db-595">Para obter mais informações, consulte a [documentação JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b57db-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b57db-596">Como exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez `Startup.ConfigureServices`dos nomes padrão "camelCase", use o código a seguir em :</span><span class="sxs-lookup"><span data-stu-id="b57db-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b57db-597">No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b57db-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b57db-598">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="b57db-599">Não é possível configurar a serialização JSON no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b57db-600">Opções de serialização do MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-600">MessagePack serialization options</span></span>

<span data-ttu-id="b57db-601">A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="b57db-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b57db-602">Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b57db-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-603">Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b57db-604">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="b57db-604">Configure server options</span></span>

<span data-ttu-id="b57db-605">A tabela a seguir descreve opções para configurar hubs SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b57db-606">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-606">Option</span></span> | <span data-ttu-id="b57db-607">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-607">Default Value</span></span> | <span data-ttu-id="b57db-608">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b57db-609">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-609">30 seconds</span></span> | <span data-ttu-id="b57db-610">O servidor considerará o cliente desconectado se não tiver recebido uma mensagem (incluindo manter-se vivo) neste intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b57db-611">Pode levar mais tempo do que esse intervalo de tempo para que o cliente seja realmente marcado desconectado, devido à forma como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="b57db-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b57db-612">O valor recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="b57db-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b57db-613">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-613">15 seconds</span></span> | <span data-ttu-id="b57db-614">Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="b57db-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b57db-615">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-616">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b57db-617">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-617">15 seconds</span></span> | <span data-ttu-id="b57db-618">Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="b57db-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b57db-619">Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b57db-620">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="b57db-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b57db-621">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b57db-621">All installed protocols</span></span> | <span data-ttu-id="b57db-622">Protocolos suportados por este hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-622">Protocols supported by this hub.</span></span> <span data-ttu-id="b57db-623">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="b57db-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b57db-624">Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b57db-625">O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b57db-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b57db-626">As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .</span><span class="sxs-lookup"><span data-stu-id="b57db-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="b57db-627">As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="b57db-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b57db-628">Opções avançadas de configuração HTTP</span><span class="sxs-lookup"><span data-stu-id="b57db-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b57db-629">Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b57db-630">Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="b57db-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="b57db-631">A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b57db-632">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-632">Option</span></span> | <span data-ttu-id="b57db-633">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-633">Default Value</span></span> | <span data-ttu-id="b57db-634">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b57db-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-635">32 KB</span></span> | <span data-ttu-id="b57db-636">O número máximo de bytes recebidos do cliente que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="b57db-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b57db-637">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b57db-638">Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b57db-639">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b57db-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-640">32 KB</span></span> | <span data-ttu-id="b57db-641">O número máximo de bytes enviados pelo aplicativo que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="b57db-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b57db-642">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b57db-643">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="b57db-643">All Transports are enabled.</span></span> | <span data-ttu-id="b57db-644">Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="b57db-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b57db-645">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-645">See below.</span></span> | <span data-ttu-id="b57db-646">Opções adicionais específicas para o transporte de votação longa.</span><span class="sxs-lookup"><span data-stu-id="b57db-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b57db-647">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-647">See below.</span></span> | <span data-ttu-id="b57db-648">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b57db-649">O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b57db-650">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-650">Option</span></span> | <span data-ttu-id="b57db-651">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-651">Default Value</span></span> | <span data-ttu-id="b57db-652">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b57db-653">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-653">90 seconds</span></span> | <span data-ttu-id="b57db-654">A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="b57db-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b57db-655">A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="b57db-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b57db-656">O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b57db-657">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-657">Option</span></span> | <span data-ttu-id="b57db-658">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-658">Default Value</span></span> | <span data-ttu-id="b57db-659">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b57db-660">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-660">5 seconds</span></span> | <span data-ttu-id="b57db-661">Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="b57db-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b57db-662">Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b57db-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b57db-663">O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b57db-664">Configurar opções de cliente</span><span class="sxs-lookup"><span data-stu-id="b57db-664">Configure client options</span></span>

<span data-ttu-id="b57db-665">As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b57db-666">Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.</span><span class="sxs-lookup"><span data-stu-id="b57db-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b57db-667">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="b57db-667">Configure logging</span></span>

<span data-ttu-id="b57db-668">O registro é configurado no `ConfigureLogging` Cliente .NET usando o método.</span><span class="sxs-lookup"><span data-stu-id="b57db-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b57db-669">Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b57db-670">Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b57db-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-671">Para registrar provedores de registro, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="b57db-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b57db-672">Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="b57db-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b57db-673">Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="b57db-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b57db-674">Ligue `AddConsole` para o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b57db-675">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="b57db-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b57db-676">Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="b57db-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b57db-677">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="b57db-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b57db-678">Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="b57db-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b57db-679">Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b57db-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b57db-680">O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b57db-681">É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b57db-682">O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="b57db-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b57db-683">Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="b57db-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b57db-684">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="b57db-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b57db-685">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b57db-685">Configure allowed transports</span></span>

<span data-ttu-id="b57db-686">Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b57db-687">Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b57db-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b57db-688">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="b57db-688">All transports are enabled by default.</span></span>

<span data-ttu-id="b57db-689">Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:</span><span class="sxs-lookup"><span data-stu-id="b57db-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b57db-690">No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b57db-691">Nesta versão do cliente Java, os websockets são os únicos transportes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b57db-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b57db-692">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="b57db-692">Configure bearer authentication</span></span>

<span data-ttu-id="b57db-693">Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b57db-694">No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="b57db-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b57db-695">No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets).</span><span class="sxs-lookup"><span data-stu-id="b57db-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b57db-696">Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .</span><span class="sxs-lookup"><span data-stu-id="b57db-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b57db-697">No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="b57db-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b57db-698">No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :</span><span class="sxs-lookup"><span data-stu-id="b57db-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="b57db-699">No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b57db-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b57db-700">Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="b57db-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b57db-701">Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b57db-702">Configure opções de tempo-out e mantenha-se viva</span><span class="sxs-lookup"><span data-stu-id="b57db-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b57db-703">Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="b57db-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-704">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-705">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-705">Option</span></span> | <span data-ttu-id="b57db-706">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-706">Default value</span></span> | <span data-ttu-id="b57db-707">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b57db-708">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-709">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-709">Timeout for server activity.</span></span> <span data-ttu-id="b57db-710">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-711">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-712">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b57db-713">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-713">15 seconds</span></span> | <span data-ttu-id="b57db-714">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-715">Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-716">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-717">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b57db-718">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-718">15 seconds</span></span> | <span data-ttu-id="b57db-719">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-720">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-721">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b57db-722">No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="b57db-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b57db-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-724">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-724">Option</span></span> | <span data-ttu-id="b57db-725">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-725">Default value</span></span> | <span data-ttu-id="b57db-726">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b57db-727">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-728">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-728">Timeout for server activity.</span></span> <span data-ttu-id="b57db-729">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b57db-730">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-731">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b57db-732">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b57db-733">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-734">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-735">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-736">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-736">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-737">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-737">Option</span></span> | <span data-ttu-id="b57db-738">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-738">Default value</span></span> | <span data-ttu-id="b57db-739">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b57db-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b57db-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b57db-741">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-742">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-742">Timeout for server activity.</span></span> <span data-ttu-id="b57db-743">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-744">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-745">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b57db-746">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-746">15 seconds</span></span> | <span data-ttu-id="b57db-747">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-748">Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-749">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-750">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b57db-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b57db-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b57db-752">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b57db-753">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b57db-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b57db-754">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b57db-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b57db-755">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b57db-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b57db-756">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-756">Configure additional options</span></span>

<span data-ttu-id="b57db-757">Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="b57db-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-758">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-759">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="b57db-759">.NET Option</span></span> |  <span data-ttu-id="b57db-760">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-760">Default value</span></span> | <span data-ttu-id="b57db-761">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b57db-762">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b57db-763">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-764">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-765">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b57db-766">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-766">Empty</span></span> | <span data-ttu-id="b57db-767">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="b57db-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b57db-768">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-768">Empty</span></span> | <span data-ttu-id="b57db-769">Uma coleção de cookies HTTP para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b57db-770">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-770">Empty</span></span> | <span data-ttu-id="b57db-771">Credenciais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b57db-772">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-772">5 seconds</span></span> | <span data-ttu-id="b57db-773">Apenas WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-773">WebSockets only.</span></span> <span data-ttu-id="b57db-774">O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="b57db-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b57db-775">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b57db-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b57db-776">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-776">Empty</span></span> | <span data-ttu-id="b57db-777">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b57db-778">Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b57db-779">Não usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="b57db-780">Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b57db-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b57db-781">Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="b57db-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b57db-782">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="b57db-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b57db-783">Um proxy HTTP para usar ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b57db-784">Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b57db-785">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="b57db-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b57db-786">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b57db-787">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="b57db-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b57db-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-789">Opção JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-789">JavaScript Option</span></span> | <span data-ttu-id="b57db-790">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-790">Default Value</span></span> | <span data-ttu-id="b57db-791">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b57db-792">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b57db-793">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-794">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-795">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-796">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-796">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-797">Opção Java</span><span class="sxs-lookup"><span data-stu-id="b57db-797">Java Option</span></span> | <span data-ttu-id="b57db-798">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-798">Default Value</span></span> | <span data-ttu-id="b57db-799">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b57db-800">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b57db-801">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-802">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-803">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b57db-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b57db-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b57db-805">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-805">Empty</span></span> | <span data-ttu-id="b57db-806">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b57db-807">No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="b57db-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b57db-808">No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b57db-809">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b57db-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b57db-810">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b57db-811">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b57db-812">ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b57db-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b57db-813">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="b57db-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b57db-814">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que pode ser adicionado após [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="b57db-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b57db-815">O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto.</span><span class="sxs-lookup"><span data-stu-id="b57db-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b57db-816">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto `JsonSerializerSettings` é um objeto JSON.NET que pode ser usado para configurar serialização de argumentos e valores de devolução.</span><span class="sxs-lookup"><span data-stu-id="b57db-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b57db-817">Para obter mais informações, consulte a [documentação JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b57db-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b57db-818">Como exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez `Startup.ConfigureServices`dos nomes padrão "camelCase", use o código a seguir em :</span><span class="sxs-lookup"><span data-stu-id="b57db-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b57db-819">No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b57db-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b57db-820">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="b57db-821">Não é possível configurar a serialização JSON no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b57db-822">Opções de serialização do MessagePack</span><span class="sxs-lookup"><span data-stu-id="b57db-822">MessagePack serialization options</span></span>

<span data-ttu-id="b57db-823">A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="b57db-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b57db-824">Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b57db-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-825">Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="b57db-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b57db-826">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="b57db-826">Configure server options</span></span>

<span data-ttu-id="b57db-827">A tabela a seguir descreve opções para configurar hubs SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b57db-828">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-828">Option</span></span> | <span data-ttu-id="b57db-829">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-829">Default Value</span></span> | <span data-ttu-id="b57db-830">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="b57db-831">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-831">15 seconds</span></span> | <span data-ttu-id="b57db-832">Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="b57db-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b57db-833">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-834">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b57db-835">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-835">15 seconds</span></span> | <span data-ttu-id="b57db-836">Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="b57db-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b57db-837">Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b57db-838">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="b57db-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b57db-839">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b57db-839">All installed protocols</span></span> | <span data-ttu-id="b57db-840">Protocolos suportados por este hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-840">Protocols supported by this hub.</span></span> <span data-ttu-id="b57db-841">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="b57db-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b57db-842">Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b57db-843">O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b57db-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b57db-844">As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .</span><span class="sxs-lookup"><span data-stu-id="b57db-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="b57db-845">As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="b57db-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b57db-846">Opções avançadas de configuração HTTP</span><span class="sxs-lookup"><span data-stu-id="b57db-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b57db-847">Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b57db-848">Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="b57db-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="b57db-849">A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="b57db-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b57db-850">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-850">Option</span></span> | <span data-ttu-id="b57db-851">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-851">Default Value</span></span> | <span data-ttu-id="b57db-852">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b57db-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-853">32 KB</span></span> | <span data-ttu-id="b57db-854">O número máximo de bytes recebidos do cliente que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="b57db-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b57db-855">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b57db-856">Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b57db-857">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub.</span><span class="sxs-lookup"><span data-stu-id="b57db-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b57db-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="b57db-858">32 KB</span></span> | <span data-ttu-id="b57db-859">O número máximo de bytes enviados pelo aplicativo que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="b57db-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b57db-860">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b57db-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b57db-861">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="b57db-861">All Transports are enabled.</span></span> | <span data-ttu-id="b57db-862">Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="b57db-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b57db-863">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-863">See below.</span></span> | <span data-ttu-id="b57db-864">Opções adicionais específicas para o transporte de votação longa.</span><span class="sxs-lookup"><span data-stu-id="b57db-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b57db-865">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b57db-865">See below.</span></span> | <span data-ttu-id="b57db-866">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b57db-867">O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b57db-868">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-868">Option</span></span> | <span data-ttu-id="b57db-869">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-869">Default Value</span></span> | <span data-ttu-id="b57db-870">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b57db-871">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-871">90 seconds</span></span> | <span data-ttu-id="b57db-872">A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="b57db-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b57db-873">A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="b57db-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b57db-874">O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="b57db-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b57db-875">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-875">Option</span></span> | <span data-ttu-id="b57db-876">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-876">Default Value</span></span> | <span data-ttu-id="b57db-877">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b57db-878">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-878">5 seconds</span></span> | <span data-ttu-id="b57db-879">Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="b57db-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b57db-880">Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b57db-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b57db-881">O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b57db-882">Configurar opções de cliente</span><span class="sxs-lookup"><span data-stu-id="b57db-882">Configure client options</span></span>

<span data-ttu-id="b57db-883">As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b57db-884">Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.</span><span class="sxs-lookup"><span data-stu-id="b57db-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b57db-885">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="b57db-885">Configure logging</span></span>

<span data-ttu-id="b57db-886">O registro é configurado no `ConfigureLogging` Cliente .NET usando o método.</span><span class="sxs-lookup"><span data-stu-id="b57db-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b57db-887">Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b57db-888">Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b57db-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b57db-889">Para registrar provedores de registro, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="b57db-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b57db-890">Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="b57db-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b57db-891">Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="b57db-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b57db-892">Ligue `AddConsole` para o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b57db-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b57db-893">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="b57db-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b57db-894">Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="b57db-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b57db-895">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="b57db-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b57db-896">Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="b57db-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b57db-897">Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b57db-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b57db-898">O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b57db-899">É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro.</span><span class="sxs-lookup"><span data-stu-id="b57db-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b57db-900">O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="b57db-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b57db-901">Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="b57db-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b57db-902">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="b57db-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b57db-903">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b57db-903">Configure allowed transports</span></span>

<span data-ttu-id="b57db-904">Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b57db-905">Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b57db-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b57db-906">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="b57db-906">All transports are enabled by default.</span></span>

<span data-ttu-id="b57db-907">Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:</span><span class="sxs-lookup"><span data-stu-id="b57db-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b57db-908">No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b57db-909">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="b57db-909">Configure bearer authentication</span></span>

<span data-ttu-id="b57db-910">Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="b57db-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b57db-911">No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="b57db-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b57db-912">No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets).</span><span class="sxs-lookup"><span data-stu-id="b57db-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b57db-913">Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .</span><span class="sxs-lookup"><span data-stu-id="b57db-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b57db-914">No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="b57db-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b57db-915">No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :</span><span class="sxs-lookup"><span data-stu-id="b57db-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="b57db-916">No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b57db-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b57db-917">Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="b57db-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b57db-918">Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="b57db-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b57db-919">Configure opções de tempo-out e mantenha-se viva</span><span class="sxs-lookup"><span data-stu-id="b57db-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b57db-920">Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="b57db-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-921">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-922">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-922">Option</span></span> | <span data-ttu-id="b57db-923">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-923">Default value</span></span> | <span data-ttu-id="b57db-924">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b57db-925">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-926">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-926">Timeout for server activity.</span></span> <span data-ttu-id="b57db-927">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-928">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-929">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b57db-930">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-930">15 seconds</span></span> | <span data-ttu-id="b57db-931">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-932">Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b57db-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b57db-933">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-934">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="b57db-935">No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="b57db-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b57db-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-937">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-937">Option</span></span> | <span data-ttu-id="b57db-938">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-938">Default value</span></span> | <span data-ttu-id="b57db-939">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b57db-940">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-941">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-941">Timeout for server activity.</span></span> <span data-ttu-id="b57db-942">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b57db-943">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-944">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="b57db-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-945">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-945">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-946">Opção</span><span class="sxs-lookup"><span data-stu-id="b57db-946">Option</span></span> | <span data-ttu-id="b57db-947">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-947">Default value</span></span> | <span data-ttu-id="b57db-948">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b57db-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b57db-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b57db-950">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b57db-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b57db-951">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-951">Timeout for server activity.</span></span> <span data-ttu-id="b57db-952">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-953">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="b57db-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b57db-954">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="b57db-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b57db-955">15 s</span><span class="sxs-lookup"><span data-stu-id="b57db-955">15 seconds</span></span> | <span data-ttu-id="b57db-956">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b57db-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="b57db-957">Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="b57db-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b57db-958">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="b57db-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b57db-959">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b57db-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b57db-960">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-960">Configure additional options</span></span>

<span data-ttu-id="b57db-961">Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="b57db-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b57db-962">.NET</span><span class="sxs-lookup"><span data-stu-id="b57db-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b57db-963">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="b57db-963">.NET Option</span></span> |  <span data-ttu-id="b57db-964">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-964">Default value</span></span> | <span data-ttu-id="b57db-965">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b57db-966">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b57db-967">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-968">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-969">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b57db-970">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-970">Empty</span></span> | <span data-ttu-id="b57db-971">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="b57db-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b57db-972">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-972">Empty</span></span> | <span data-ttu-id="b57db-973">Uma coleção de cookies HTTP para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b57db-974">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-974">Empty</span></span> | <span data-ttu-id="b57db-975">Credenciais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b57db-976">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b57db-976">5 seconds</span></span> | <span data-ttu-id="b57db-977">Apenas WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-977">WebSockets only.</span></span> <span data-ttu-id="b57db-978">O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="b57db-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b57db-979">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="b57db-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b57db-980">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-980">Empty</span></span> | <span data-ttu-id="b57db-981">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b57db-982">Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b57db-983">Não usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="b57db-984">Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b57db-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b57db-985">Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="b57db-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b57db-986">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="b57db-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b57db-987">Um proxy HTTP para usar ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b57db-988">Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b57db-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b57db-989">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="b57db-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b57db-990">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b57db-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b57db-991">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="b57db-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b57db-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b57db-993">Opção JavaScript</span><span class="sxs-lookup"><span data-stu-id="b57db-993">JavaScript Option</span></span> | <span data-ttu-id="b57db-994">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-994">Default Value</span></span> | <span data-ttu-id="b57db-995">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b57db-996">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b57db-997">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-998">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-999">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b57db-1000">Java</span><span class="sxs-lookup"><span data-stu-id="b57db-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="b57db-1001">Opção Java</span><span class="sxs-lookup"><span data-stu-id="b57db-1001">Java Option</span></span> | <span data-ttu-id="b57db-1002">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="b57db-1002">Default Value</span></span> | <span data-ttu-id="b57db-1003">Descrição</span><span class="sxs-lookup"><span data-stu-id="b57db-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b57db-1004">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b57db-1005">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b57db-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b57db-1006">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b57db-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b57db-1007">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="b57db-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b57db-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b57db-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b57db-1009">Vazio</span><span class="sxs-lookup"><span data-stu-id="b57db-1009">Empty</span></span> | <span data-ttu-id="b57db-1010">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b57db-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b57db-1011">No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="b57db-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b57db-1012">No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:</span><span class="sxs-lookup"><span data-stu-id="b57db-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b57db-1013">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b57db-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b57db-1014">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b57db-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
