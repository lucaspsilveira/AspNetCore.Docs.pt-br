---
title: Configuração SignalR de ASP.NET Core
author: bradygaster
description: Saiba como configurar os aplicativos SignalR ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559001"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="669c2-103">Configuração do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="669c2-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="669c2-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="669c2-105">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="669c2-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="669c2-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="669c2-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="669c2-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="669c2-108">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="669c2-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="669c2-109">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="669c2-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="669c2-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="669c2-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="669c2-111">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="669c2-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="669c2-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o `Startup.ConfigureServices`seguinte código em:</span><span class="sxs-lookup"><span data-stu-id="669c2-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="669c2-113">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="669c2-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="669c2-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="669c2-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="669c2-116">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="669c2-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="669c2-117">Se você precisar de recursos `Newtonsoft.Json` do que não têm `System.Text.Json`suporte no, consulte [alternar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="669c2-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="669c2-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-118">MessagePack serialization options</span></span>

<span data-ttu-id="669c2-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="669c2-120">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="669c2-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="669c2-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="669c2-122">Configure server options</span></span>

<span data-ttu-id="669c2-123">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="669c2-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="669c2-124">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-124">Option</span></span> | <span data-ttu-id="669c2-125">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-125">Default Value</span></span> | <span data-ttu-id="669c2-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="669c2-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-127">30 seconds</span></span> | <span data-ttu-id="669c2-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="669c2-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="669c2-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="669c2-130">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="669c2-131">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-131">15 seconds</span></span> | <span data-ttu-id="669c2-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="669c2-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="669c2-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-134">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-135">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-135">15 seconds</span></span> | <span data-ttu-id="669c2-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="669c2-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="669c2-137">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="669c2-138">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="669c2-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="669c2-139">All installed protocols</span></span> | <span data-ttu-id="669c2-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-140">Protocols supported by this hub.</span></span> <span data-ttu-id="669c2-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="669c2-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="669c2-142">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="669c2-143">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="669c2-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="669c2-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="669c2-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="669c2-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="669c2-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-146">32 KB</span></span> | <span data-ttu-id="669c2-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="669c2-148">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="669c2-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="669c2-149">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="669c2-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="669c2-150">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="669c2-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="669c2-151">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="669c2-152">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="669c2-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="669c2-153">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="669c2-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="669c2-154">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-154">Option</span></span> | <span data-ttu-id="669c2-155">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-155">Default Value</span></span> | <span data-ttu-id="669c2-156">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="669c2-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-157">32 KB</span></span> | <span data-ttu-id="669c2-158">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="669c2-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="669c2-159">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="669c2-160">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="669c2-161">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="669c2-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-162">32 KB</span></span> | <span data-ttu-id="669c2-163">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="669c2-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="669c2-164">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="669c2-165">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="669c2-165">All Transports are enabled.</span></span> | <span data-ttu-id="669c2-166">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="669c2-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="669c2-167">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-167">See below.</span></span> | <span data-ttu-id="669c2-168">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="669c2-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="669c2-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-169">See below.</span></span> | <span data-ttu-id="669c2-170">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="669c2-171">0</span><span class="sxs-lookup"><span data-stu-id="669c2-171">0</span></span> | <span data-ttu-id="669c2-172">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="669c2-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="669c2-173">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="669c2-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="669c2-174">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="669c2-175">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-175">Option</span></span> | <span data-ttu-id="669c2-176">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-176">Default Value</span></span> | <span data-ttu-id="669c2-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="669c2-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-178">90 seconds</span></span> | <span data-ttu-id="669c2-179">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="669c2-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="669c2-180">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="669c2-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="669c2-181">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="669c2-182">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-182">Option</span></span> | <span data-ttu-id="669c2-183">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-183">Default Value</span></span> | <span data-ttu-id="669c2-184">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="669c2-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-185">5 seconds</span></span> | <span data-ttu-id="669c2-186">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="669c2-187">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="669c2-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="669c2-188">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="669c2-189">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="669c2-189">Configure client options</span></span>

<span data-ttu-id="669c2-190">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="669c2-191">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="669c2-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="669c2-192">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="669c2-192">Configure logging</span></span>

<span data-ttu-id="669c2-193">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="669c2-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="669c2-194">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="669c2-195">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="669c2-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-196">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="669c2-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="669c2-197">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="669c2-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="669c2-198">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="669c2-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="669c2-199">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="669c2-200">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="669c2-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="669c2-201">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="669c2-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="669c2-202">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="669c2-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="669c2-203">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="669c2-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="669c2-204">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="669c2-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="669c2-205">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="669c2-205">The following table lists the available log levels.</span></span> <span data-ttu-id="669c2-206">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="669c2-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="669c2-207">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="669c2-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="669c2-208">String</span><span class="sxs-lookup"><span data-stu-id="669c2-208">String</span></span>                      | <span data-ttu-id="669c2-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="669c2-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="669c2-210">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="669c2-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="669c2-211">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="669c2-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="669c2-212">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="669c2-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="669c2-213">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="669c2-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="669c2-214">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="669c2-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="669c2-215">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="669c2-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="669c2-216">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="669c2-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="669c2-217">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="669c2-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="669c2-218">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="669c2-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="669c2-219">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="669c2-219">Configure allowed transports</span></span>

<span data-ttu-id="669c2-220">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="669c2-221">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="669c2-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="669c2-222">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="669c2-222">All transports are enabled by default.</span></span>

<span data-ttu-id="669c2-223">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="669c2-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="669c2-224">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="669c2-225">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="669c2-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="669c2-226">No cliente Java, o transporte é selecionado com o `withTransport` método no. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="669c2-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="669c2-227">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="669c2-228">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="669c2-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="669c2-229">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="669c2-229">Configure bearer authentication</span></span>

<span data-ttu-id="669c2-230">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="669c2-231">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="669c2-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="669c2-232">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="669c2-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="669c2-233">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="669c2-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="669c2-234">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="669c2-235">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="669c2-236">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="669c2-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="669c2-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="669c2-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="669c2-238">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="669c2-239">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="669c2-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="669c2-240">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="669c2-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-241">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-242">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-242">Option</span></span> | <span data-ttu-id="669c2-243">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-243">Default value</span></span> | <span data-ttu-id="669c2-244">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="669c2-245">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-246">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-246">Timeout for server activity.</span></span> <span data-ttu-id="669c2-247">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-248">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-249">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="669c2-250">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-250">15 seconds</span></span> | <span data-ttu-id="669c2-251">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-252">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-253">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-254">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-255">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-255">15 seconds</span></span> | <span data-ttu-id="669c2-256">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-257">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-258">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="669c2-259">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="669c2-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="669c2-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-261">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-261">Option</span></span> | <span data-ttu-id="669c2-262">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-262">Default value</span></span> | <span data-ttu-id="669c2-263">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="669c2-264">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-265">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-265">Timeout for server activity.</span></span> <span data-ttu-id="669c2-266">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="669c2-267">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-268">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="669c2-269">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-270">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-271">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-272">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-273">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-273">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-274">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-274">Option</span></span> | <span data-ttu-id="669c2-275">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-275">Default value</span></span> | <span data-ttu-id="669c2-276">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="669c2-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="669c2-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="669c2-278">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-279">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-279">Timeout for server activity.</span></span> <span data-ttu-id="669c2-280">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-281">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-282">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="669c2-283">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-283">15 seconds</span></span> | <span data-ttu-id="669c2-284">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-285">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-286">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-287">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="669c2-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="669c2-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="669c2-289">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-290">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-291">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-292">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="669c2-293">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-293">Configure additional options</span></span>

<span data-ttu-id="669c2-294">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="669c2-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-295">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-296">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="669c2-296">.NET Option</span></span> |  <span data-ttu-id="669c2-297">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-297">Default value</span></span> | <span data-ttu-id="669c2-298">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="669c2-299">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="669c2-300">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-301">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-302">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="669c2-303">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-303">Empty</span></span> | <span data-ttu-id="669c2-304">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="669c2-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="669c2-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-305">Empty</span></span> | <span data-ttu-id="669c2-306">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="669c2-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-307">Empty</span></span> | <span data-ttu-id="669c2-308">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="669c2-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-309">5 seconds</span></span> | <span data-ttu-id="669c2-310">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-310">WebSockets only.</span></span> <span data-ttu-id="669c2-311">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="669c2-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="669c2-312">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="669c2-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="669c2-313">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-313">Empty</span></span> | <span data-ttu-id="669c2-314">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="669c2-315">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="669c2-316">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="669c2-317">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="669c2-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="669c2-318">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="669c2-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="669c2-319">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="669c2-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="669c2-320">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="669c2-321">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="669c2-322">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="669c2-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="669c2-323">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="669c2-324">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="669c2-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="669c2-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-326">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-326">JavaScript Option</span></span> | <span data-ttu-id="669c2-327">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-327">Default Value</span></span> | <span data-ttu-id="669c2-328">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="669c2-329">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="669c2-330">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-331">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-332">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="669c2-333">Especifica se as credenciais serão enviadas com a solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="669c2-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="669c2-334">Azure App serviço usa cookies para sessões adesivas e precisa que essa opção esteja habilitada para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="669c2-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="669c2-335">Para obter mais informações sobre CORS com Signalr, <xref:signalr/security#cross-origin-resource-sharing>consulte.</span><span class="sxs-lookup"><span data-stu-id="669c2-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-336">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-336">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-337">Opção Java</span><span class="sxs-lookup"><span data-stu-id="669c2-337">Java Option</span></span> | <span data-ttu-id="669c2-338">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-338">Default Value</span></span> | <span data-ttu-id="669c2-339">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="669c2-340">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="669c2-341">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-342">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-343">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="669c2-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="669c2-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="669c2-345">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-345">Empty</span></span> | <span data-ttu-id="669c2-346">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="669c2-347">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="669c2-348">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="669c2-349">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="669c2-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="669c2-350">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="669c2-351">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="669c2-352">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="669c2-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="669c2-353">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="669c2-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="669c2-354">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="669c2-355">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="669c2-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="669c2-356">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="669c2-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="669c2-357">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="669c2-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="669c2-358">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="669c2-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="669c2-359">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o `Startup.ConfigureServices`seguinte código em:</span><span class="sxs-lookup"><span data-stu-id="669c2-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="669c2-360">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="669c2-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="669c2-361">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="669c2-362">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="669c2-363">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="669c2-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="669c2-364">Se você precisar de recursos `Newtonsoft.Json` do que não têm `System.Text.Json`suporte no, consulte [alternar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="669c2-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="669c2-365">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-365">MessagePack serialization options</span></span>

<span data-ttu-id="669c2-366">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="669c2-367">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="669c2-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-368">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="669c2-369">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="669c2-369">Configure server options</span></span>

<span data-ttu-id="669c2-370">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="669c2-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="669c2-371">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-371">Option</span></span> | <span data-ttu-id="669c2-372">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-372">Default Value</span></span> | <span data-ttu-id="669c2-373">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="669c2-374">30 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-374">30 seconds</span></span> | <span data-ttu-id="669c2-375">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="669c2-376">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="669c2-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="669c2-377">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="669c2-378">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-378">15 seconds</span></span> | <span data-ttu-id="669c2-379">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="669c2-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="669c2-380">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-381">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-382">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-382">15 seconds</span></span> | <span data-ttu-id="669c2-383">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="669c2-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="669c2-384">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="669c2-385">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="669c2-386">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="669c2-386">All installed protocols</span></span> | <span data-ttu-id="669c2-387">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-387">Protocols supported by this hub.</span></span> <span data-ttu-id="669c2-388">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="669c2-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="669c2-389">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="669c2-390">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="669c2-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="669c2-391">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="669c2-392">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="669c2-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="669c2-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-393">32 KB</span></span> | <span data-ttu-id="669c2-394">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="669c2-395">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="669c2-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="669c2-396">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="669c2-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="669c2-397">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="669c2-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="669c2-398">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="669c2-399">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="669c2-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="669c2-400">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="669c2-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="669c2-401">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-401">Option</span></span> | <span data-ttu-id="669c2-402">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-402">Default Value</span></span> | <span data-ttu-id="669c2-403">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="669c2-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-404">32 KB</span></span> | <span data-ttu-id="669c2-405">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="669c2-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="669c2-406">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="669c2-407">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="669c2-408">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="669c2-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-409">32 KB</span></span> | <span data-ttu-id="669c2-410">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="669c2-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="669c2-411">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="669c2-412">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="669c2-412">All Transports are enabled.</span></span> | <span data-ttu-id="669c2-413">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="669c2-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="669c2-414">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-414">See below.</span></span> | <span data-ttu-id="669c2-415">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="669c2-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="669c2-416">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-416">See below.</span></span> | <span data-ttu-id="669c2-417">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="669c2-418">0</span><span class="sxs-lookup"><span data-stu-id="669c2-418">0</span></span> | <span data-ttu-id="669c2-419">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="669c2-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="669c2-420">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="669c2-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="669c2-421">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="669c2-422">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-422">Option</span></span> | <span data-ttu-id="669c2-423">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-423">Default Value</span></span> | <span data-ttu-id="669c2-424">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="669c2-425">90 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-425">90 seconds</span></span> | <span data-ttu-id="669c2-426">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="669c2-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="669c2-427">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="669c2-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="669c2-428">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="669c2-429">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-429">Option</span></span> | <span data-ttu-id="669c2-430">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-430">Default Value</span></span> | <span data-ttu-id="669c2-431">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="669c2-432">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-432">5 seconds</span></span> | <span data-ttu-id="669c2-433">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="669c2-434">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="669c2-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="669c2-435">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="669c2-436">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="669c2-436">Configure client options</span></span>

<span data-ttu-id="669c2-437">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="669c2-438">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="669c2-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="669c2-439">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="669c2-439">Configure logging</span></span>

<span data-ttu-id="669c2-440">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="669c2-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="669c2-441">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="669c2-442">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="669c2-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-443">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="669c2-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="669c2-444">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="669c2-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="669c2-445">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="669c2-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="669c2-446">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="669c2-447">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="669c2-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="669c2-448">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="669c2-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="669c2-449">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="669c2-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="669c2-450">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="669c2-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="669c2-451">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="669c2-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="669c2-452">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="669c2-452">The following table lists the available log levels.</span></span> <span data-ttu-id="669c2-453">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="669c2-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="669c2-454">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="669c2-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="669c2-455">String</span><span class="sxs-lookup"><span data-stu-id="669c2-455">String</span></span>                      | <span data-ttu-id="669c2-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="669c2-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="669c2-457">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="669c2-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="669c2-458">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="669c2-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="669c2-459">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="669c2-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="669c2-460">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="669c2-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="669c2-461">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="669c2-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="669c2-462">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="669c2-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="669c2-463">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="669c2-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="669c2-464">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="669c2-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="669c2-465">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="669c2-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="669c2-466">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="669c2-466">Configure allowed transports</span></span>

<span data-ttu-id="669c2-467">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="669c2-468">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="669c2-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="669c2-469">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="669c2-469">All transports are enabled by default.</span></span>

<span data-ttu-id="669c2-470">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="669c2-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="669c2-471">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="669c2-472">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="669c2-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="669c2-473">No cliente Java, o transporte é selecionado com o `withTransport` método no. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="669c2-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="669c2-474">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="669c2-475">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="669c2-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="669c2-476">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="669c2-476">Configure bearer authentication</span></span>

<span data-ttu-id="669c2-477">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="669c2-478">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="669c2-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="669c2-479">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="669c2-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="669c2-480">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="669c2-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="669c2-481">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="669c2-482">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="669c2-483">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="669c2-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="669c2-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="669c2-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="669c2-485">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="669c2-486">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="669c2-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="669c2-487">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="669c2-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-488">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-489">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-489">Option</span></span> | <span data-ttu-id="669c2-490">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-490">Default value</span></span> | <span data-ttu-id="669c2-491">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="669c2-492">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-493">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-493">Timeout for server activity.</span></span> <span data-ttu-id="669c2-494">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-495">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-496">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="669c2-497">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-497">15 seconds</span></span> | <span data-ttu-id="669c2-498">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-499">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-500">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-501">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-502">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-502">15 seconds</span></span> | <span data-ttu-id="669c2-503">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-504">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-505">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="669c2-506">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="669c2-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="669c2-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-508">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-508">Option</span></span> | <span data-ttu-id="669c2-509">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-509">Default value</span></span> | <span data-ttu-id="669c2-510">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="669c2-511">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-512">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-512">Timeout for server activity.</span></span> <span data-ttu-id="669c2-513">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="669c2-514">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-515">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="669c2-516">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-517">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-518">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-519">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-520">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-520">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-521">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-521">Option</span></span> | <span data-ttu-id="669c2-522">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-522">Default value</span></span> | <span data-ttu-id="669c2-523">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="669c2-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="669c2-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="669c2-525">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-526">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-526">Timeout for server activity.</span></span> <span data-ttu-id="669c2-527">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-528">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-529">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="669c2-530">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-530">15 seconds</span></span> | <span data-ttu-id="669c2-531">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-532">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-533">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-534">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="669c2-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="669c2-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="669c2-536">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-537">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-538">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-539">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="669c2-540">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-540">Configure additional options</span></span>

<span data-ttu-id="669c2-541">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="669c2-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-542">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-543">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="669c2-543">.NET Option</span></span> |  <span data-ttu-id="669c2-544">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-544">Default value</span></span> | <span data-ttu-id="669c2-545">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="669c2-546">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="669c2-547">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-548">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-549">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="669c2-550">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-550">Empty</span></span> | <span data-ttu-id="669c2-551">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="669c2-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="669c2-552">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-552">Empty</span></span> | <span data-ttu-id="669c2-553">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="669c2-554">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-554">Empty</span></span> | <span data-ttu-id="669c2-555">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="669c2-556">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-556">5 seconds</span></span> | <span data-ttu-id="669c2-557">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-557">WebSockets only.</span></span> <span data-ttu-id="669c2-558">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="669c2-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="669c2-559">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="669c2-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="669c2-560">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-560">Empty</span></span> | <span data-ttu-id="669c2-561">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="669c2-562">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="669c2-563">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="669c2-564">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="669c2-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="669c2-565">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="669c2-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="669c2-566">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="669c2-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="669c2-567">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="669c2-568">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="669c2-569">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="669c2-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="669c2-570">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="669c2-571">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="669c2-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="669c2-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-573">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-573">JavaScript Option</span></span> | <span data-ttu-id="669c2-574">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-574">Default Value</span></span> | <span data-ttu-id="669c2-575">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="669c2-576">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="669c2-577">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-578">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-579">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-580">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-580">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-581">Opção Java</span><span class="sxs-lookup"><span data-stu-id="669c2-581">Java Option</span></span> | <span data-ttu-id="669c2-582">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-582">Default Value</span></span> | <span data-ttu-id="669c2-583">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="669c2-584">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="669c2-585">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-586">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-587">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="669c2-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="669c2-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="669c2-589">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-589">Empty</span></span> | <span data-ttu-id="669c2-590">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="669c2-591">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="669c2-592">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="669c2-593">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="669c2-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="669c2-594">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="669c2-595">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="669c2-596">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="669c2-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="669c2-597">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="669c2-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="669c2-598">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="669c2-599">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="669c2-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="669c2-600">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="669c2-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="669c2-601">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="669c2-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="669c2-602">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="669c2-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="669c2-603">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o `Startup.ConfigureServices`seguinte código em:</span><span class="sxs-lookup"><span data-stu-id="669c2-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="669c2-604">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="669c2-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="669c2-605">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="669c2-606">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="669c2-607">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="669c2-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="669c2-608">Se você precisar de recursos `Newtonsoft.Json` do que não têm `System.Text.Json`suporte no, consulte [alternar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="669c2-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="669c2-609">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-609">MessagePack serialization options</span></span>

<span data-ttu-id="669c2-610">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="669c2-611">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="669c2-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-612">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="669c2-613">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="669c2-613">Configure server options</span></span>

<span data-ttu-id="669c2-614">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="669c2-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="669c2-615">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-615">Option</span></span> | <span data-ttu-id="669c2-616">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-616">Default Value</span></span> | <span data-ttu-id="669c2-617">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="669c2-618">30 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-618">30 seconds</span></span> | <span data-ttu-id="669c2-619">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="669c2-620">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="669c2-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="669c2-621">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="669c2-622">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-622">15 seconds</span></span> | <span data-ttu-id="669c2-623">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="669c2-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="669c2-624">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-625">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-626">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-626">15 seconds</span></span> | <span data-ttu-id="669c2-627">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="669c2-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="669c2-628">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="669c2-629">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="669c2-630">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="669c2-630">All installed protocols</span></span> | <span data-ttu-id="669c2-631">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-631">Protocols supported by this hub.</span></span> <span data-ttu-id="669c2-632">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="669c2-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="669c2-633">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="669c2-634">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="669c2-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="669c2-635">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="669c2-636">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="669c2-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="669c2-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-637">32 KB</span></span> | <span data-ttu-id="669c2-638">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="669c2-639">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="669c2-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="669c2-640">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="669c2-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="669c2-641">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="669c2-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="669c2-642">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="669c2-643">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="669c2-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="669c2-644">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="669c2-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="669c2-645">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-645">Option</span></span> | <span data-ttu-id="669c2-646">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-646">Default Value</span></span> | <span data-ttu-id="669c2-647">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="669c2-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-648">32 KB</span></span> | <span data-ttu-id="669c2-649">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="669c2-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="669c2-650">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="669c2-651">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="669c2-652">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="669c2-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-653">32 KB</span></span> | <span data-ttu-id="669c2-654">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="669c2-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="669c2-655">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="669c2-656">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="669c2-656">All Transports are enabled.</span></span> | <span data-ttu-id="669c2-657">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="669c2-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="669c2-658">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-658">See below.</span></span> | <span data-ttu-id="669c2-659">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="669c2-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="669c2-660">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-660">See below.</span></span> | <span data-ttu-id="669c2-661">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="669c2-662">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="669c2-663">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-663">Option</span></span> | <span data-ttu-id="669c2-664">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-664">Default Value</span></span> | <span data-ttu-id="669c2-665">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="669c2-666">90 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-666">90 seconds</span></span> | <span data-ttu-id="669c2-667">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="669c2-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="669c2-668">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="669c2-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="669c2-669">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="669c2-670">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-670">Option</span></span> | <span data-ttu-id="669c2-671">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-671">Default Value</span></span> | <span data-ttu-id="669c2-672">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="669c2-673">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-673">5 seconds</span></span> | <span data-ttu-id="669c2-674">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="669c2-675">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="669c2-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="669c2-676">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="669c2-677">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="669c2-677">Configure client options</span></span>

<span data-ttu-id="669c2-678">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="669c2-679">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="669c2-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="669c2-680">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="669c2-680">Configure logging</span></span>

<span data-ttu-id="669c2-681">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="669c2-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="669c2-682">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="669c2-683">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="669c2-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-684">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="669c2-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="669c2-685">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="669c2-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="669c2-686">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="669c2-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="669c2-687">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="669c2-688">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="669c2-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="669c2-689">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="669c2-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="669c2-690">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="669c2-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="669c2-691">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="669c2-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="669c2-692">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="669c2-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="669c2-693">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="669c2-693">The following table lists the available log levels.</span></span> <span data-ttu-id="669c2-694">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="669c2-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="669c2-695">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="669c2-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="669c2-696">String</span><span class="sxs-lookup"><span data-stu-id="669c2-696">String</span></span>                      | <span data-ttu-id="669c2-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="669c2-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="669c2-698">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="669c2-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="669c2-699">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="669c2-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="669c2-700">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="669c2-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="669c2-701">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="669c2-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="669c2-702">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="669c2-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="669c2-703">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="669c2-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="669c2-704">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="669c2-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="669c2-705">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="669c2-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="669c2-706">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="669c2-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="669c2-707">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="669c2-707">Configure allowed transports</span></span>

<span data-ttu-id="669c2-708">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="669c2-709">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="669c2-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="669c2-710">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="669c2-710">All transports are enabled by default.</span></span>

<span data-ttu-id="669c2-711">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="669c2-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="669c2-712">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="669c2-713">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="669c2-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="669c2-714">No cliente Java, o transporte é selecionado com o `withTransport` método no. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="669c2-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="669c2-715">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="669c2-716">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="669c2-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="669c2-717">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="669c2-717">Configure bearer authentication</span></span>

<span data-ttu-id="669c2-718">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="669c2-719">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="669c2-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="669c2-720">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="669c2-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="669c2-721">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="669c2-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="669c2-722">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="669c2-723">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="669c2-724">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="669c2-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="669c2-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="669c2-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="669c2-726">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="669c2-727">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="669c2-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="669c2-728">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="669c2-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-729">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-730">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-730">Option</span></span> | <span data-ttu-id="669c2-731">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-731">Default value</span></span> | <span data-ttu-id="669c2-732">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="669c2-733">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-734">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-734">Timeout for server activity.</span></span> <span data-ttu-id="669c2-735">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-736">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-737">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="669c2-738">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-738">15 seconds</span></span> | <span data-ttu-id="669c2-739">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-740">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-741">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-742">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-743">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-743">15 seconds</span></span> | <span data-ttu-id="669c2-744">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-745">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-746">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="669c2-747">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="669c2-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="669c2-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-749">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-749">Option</span></span> | <span data-ttu-id="669c2-750">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-750">Default value</span></span> | <span data-ttu-id="669c2-751">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="669c2-752">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-753">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-753">Timeout for server activity.</span></span> <span data-ttu-id="669c2-754">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="669c2-755">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-756">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="669c2-757">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-758">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-759">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-760">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-761">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-761">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-762">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-762">Option</span></span> | <span data-ttu-id="669c2-763">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-763">Default value</span></span> | <span data-ttu-id="669c2-764">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="669c2-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="669c2-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="669c2-766">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-767">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-767">Timeout for server activity.</span></span> <span data-ttu-id="669c2-768">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-769">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-770">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="669c2-771">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-771">15 seconds</span></span> | <span data-ttu-id="669c2-772">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-773">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-774">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-775">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="669c2-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="669c2-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="669c2-777">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-778">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-779">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-780">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="669c2-781">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-781">Configure additional options</span></span>

<span data-ttu-id="669c2-782">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="669c2-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-783">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-784">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="669c2-784">.NET Option</span></span> |  <span data-ttu-id="669c2-785">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-785">Default value</span></span> | <span data-ttu-id="669c2-786">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="669c2-787">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="669c2-788">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-789">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-790">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="669c2-791">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-791">Empty</span></span> | <span data-ttu-id="669c2-792">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="669c2-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="669c2-793">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-793">Empty</span></span> | <span data-ttu-id="669c2-794">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="669c2-795">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-795">Empty</span></span> | <span data-ttu-id="669c2-796">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="669c2-797">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-797">5 seconds</span></span> | <span data-ttu-id="669c2-798">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-798">WebSockets only.</span></span> <span data-ttu-id="669c2-799">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="669c2-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="669c2-800">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="669c2-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="669c2-801">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-801">Empty</span></span> | <span data-ttu-id="669c2-802">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="669c2-803">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="669c2-804">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="669c2-805">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="669c2-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="669c2-806">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="669c2-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="669c2-807">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="669c2-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="669c2-808">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="669c2-809">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="669c2-810">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="669c2-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="669c2-811">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="669c2-812">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="669c2-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="669c2-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-814">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-814">JavaScript Option</span></span> | <span data-ttu-id="669c2-815">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-815">Default Value</span></span> | <span data-ttu-id="669c2-816">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="669c2-817">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="669c2-818">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-819">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-820">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-821">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-821">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-822">Opção Java</span><span class="sxs-lookup"><span data-stu-id="669c2-822">Java Option</span></span> | <span data-ttu-id="669c2-823">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-823">Default Value</span></span> | <span data-ttu-id="669c2-824">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="669c2-825">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="669c2-826">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-827">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-828">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="669c2-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="669c2-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="669c2-830">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-830">Empty</span></span> | <span data-ttu-id="669c2-831">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="669c2-832">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="669c2-833">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="669c2-834">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="669c2-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="669c2-835">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="669c2-836">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="669c2-837">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="669c2-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="669c2-838">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="669c2-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="669c2-839">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="669c2-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="669c2-840">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="669c2-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="669c2-841">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="669c2-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="669c2-842">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="669c2-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="669c2-843">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="669c2-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="669c2-844">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="669c2-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="669c2-845">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="669c2-846">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="669c2-847">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-847">MessagePack serialization options</span></span>

<span data-ttu-id="669c2-848">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="669c2-849">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="669c2-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-850">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="669c2-851">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="669c2-851">Configure server options</span></span>

<span data-ttu-id="669c2-852">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="669c2-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="669c2-853">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-853">Option</span></span> | <span data-ttu-id="669c2-854">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-854">Default Value</span></span> | <span data-ttu-id="669c2-855">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="669c2-856">30 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-856">30 seconds</span></span> | <span data-ttu-id="669c2-857">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="669c2-858">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="669c2-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="669c2-859">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="669c2-860">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-860">15 seconds</span></span> | <span data-ttu-id="669c2-861">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="669c2-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="669c2-862">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-863">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-864">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-864">15 seconds</span></span> | <span data-ttu-id="669c2-865">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="669c2-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="669c2-866">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="669c2-867">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="669c2-868">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="669c2-868">All installed protocols</span></span> | <span data-ttu-id="669c2-869">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-869">Protocols supported by this hub.</span></span> <span data-ttu-id="669c2-870">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="669c2-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="669c2-871">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="669c2-872">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="669c2-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="669c2-873">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="669c2-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="669c2-874">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="669c2-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="669c2-875">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="669c2-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="669c2-876">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="669c2-877">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="669c2-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="669c2-878">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="669c2-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="669c2-879">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-879">Option</span></span> | <span data-ttu-id="669c2-880">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-880">Default Value</span></span> | <span data-ttu-id="669c2-881">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="669c2-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-882">32 KB</span></span> | <span data-ttu-id="669c2-883">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="669c2-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="669c2-884">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="669c2-885">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="669c2-886">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="669c2-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-887">32 KB</span></span> | <span data-ttu-id="669c2-888">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="669c2-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="669c2-889">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="669c2-890">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="669c2-890">All Transports are enabled.</span></span> | <span data-ttu-id="669c2-891">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="669c2-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="669c2-892">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-892">See below.</span></span> | <span data-ttu-id="669c2-893">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="669c2-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="669c2-894">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-894">See below.</span></span> | <span data-ttu-id="669c2-895">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="669c2-896">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="669c2-897">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-897">Option</span></span> | <span data-ttu-id="669c2-898">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-898">Default Value</span></span> | <span data-ttu-id="669c2-899">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="669c2-900">90 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-900">90 seconds</span></span> | <span data-ttu-id="669c2-901">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="669c2-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="669c2-902">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="669c2-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="669c2-903">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="669c2-904">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-904">Option</span></span> | <span data-ttu-id="669c2-905">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-905">Default Value</span></span> | <span data-ttu-id="669c2-906">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="669c2-907">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-907">5 seconds</span></span> | <span data-ttu-id="669c2-908">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="669c2-909">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="669c2-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="669c2-910">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="669c2-911">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="669c2-911">Configure client options</span></span>

<span data-ttu-id="669c2-912">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="669c2-913">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="669c2-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="669c2-914">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="669c2-914">Configure logging</span></span>

<span data-ttu-id="669c2-915">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="669c2-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="669c2-916">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="669c2-917">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="669c2-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-918">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="669c2-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="669c2-919">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="669c2-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="669c2-920">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="669c2-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="669c2-921">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="669c2-922">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="669c2-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="669c2-923">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="669c2-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="669c2-924">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="669c2-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="669c2-925">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="669c2-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="669c2-926">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="669c2-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="669c2-927">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="669c2-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="669c2-928">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="669c2-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="669c2-929">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="669c2-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="669c2-930">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="669c2-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="669c2-931">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="669c2-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="669c2-932">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="669c2-932">Configure allowed transports</span></span>

<span data-ttu-id="669c2-933">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="669c2-934">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="669c2-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="669c2-935">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="669c2-935">All transports are enabled by default.</span></span>

<span data-ttu-id="669c2-936">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="669c2-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="669c2-937">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="669c2-938">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="669c2-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="669c2-939">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="669c2-939">Configure bearer authentication</span></span>

<span data-ttu-id="669c2-940">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="669c2-941">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="669c2-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="669c2-942">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="669c2-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="669c2-943">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="669c2-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="669c2-944">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="669c2-945">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="669c2-946">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="669c2-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="669c2-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="669c2-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="669c2-948">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="669c2-949">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="669c2-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="669c2-950">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="669c2-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-951">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-952">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-952">Option</span></span> | <span data-ttu-id="669c2-953">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-953">Default value</span></span> | <span data-ttu-id="669c2-954">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="669c2-955">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-956">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-956">Timeout for server activity.</span></span> <span data-ttu-id="669c2-957">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-958">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-959">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="669c2-960">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-960">15 seconds</span></span> | <span data-ttu-id="669c2-961">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-962">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-963">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-964">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-965">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-965">15 seconds</span></span> | <span data-ttu-id="669c2-966">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-967">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-968">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="669c2-969">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="669c2-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="669c2-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-971">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-971">Option</span></span> | <span data-ttu-id="669c2-972">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-972">Default value</span></span> | <span data-ttu-id="669c2-973">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="669c2-974">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-975">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-975">Timeout for server activity.</span></span> <span data-ttu-id="669c2-976">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="669c2-977">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-978">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="669c2-979">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-980">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-981">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-982">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-983">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-983">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-984">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-984">Option</span></span> | <span data-ttu-id="669c2-985">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-985">Default value</span></span> | <span data-ttu-id="669c2-986">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="669c2-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="669c2-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="669c2-988">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-989">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-989">Timeout for server activity.</span></span> <span data-ttu-id="669c2-990">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-991">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-992">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="669c2-993">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-993">15 seconds</span></span> | <span data-ttu-id="669c2-994">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-995">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-996">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-997">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="669c2-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="669c2-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="669c2-999">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="669c2-1000">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="669c2-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="669c2-1001">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="669c2-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="669c2-1002">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="669c2-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="669c2-1003">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-1003">Configure additional options</span></span>

<span data-ttu-id="669c2-1004">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="669c2-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-1006">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="669c2-1006">.NET Option</span></span> |  <span data-ttu-id="669c2-1007">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1007">Default value</span></span> | <span data-ttu-id="669c2-1008">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="669c2-1009">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="669c2-1010">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-1011">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-1012">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="669c2-1013">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1013">Empty</span></span> | <span data-ttu-id="669c2-1014">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="669c2-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="669c2-1015">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1015">Empty</span></span> | <span data-ttu-id="669c2-1016">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="669c2-1017">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1017">Empty</span></span> | <span data-ttu-id="669c2-1018">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="669c2-1019">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-1019">5 seconds</span></span> | <span data-ttu-id="669c2-1020">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-1020">WebSockets only.</span></span> <span data-ttu-id="669c2-1021">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="669c2-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="669c2-1022">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="669c2-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="669c2-1023">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1023">Empty</span></span> | <span data-ttu-id="669c2-1024">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="669c2-1025">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="669c2-1026">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="669c2-1027">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="669c2-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="669c2-1028">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="669c2-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="669c2-1029">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="669c2-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="669c2-1030">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="669c2-1031">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="669c2-1032">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="669c2-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="669c2-1033">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="669c2-1034">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="669c2-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="669c2-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-1036">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-1036">JavaScript Option</span></span> | <span data-ttu-id="669c2-1037">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1037">Default Value</span></span> | <span data-ttu-id="669c2-1038">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="669c2-1039">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="669c2-1040">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-1041">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-1042">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-1043">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-1044">Opção Java</span><span class="sxs-lookup"><span data-stu-id="669c2-1044">Java Option</span></span> | <span data-ttu-id="669c2-1045">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1045">Default Value</span></span> | <span data-ttu-id="669c2-1046">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="669c2-1047">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="669c2-1048">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-1049">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-1050">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="669c2-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="669c2-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="669c2-1052">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1052">Empty</span></span> | <span data-ttu-id="669c2-1053">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="669c2-1054">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="669c2-1055">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="669c2-1056">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="669c2-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="669c2-1057">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="669c2-1058">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="669c2-1059">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="669c2-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="669c2-1060">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="669c2-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="669c2-1061">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="669c2-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="669c2-1062">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="669c2-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="669c2-1063">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="669c2-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="669c2-1064">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="669c2-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="669c2-1065">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="669c2-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="669c2-1066">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="669c2-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="669c2-1067">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="669c2-1068">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="669c2-1069">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="669c2-1069">MessagePack serialization options</span></span>

<span data-ttu-id="669c2-1070">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="669c2-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="669c2-1071">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="669c2-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-1072">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="669c2-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="669c2-1073">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="669c2-1073">Configure server options</span></span>

<span data-ttu-id="669c2-1074">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="669c2-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="669c2-1075">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-1075">Option</span></span> | <span data-ttu-id="669c2-1076">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1076">Default Value</span></span> | <span data-ttu-id="669c2-1077">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="669c2-1078">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-1078">15 seconds</span></span> | <span data-ttu-id="669c2-1079">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="669c2-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="669c2-1080">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-1081">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="669c2-1082">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-1082">15 seconds</span></span> | <span data-ttu-id="669c2-1083">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="669c2-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="669c2-1084">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="669c2-1085">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="669c2-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="669c2-1086">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="669c2-1086">All installed protocols</span></span> | <span data-ttu-id="669c2-1087">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="669c2-1088">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="669c2-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="669c2-1089">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="669c2-1090">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="669c2-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="669c2-1091">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="669c2-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="669c2-1092">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="669c2-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="669c2-1093">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="669c2-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="669c2-1094">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="669c2-1095">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="669c2-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="669c2-1096">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="669c2-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="669c2-1097">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-1097">Option</span></span> | <span data-ttu-id="669c2-1098">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1098">Default Value</span></span> | <span data-ttu-id="669c2-1099">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="669c2-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-1100">32 KB</span></span> | <span data-ttu-id="669c2-1101">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="669c2-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="669c2-1102">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="669c2-1103">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="669c2-1104">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="669c2-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="669c2-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="669c2-1105">32 KB</span></span> | <span data-ttu-id="669c2-1106">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="669c2-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="669c2-1107">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="669c2-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="669c2-1108">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="669c2-1108">All Transports are enabled.</span></span> | <span data-ttu-id="669c2-1109">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="669c2-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="669c2-1110">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-1110">See below.</span></span> | <span data-ttu-id="669c2-1111">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="669c2-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="669c2-1112">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="669c2-1112">See below.</span></span> | <span data-ttu-id="669c2-1113">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="669c2-1114">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="669c2-1115">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-1115">Option</span></span> | <span data-ttu-id="669c2-1116">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1116">Default Value</span></span> | <span data-ttu-id="669c2-1117">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="669c2-1118">90 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-1118">90 seconds</span></span> | <span data-ttu-id="669c2-1119">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="669c2-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="669c2-1120">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="669c2-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="669c2-1121">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="669c2-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="669c2-1122">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-1122">Option</span></span> | <span data-ttu-id="669c2-1123">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1123">Default Value</span></span> | <span data-ttu-id="669c2-1124">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="669c2-1125">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-1125">5 seconds</span></span> | <span data-ttu-id="669c2-1126">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="669c2-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="669c2-1127">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="669c2-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="669c2-1128">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="669c2-1129">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="669c2-1129">Configure client options</span></span>

<span data-ttu-id="669c2-1130">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="669c2-1131">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="669c2-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="669c2-1132">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="669c2-1132">Configure logging</span></span>

<span data-ttu-id="669c2-1133">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="669c2-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="669c2-1134">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="669c2-1135">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="669c2-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="669c2-1136">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="669c2-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="669c2-1137">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="669c2-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="669c2-1138">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="669c2-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="669c2-1139">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="669c2-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="669c2-1140">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="669c2-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="669c2-1141">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="669c2-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="669c2-1142">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="669c2-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="669c2-1143">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="669c2-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="669c2-1144">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="669c2-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="669c2-1145">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="669c2-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="669c2-1146">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="669c2-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="669c2-1147">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="669c2-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="669c2-1148">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="669c2-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="669c2-1149">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="669c2-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="669c2-1150">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="669c2-1150">Configure allowed transports</span></span>

<span data-ttu-id="669c2-1151">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="669c2-1152">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="669c2-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="669c2-1153">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="669c2-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="669c2-1154">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="669c2-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="669c2-1155">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="669c2-1156">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="669c2-1156">Configure bearer authentication</span></span>

<span data-ttu-id="669c2-1157">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="669c2-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="669c2-1158">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="669c2-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="669c2-1159">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="669c2-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="669c2-1160">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="669c2-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="669c2-1161">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="669c2-1162">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="669c2-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="669c2-1163">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="669c2-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="669c2-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="669c2-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="669c2-1165">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="669c2-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="669c2-1166">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="669c2-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="669c2-1167">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="669c2-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-1169">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-1169">Option</span></span> | <span data-ttu-id="669c2-1170">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1170">Default value</span></span> | <span data-ttu-id="669c2-1171">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="669c2-1172">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-1173">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-1173">Timeout for server activity.</span></span> <span data-ttu-id="669c2-1174">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-1175">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-1176">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="669c2-1177">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-1177">15 seconds</span></span> | <span data-ttu-id="669c2-1178">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-1179">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="669c2-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="669c2-1180">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-1181">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="669c2-1182">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="669c2-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="669c2-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-1184">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-1184">Option</span></span> | <span data-ttu-id="669c2-1185">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1185">Default value</span></span> | <span data-ttu-id="669c2-1186">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="669c2-1187">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-1188">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-1188">Timeout for server activity.</span></span> <span data-ttu-id="669c2-1189">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="669c2-1190">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-1191">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-1192">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-1193">Opção</span><span class="sxs-lookup"><span data-stu-id="669c2-1193">Option</span></span> | <span data-ttu-id="669c2-1194">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1194">Default value</span></span> | <span data-ttu-id="669c2-1195">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="669c2-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="669c2-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="669c2-1197">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="669c2-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="669c2-1198">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-1198">Timeout for server activity.</span></span> <span data-ttu-id="669c2-1199">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-1200">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="669c2-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="669c2-1201">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="669c2-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="669c2-1202">15 s</span><span class="sxs-lookup"><span data-stu-id="669c2-1202">15 seconds</span></span> | <span data-ttu-id="669c2-1203">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="669c2-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="669c2-1204">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="669c2-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="669c2-1205">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="669c2-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="669c2-1206">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="669c2-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="669c2-1207">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-1207">Configure additional options</span></span>

<span data-ttu-id="669c2-1208">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="669c2-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="669c2-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="669c2-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="669c2-1210">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="669c2-1210">.NET Option</span></span> |  <span data-ttu-id="669c2-1211">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1211">Default value</span></span> | <span data-ttu-id="669c2-1212">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="669c2-1213">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="669c2-1214">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-1215">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-1216">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="669c2-1217">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1217">Empty</span></span> | <span data-ttu-id="669c2-1218">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="669c2-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="669c2-1219">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1219">Empty</span></span> | <span data-ttu-id="669c2-1220">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="669c2-1221">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1221">Empty</span></span> | <span data-ttu-id="669c2-1222">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="669c2-1223">5 segundos</span><span class="sxs-lookup"><span data-stu-id="669c2-1223">5 seconds</span></span> | <span data-ttu-id="669c2-1224">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-1224">WebSockets only.</span></span> <span data-ttu-id="669c2-1225">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="669c2-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="669c2-1226">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="669c2-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="669c2-1227">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1227">Empty</span></span> | <span data-ttu-id="669c2-1228">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="669c2-1229">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="669c2-1230">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="669c2-1231">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="669c2-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="669c2-1232">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="669c2-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="669c2-1233">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="669c2-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="669c2-1234">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="669c2-1235">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="669c2-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="669c2-1236">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="669c2-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="669c2-1237">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="669c2-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="669c2-1238">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="669c2-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="669c2-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="669c2-1240">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="669c2-1240">JavaScript Option</span></span> | <span data-ttu-id="669c2-1241">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1241">Default Value</span></span> | <span data-ttu-id="669c2-1242">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="669c2-1243">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="669c2-1244">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-1245">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-1246">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="669c2-1247">Java</span><span class="sxs-lookup"><span data-stu-id="669c2-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="669c2-1248">Opção Java</span><span class="sxs-lookup"><span data-stu-id="669c2-1248">Java Option</span></span> | <span data-ttu-id="669c2-1249">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="669c2-1249">Default Value</span></span> | <span data-ttu-id="669c2-1250">Descrição</span><span class="sxs-lookup"><span data-stu-id="669c2-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="669c2-1251">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="669c2-1252">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="669c2-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="669c2-1253">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="669c2-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="669c2-1254">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="669c2-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="669c2-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="669c2-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="669c2-1256">Vazio</span><span class="sxs-lookup"><span data-stu-id="669c2-1256">Empty</span></span> | <span data-ttu-id="669c2-1257">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="669c2-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="669c2-1258">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="669c2-1259">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="669c2-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="669c2-1260">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="669c2-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="669c2-1261">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="669c2-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
