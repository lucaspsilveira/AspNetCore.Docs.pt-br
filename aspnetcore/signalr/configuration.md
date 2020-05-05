---
title: Configuração SignalR de ASP.NET Core
author: bradygaster
description: Saiba como configurar os aplicativos SignalR ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 054462c37fffd1973cbbe4f76ae4a3be5a6c1778
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767298"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="d1790-103">Configuração do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d1790-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d1790-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d1790-105">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d1790-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d1790-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d1790-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d1790-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d1790-108">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d1790-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1790-109">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d1790-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d1790-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d1790-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d1790-111">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d1790-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d1790-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o `Startup.ConfigureServices`seguinte código em:</span><span class="sxs-lookup"><span data-stu-id="d1790-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d1790-113">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d1790-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d1790-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d1790-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d1790-116">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="d1790-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d1790-117">Se você precisar de recursos `Newtonsoft.Json` do que não têm `System.Text.Json`suporte no, consulte [alternar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d1790-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d1790-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-118">MessagePack serialization options</span></span>

<span data-ttu-id="d1790-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d1790-120">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d1790-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d1790-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d1790-122">Configure server options</span></span>

<span data-ttu-id="d1790-123">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="d1790-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d1790-124">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-124">Option</span></span> | <span data-ttu-id="d1790-125">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-125">Default Value</span></span> | <span data-ttu-id="d1790-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d1790-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-127">30 seconds</span></span> | <span data-ttu-id="d1790-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d1790-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d1790-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d1790-130">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d1790-131">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-131">15 seconds</span></span> | <span data-ttu-id="d1790-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d1790-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d1790-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-134">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-135">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-135">15 seconds</span></span> | <span data-ttu-id="d1790-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d1790-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d1790-137">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d1790-138">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d1790-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d1790-139">All installed protocols</span></span> | <span data-ttu-id="d1790-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-140">Protocols supported by this hub.</span></span> <span data-ttu-id="d1790-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d1790-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d1790-142">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d1790-143">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d1790-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d1790-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d1790-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d1790-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d1790-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-146">32 KB</span></span> | <span data-ttu-id="d1790-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d1790-148">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="d1790-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d1790-149">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="d1790-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d1790-150">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d1790-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d1790-151">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d1790-152">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="d1790-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d1790-153">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d1790-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d1790-154">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-154">Option</span></span> | <span data-ttu-id="d1790-155">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-155">Default Value</span></span> | <span data-ttu-id="d1790-156">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d1790-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-157">32 KB</span></span> | <span data-ttu-id="d1790-158">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d1790-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d1790-159">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d1790-160">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d1790-161">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d1790-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-162">32 KB</span></span> | <span data-ttu-id="d1790-163">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d1790-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d1790-164">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d1790-165">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d1790-165">All Transports are enabled.</span></span> | <span data-ttu-id="d1790-166">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d1790-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d1790-167">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-167">See below.</span></span> | <span data-ttu-id="d1790-168">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d1790-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d1790-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-169">See below.</span></span> | <span data-ttu-id="d1790-170">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d1790-171">0</span><span class="sxs-lookup"><span data-stu-id="d1790-171">0</span></span> | <span data-ttu-id="d1790-172">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d1790-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d1790-173">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="d1790-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d1790-174">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d1790-175">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-175">Option</span></span> | <span data-ttu-id="d1790-176">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-176">Default Value</span></span> | <span data-ttu-id="d1790-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d1790-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-178">90 seconds</span></span> | <span data-ttu-id="d1790-179">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d1790-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d1790-180">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d1790-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d1790-181">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d1790-182">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-182">Option</span></span> | <span data-ttu-id="d1790-183">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-183">Default Value</span></span> | <span data-ttu-id="d1790-184">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d1790-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-185">5 seconds</span></span> | <span data-ttu-id="d1790-186">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d1790-187">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d1790-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d1790-188">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d1790-189">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d1790-189">Configure client options</span></span>

<span data-ttu-id="d1790-190">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d1790-191">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d1790-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d1790-192">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d1790-192">Configure logging</span></span>

<span data-ttu-id="d1790-193">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="d1790-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d1790-194">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d1790-195">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d1790-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-196">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d1790-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d1790-197">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d1790-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d1790-198">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d1790-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d1790-199">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d1790-200">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1790-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d1790-201">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d1790-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d1790-202">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1790-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d1790-203">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d1790-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d1790-204">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="d1790-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d1790-205">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d1790-205">The following table lists the available log levels.</span></span> <span data-ttu-id="d1790-206">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d1790-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d1790-207">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d1790-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d1790-208">String</span><span class="sxs-lookup"><span data-stu-id="d1790-208">String</span></span>                      | <span data-ttu-id="d1790-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d1790-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d1790-210">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d1790-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d1790-211">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d1790-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d1790-212">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="d1790-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d1790-213">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d1790-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d1790-214">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d1790-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d1790-215">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d1790-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d1790-216">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="d1790-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d1790-217">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d1790-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d1790-218">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d1790-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d1790-219">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d1790-219">Configure allowed transports</span></span>

<span data-ttu-id="d1790-220">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d1790-221">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d1790-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d1790-222">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d1790-222">All transports are enabled by default.</span></span>

<span data-ttu-id="d1790-223">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d1790-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d1790-224">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d1790-225">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d1790-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d1790-226">No cliente Java, o transporte é selecionado com o `withTransport` método no. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="d1790-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d1790-227">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d1790-228">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d1790-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d1790-229">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d1790-229">Configure bearer authentication</span></span>

<span data-ttu-id="d1790-230">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d1790-231">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="d1790-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d1790-232">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d1790-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d1790-233">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="d1790-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d1790-234">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d1790-235">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d1790-236">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d1790-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d1790-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="d1790-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d1790-238">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d1790-239">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d1790-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d1790-240">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d1790-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-241">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-242">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-242">Option</span></span> | <span data-ttu-id="d1790-243">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-243">Default value</span></span> | <span data-ttu-id="d1790-244">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d1790-245">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-246">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-246">Timeout for server activity.</span></span> <span data-ttu-id="d1790-247">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-248">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-249">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d1790-250">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-250">15 seconds</span></span> | <span data-ttu-id="d1790-251">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-252">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-253">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-254">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-255">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-255">15 seconds</span></span> | <span data-ttu-id="d1790-256">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-257">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-258">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d1790-259">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="d1790-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d1790-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-261">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-261">Option</span></span> | <span data-ttu-id="d1790-262">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-262">Default value</span></span> | <span data-ttu-id="d1790-263">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d1790-264">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-265">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-265">Timeout for server activity.</span></span> <span data-ttu-id="d1790-266">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d1790-267">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-268">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d1790-269">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-270">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-271">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-272">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-273">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-273">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-274">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-274">Option</span></span> | <span data-ttu-id="d1790-275">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-275">Default value</span></span> | <span data-ttu-id="d1790-276">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d1790-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d1790-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d1790-278">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-279">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-279">Timeout for server activity.</span></span> <span data-ttu-id="d1790-280">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-281">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-282">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d1790-283">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-283">15 seconds</span></span> | <span data-ttu-id="d1790-284">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-285">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-286">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-287">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d1790-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d1790-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d1790-289">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-290">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-291">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-292">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d1790-293">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-293">Configure additional options</span></span>

<span data-ttu-id="d1790-294">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d1790-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-295">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-296">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d1790-296">.NET Option</span></span> |  <span data-ttu-id="d1790-297">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-297">Default value</span></span> | <span data-ttu-id="d1790-298">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d1790-299">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d1790-300">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-301">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-302">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d1790-303">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-303">Empty</span></span> | <span data-ttu-id="d1790-304">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d1790-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d1790-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-305">Empty</span></span> | <span data-ttu-id="d1790-306">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d1790-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-307">Empty</span></span> | <span data-ttu-id="d1790-308">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d1790-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-309">5 seconds</span></span> | <span data-ttu-id="d1790-310">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-310">WebSockets only.</span></span> <span data-ttu-id="d1790-311">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d1790-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d1790-312">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d1790-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d1790-313">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-313">Empty</span></span> | <span data-ttu-id="d1790-314">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d1790-315">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d1790-316">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="d1790-317">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d1790-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d1790-318">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="d1790-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d1790-319">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d1790-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d1790-320">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d1790-321">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d1790-322">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d1790-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d1790-323">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d1790-324">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d1790-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d1790-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-326">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-326">JavaScript Option</span></span> | <span data-ttu-id="d1790-327">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-327">Default Value</span></span> | <span data-ttu-id="d1790-328">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d1790-329">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d1790-330">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-331">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-332">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="d1790-333">Especifica se as credenciais serão enviadas com a solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="d1790-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="d1790-334">Azure App serviço usa cookies para sessões adesivas e precisa que essa opção esteja habilitada para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="d1790-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="d1790-335">Para obter mais informações sobre CORS com Signalr, <xref:signalr/security#cross-origin-resource-sharing>consulte.</span><span class="sxs-lookup"><span data-stu-id="d1790-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-336">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-336">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-337">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d1790-337">Java Option</span></span> | <span data-ttu-id="d1790-338">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-338">Default Value</span></span> | <span data-ttu-id="d1790-339">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d1790-340">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d1790-341">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-342">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-343">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d1790-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d1790-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d1790-345">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-345">Empty</span></span> | <span data-ttu-id="d1790-346">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d1790-347">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d1790-348">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d1790-349">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d1790-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d1790-350">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d1790-351">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d1790-352">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d1790-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d1790-353">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d1790-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d1790-354">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d1790-355">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d1790-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1790-356">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d1790-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d1790-357">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d1790-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d1790-358">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d1790-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d1790-359">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o `Startup.ConfigureServices`seguinte código em:</span><span class="sxs-lookup"><span data-stu-id="d1790-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d1790-360">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d1790-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d1790-361">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d1790-362">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d1790-363">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="d1790-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d1790-364">Se você precisar de recursos `Newtonsoft.Json` do que não têm `System.Text.Json`suporte no, consulte [alternar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d1790-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d1790-365">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-365">MessagePack serialization options</span></span>

<span data-ttu-id="d1790-366">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d1790-367">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d1790-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-368">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d1790-369">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d1790-369">Configure server options</span></span>

<span data-ttu-id="d1790-370">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="d1790-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d1790-371">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-371">Option</span></span> | <span data-ttu-id="d1790-372">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-372">Default Value</span></span> | <span data-ttu-id="d1790-373">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d1790-374">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-374">30 seconds</span></span> | <span data-ttu-id="d1790-375">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d1790-376">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d1790-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d1790-377">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d1790-378">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-378">15 seconds</span></span> | <span data-ttu-id="d1790-379">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d1790-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d1790-380">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-381">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-382">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-382">15 seconds</span></span> | <span data-ttu-id="d1790-383">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d1790-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d1790-384">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d1790-385">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d1790-386">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d1790-386">All installed protocols</span></span> | <span data-ttu-id="d1790-387">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-387">Protocols supported by this hub.</span></span> <span data-ttu-id="d1790-388">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d1790-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d1790-389">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d1790-390">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d1790-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d1790-391">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d1790-392">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d1790-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d1790-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-393">32 KB</span></span> | <span data-ttu-id="d1790-394">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d1790-395">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="d1790-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d1790-396">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="d1790-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d1790-397">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d1790-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d1790-398">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d1790-399">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="d1790-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d1790-400">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d1790-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d1790-401">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-401">Option</span></span> | <span data-ttu-id="d1790-402">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-402">Default Value</span></span> | <span data-ttu-id="d1790-403">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d1790-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-404">32 KB</span></span> | <span data-ttu-id="d1790-405">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d1790-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d1790-406">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d1790-407">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d1790-408">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d1790-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-409">32 KB</span></span> | <span data-ttu-id="d1790-410">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d1790-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d1790-411">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d1790-412">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d1790-412">All Transports are enabled.</span></span> | <span data-ttu-id="d1790-413">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d1790-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d1790-414">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-414">See below.</span></span> | <span data-ttu-id="d1790-415">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d1790-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d1790-416">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-416">See below.</span></span> | <span data-ttu-id="d1790-417">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d1790-418">0</span><span class="sxs-lookup"><span data-stu-id="d1790-418">0</span></span> | <span data-ttu-id="d1790-419">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d1790-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d1790-420">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="d1790-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d1790-421">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d1790-422">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-422">Option</span></span> | <span data-ttu-id="d1790-423">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-423">Default Value</span></span> | <span data-ttu-id="d1790-424">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d1790-425">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-425">90 seconds</span></span> | <span data-ttu-id="d1790-426">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d1790-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d1790-427">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d1790-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d1790-428">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d1790-429">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-429">Option</span></span> | <span data-ttu-id="d1790-430">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-430">Default Value</span></span> | <span data-ttu-id="d1790-431">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d1790-432">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-432">5 seconds</span></span> | <span data-ttu-id="d1790-433">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d1790-434">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d1790-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d1790-435">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d1790-436">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d1790-436">Configure client options</span></span>

<span data-ttu-id="d1790-437">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d1790-438">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d1790-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d1790-439">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d1790-439">Configure logging</span></span>

<span data-ttu-id="d1790-440">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="d1790-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d1790-441">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d1790-442">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d1790-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-443">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d1790-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d1790-444">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d1790-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d1790-445">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d1790-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d1790-446">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d1790-447">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1790-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d1790-448">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d1790-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d1790-449">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1790-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d1790-450">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d1790-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d1790-451">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="d1790-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d1790-452">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d1790-452">The following table lists the available log levels.</span></span> <span data-ttu-id="d1790-453">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d1790-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d1790-454">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d1790-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d1790-455">String</span><span class="sxs-lookup"><span data-stu-id="d1790-455">String</span></span>                      | <span data-ttu-id="d1790-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d1790-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d1790-457">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d1790-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d1790-458">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d1790-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d1790-459">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="d1790-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d1790-460">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d1790-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d1790-461">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d1790-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d1790-462">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d1790-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d1790-463">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="d1790-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d1790-464">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d1790-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d1790-465">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d1790-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d1790-466">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d1790-466">Configure allowed transports</span></span>

<span data-ttu-id="d1790-467">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d1790-468">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d1790-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d1790-469">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d1790-469">All transports are enabled by default.</span></span>

<span data-ttu-id="d1790-470">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d1790-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d1790-471">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d1790-472">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d1790-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d1790-473">No cliente Java, o transporte é selecionado com o `withTransport` método no. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="d1790-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d1790-474">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d1790-475">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d1790-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d1790-476">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d1790-476">Configure bearer authentication</span></span>

<span data-ttu-id="d1790-477">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d1790-478">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="d1790-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d1790-479">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d1790-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d1790-480">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="d1790-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d1790-481">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d1790-482">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d1790-483">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d1790-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d1790-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="d1790-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d1790-485">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d1790-486">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d1790-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d1790-487">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d1790-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-488">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-489">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-489">Option</span></span> | <span data-ttu-id="d1790-490">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-490">Default value</span></span> | <span data-ttu-id="d1790-491">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d1790-492">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-493">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-493">Timeout for server activity.</span></span> <span data-ttu-id="d1790-494">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-495">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-496">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d1790-497">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-497">15 seconds</span></span> | <span data-ttu-id="d1790-498">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-499">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-500">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-501">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-502">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-502">15 seconds</span></span> | <span data-ttu-id="d1790-503">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-504">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-505">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d1790-506">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="d1790-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d1790-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-508">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-508">Option</span></span> | <span data-ttu-id="d1790-509">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-509">Default value</span></span> | <span data-ttu-id="d1790-510">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d1790-511">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-512">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-512">Timeout for server activity.</span></span> <span data-ttu-id="d1790-513">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d1790-514">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-515">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d1790-516">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-517">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-518">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-519">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-520">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-520">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-521">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-521">Option</span></span> | <span data-ttu-id="d1790-522">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-522">Default value</span></span> | <span data-ttu-id="d1790-523">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d1790-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d1790-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d1790-525">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-526">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-526">Timeout for server activity.</span></span> <span data-ttu-id="d1790-527">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-528">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-529">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d1790-530">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-530">15 seconds</span></span> | <span data-ttu-id="d1790-531">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-532">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-533">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-534">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d1790-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d1790-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d1790-536">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-537">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-538">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-539">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d1790-540">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-540">Configure additional options</span></span>

<span data-ttu-id="d1790-541">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d1790-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-542">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-543">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d1790-543">.NET Option</span></span> |  <span data-ttu-id="d1790-544">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-544">Default value</span></span> | <span data-ttu-id="d1790-545">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d1790-546">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d1790-547">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-548">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-549">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d1790-550">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-550">Empty</span></span> | <span data-ttu-id="d1790-551">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d1790-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d1790-552">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-552">Empty</span></span> | <span data-ttu-id="d1790-553">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d1790-554">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-554">Empty</span></span> | <span data-ttu-id="d1790-555">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d1790-556">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-556">5 seconds</span></span> | <span data-ttu-id="d1790-557">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-557">WebSockets only.</span></span> <span data-ttu-id="d1790-558">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d1790-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d1790-559">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d1790-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d1790-560">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-560">Empty</span></span> | <span data-ttu-id="d1790-561">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d1790-562">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d1790-563">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="d1790-564">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d1790-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d1790-565">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="d1790-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d1790-566">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d1790-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d1790-567">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d1790-568">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d1790-569">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d1790-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d1790-570">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d1790-571">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d1790-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d1790-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-573">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-573">JavaScript Option</span></span> | <span data-ttu-id="d1790-574">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-574">Default Value</span></span> | <span data-ttu-id="d1790-575">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d1790-576">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d1790-577">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-578">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-579">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-580">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-580">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-581">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d1790-581">Java Option</span></span> | <span data-ttu-id="d1790-582">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-582">Default Value</span></span> | <span data-ttu-id="d1790-583">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d1790-584">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d1790-585">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-586">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-587">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d1790-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d1790-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d1790-589">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-589">Empty</span></span> | <span data-ttu-id="d1790-590">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d1790-591">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d1790-592">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d1790-593">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d1790-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d1790-594">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d1790-595">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d1790-596">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d1790-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d1790-597">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d1790-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d1790-598">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d1790-599">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d1790-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d1790-600">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d1790-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d1790-601">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d1790-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d1790-602">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d1790-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d1790-603">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o `Startup.ConfigureServices`seguinte código em:</span><span class="sxs-lookup"><span data-stu-id="d1790-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="d1790-604">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d1790-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d1790-605">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d1790-606">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d1790-607">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="d1790-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d1790-608">Se você precisar de recursos `Newtonsoft.Json` do que não têm `System.Text.Json`suporte no, consulte [alternar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d1790-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d1790-609">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-609">MessagePack serialization options</span></span>

<span data-ttu-id="d1790-610">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d1790-611">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d1790-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-612">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d1790-613">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d1790-613">Configure server options</span></span>

<span data-ttu-id="d1790-614">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="d1790-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d1790-615">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-615">Option</span></span> | <span data-ttu-id="d1790-616">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-616">Default Value</span></span> | <span data-ttu-id="d1790-617">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d1790-618">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-618">30 seconds</span></span> | <span data-ttu-id="d1790-619">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d1790-620">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d1790-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d1790-621">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d1790-622">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-622">15 seconds</span></span> | <span data-ttu-id="d1790-623">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d1790-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d1790-624">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-625">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-626">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-626">15 seconds</span></span> | <span data-ttu-id="d1790-627">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d1790-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d1790-628">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d1790-629">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d1790-630">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d1790-630">All installed protocols</span></span> | <span data-ttu-id="d1790-631">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-631">Protocols supported by this hub.</span></span> <span data-ttu-id="d1790-632">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d1790-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d1790-633">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d1790-634">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d1790-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d1790-635">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d1790-636">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d1790-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d1790-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-637">32 KB</span></span> | <span data-ttu-id="d1790-638">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d1790-639">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="d1790-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d1790-640">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="d1790-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d1790-641">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d1790-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d1790-642">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d1790-643">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="d1790-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d1790-644">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d1790-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d1790-645">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-645">Option</span></span> | <span data-ttu-id="d1790-646">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-646">Default Value</span></span> | <span data-ttu-id="d1790-647">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d1790-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-648">32 KB</span></span> | <span data-ttu-id="d1790-649">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d1790-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d1790-650">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d1790-651">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d1790-652">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d1790-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-653">32 KB</span></span> | <span data-ttu-id="d1790-654">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d1790-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d1790-655">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d1790-656">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d1790-656">All Transports are enabled.</span></span> | <span data-ttu-id="d1790-657">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d1790-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d1790-658">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-658">See below.</span></span> | <span data-ttu-id="d1790-659">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d1790-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d1790-660">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-660">See below.</span></span> | <span data-ttu-id="d1790-661">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d1790-662">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d1790-663">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-663">Option</span></span> | <span data-ttu-id="d1790-664">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-664">Default Value</span></span> | <span data-ttu-id="d1790-665">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d1790-666">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-666">90 seconds</span></span> | <span data-ttu-id="d1790-667">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d1790-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d1790-668">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d1790-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d1790-669">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d1790-670">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-670">Option</span></span> | <span data-ttu-id="d1790-671">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-671">Default Value</span></span> | <span data-ttu-id="d1790-672">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d1790-673">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-673">5 seconds</span></span> | <span data-ttu-id="d1790-674">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d1790-675">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d1790-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d1790-676">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d1790-677">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d1790-677">Configure client options</span></span>

<span data-ttu-id="d1790-678">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d1790-679">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d1790-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d1790-680">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d1790-680">Configure logging</span></span>

<span data-ttu-id="d1790-681">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="d1790-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d1790-682">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d1790-683">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d1790-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-684">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d1790-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d1790-685">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d1790-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d1790-686">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d1790-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d1790-687">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d1790-688">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1790-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d1790-689">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d1790-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d1790-690">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1790-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d1790-691">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d1790-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d1790-692">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="d1790-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d1790-693">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d1790-693">The following table lists the available log levels.</span></span> <span data-ttu-id="d1790-694">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d1790-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d1790-695">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d1790-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d1790-696">String</span><span class="sxs-lookup"><span data-stu-id="d1790-696">String</span></span>                      | <span data-ttu-id="d1790-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d1790-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d1790-698">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d1790-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d1790-699">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d1790-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d1790-700">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="d1790-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d1790-701">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d1790-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d1790-702">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d1790-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d1790-703">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d1790-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d1790-704">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="d1790-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d1790-705">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d1790-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d1790-706">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d1790-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d1790-707">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d1790-707">Configure allowed transports</span></span>

<span data-ttu-id="d1790-708">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d1790-709">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d1790-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d1790-710">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d1790-710">All transports are enabled by default.</span></span>

<span data-ttu-id="d1790-711">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d1790-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d1790-712">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d1790-713">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d1790-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d1790-714">No cliente Java, o transporte é selecionado com o `withTransport` método no. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="d1790-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d1790-715">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d1790-716">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d1790-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d1790-717">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d1790-717">Configure bearer authentication</span></span>

<span data-ttu-id="d1790-718">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d1790-719">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="d1790-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d1790-720">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d1790-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d1790-721">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="d1790-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d1790-722">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d1790-723">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d1790-724">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d1790-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d1790-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="d1790-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d1790-726">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d1790-727">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d1790-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d1790-728">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d1790-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-729">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-730">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-730">Option</span></span> | <span data-ttu-id="d1790-731">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-731">Default value</span></span> | <span data-ttu-id="d1790-732">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d1790-733">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-734">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-734">Timeout for server activity.</span></span> <span data-ttu-id="d1790-735">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-736">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-737">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d1790-738">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-738">15 seconds</span></span> | <span data-ttu-id="d1790-739">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-740">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-741">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-742">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-743">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-743">15 seconds</span></span> | <span data-ttu-id="d1790-744">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-745">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-746">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d1790-747">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="d1790-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d1790-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-749">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-749">Option</span></span> | <span data-ttu-id="d1790-750">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-750">Default value</span></span> | <span data-ttu-id="d1790-751">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d1790-752">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-753">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-753">Timeout for server activity.</span></span> <span data-ttu-id="d1790-754">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d1790-755">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-756">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d1790-757">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-758">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-759">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-760">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-761">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-761">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-762">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-762">Option</span></span> | <span data-ttu-id="d1790-763">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-763">Default value</span></span> | <span data-ttu-id="d1790-764">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d1790-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d1790-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d1790-766">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-767">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-767">Timeout for server activity.</span></span> <span data-ttu-id="d1790-768">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-769">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-770">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d1790-771">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-771">15 seconds</span></span> | <span data-ttu-id="d1790-772">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-773">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-774">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-775">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d1790-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d1790-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d1790-777">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-778">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-779">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-780">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d1790-781">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-781">Configure additional options</span></span>

<span data-ttu-id="d1790-782">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d1790-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-783">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-784">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d1790-784">.NET Option</span></span> |  <span data-ttu-id="d1790-785">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-785">Default value</span></span> | <span data-ttu-id="d1790-786">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d1790-787">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d1790-788">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-789">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-790">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d1790-791">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-791">Empty</span></span> | <span data-ttu-id="d1790-792">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d1790-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d1790-793">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-793">Empty</span></span> | <span data-ttu-id="d1790-794">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d1790-795">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-795">Empty</span></span> | <span data-ttu-id="d1790-796">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d1790-797">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-797">5 seconds</span></span> | <span data-ttu-id="d1790-798">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-798">WebSockets only.</span></span> <span data-ttu-id="d1790-799">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d1790-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d1790-800">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d1790-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d1790-801">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-801">Empty</span></span> | <span data-ttu-id="d1790-802">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d1790-803">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d1790-804">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="d1790-805">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d1790-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d1790-806">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="d1790-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d1790-807">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d1790-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d1790-808">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d1790-809">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d1790-810">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d1790-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d1790-811">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d1790-812">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d1790-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d1790-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-814">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-814">JavaScript Option</span></span> | <span data-ttu-id="d1790-815">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-815">Default Value</span></span> | <span data-ttu-id="d1790-816">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d1790-817">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d1790-818">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-819">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-820">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-821">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-821">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-822">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d1790-822">Java Option</span></span> | <span data-ttu-id="d1790-823">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-823">Default Value</span></span> | <span data-ttu-id="d1790-824">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d1790-825">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d1790-826">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-827">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-828">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d1790-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d1790-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d1790-830">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-830">Empty</span></span> | <span data-ttu-id="d1790-831">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d1790-832">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d1790-833">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d1790-834">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d1790-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d1790-835">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d1790-836">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d1790-837">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d1790-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d1790-838">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d1790-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d1790-839">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d1790-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d1790-840">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d1790-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d1790-841">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d1790-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d1790-842">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d1790-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d1790-843">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="d1790-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d1790-844">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d1790-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d1790-845">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d1790-846">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d1790-847">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-847">MessagePack serialization options</span></span>

<span data-ttu-id="d1790-848">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d1790-849">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d1790-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-850">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d1790-851">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d1790-851">Configure server options</span></span>

<span data-ttu-id="d1790-852">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="d1790-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d1790-853">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-853">Option</span></span> | <span data-ttu-id="d1790-854">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-854">Default Value</span></span> | <span data-ttu-id="d1790-855">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d1790-856">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-856">30 seconds</span></span> | <span data-ttu-id="d1790-857">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d1790-858">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d1790-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d1790-859">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d1790-860">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-860">15 seconds</span></span> | <span data-ttu-id="d1790-861">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d1790-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d1790-862">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-863">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-864">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-864">15 seconds</span></span> | <span data-ttu-id="d1790-865">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d1790-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d1790-866">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d1790-867">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d1790-868">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d1790-868">All installed protocols</span></span> | <span data-ttu-id="d1790-869">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-869">Protocols supported by this hub.</span></span> <span data-ttu-id="d1790-870">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d1790-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d1790-871">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d1790-872">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d1790-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d1790-873">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="d1790-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d1790-874">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="d1790-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d1790-875">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d1790-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d1790-876">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d1790-877">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="d1790-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d1790-878">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d1790-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d1790-879">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-879">Option</span></span> | <span data-ttu-id="d1790-880">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-880">Default Value</span></span> | <span data-ttu-id="d1790-881">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d1790-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-882">32 KB</span></span> | <span data-ttu-id="d1790-883">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d1790-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d1790-884">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d1790-885">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d1790-886">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d1790-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-887">32 KB</span></span> | <span data-ttu-id="d1790-888">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d1790-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d1790-889">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d1790-890">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d1790-890">All Transports are enabled.</span></span> | <span data-ttu-id="d1790-891">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d1790-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d1790-892">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-892">See below.</span></span> | <span data-ttu-id="d1790-893">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d1790-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d1790-894">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-894">See below.</span></span> | <span data-ttu-id="d1790-895">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d1790-896">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d1790-897">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-897">Option</span></span> | <span data-ttu-id="d1790-898">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-898">Default Value</span></span> | <span data-ttu-id="d1790-899">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d1790-900">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-900">90 seconds</span></span> | <span data-ttu-id="d1790-901">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d1790-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d1790-902">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d1790-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d1790-903">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d1790-904">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-904">Option</span></span> | <span data-ttu-id="d1790-905">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-905">Default Value</span></span> | <span data-ttu-id="d1790-906">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d1790-907">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-907">5 seconds</span></span> | <span data-ttu-id="d1790-908">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d1790-909">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d1790-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d1790-910">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d1790-911">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d1790-911">Configure client options</span></span>

<span data-ttu-id="d1790-912">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d1790-913">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d1790-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d1790-914">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d1790-914">Configure logging</span></span>

<span data-ttu-id="d1790-915">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="d1790-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d1790-916">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d1790-917">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d1790-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-918">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d1790-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d1790-919">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d1790-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d1790-920">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d1790-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d1790-921">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d1790-922">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1790-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d1790-923">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d1790-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d1790-924">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1790-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d1790-925">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="d1790-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d1790-926">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d1790-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d1790-927">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d1790-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d1790-928">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d1790-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d1790-929">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="d1790-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d1790-930">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d1790-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d1790-931">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d1790-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d1790-932">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d1790-932">Configure allowed transports</span></span>

<span data-ttu-id="d1790-933">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d1790-934">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d1790-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d1790-935">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d1790-935">All transports are enabled by default.</span></span>

<span data-ttu-id="d1790-936">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d1790-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d1790-937">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d1790-938">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d1790-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d1790-939">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d1790-939">Configure bearer authentication</span></span>

<span data-ttu-id="d1790-940">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d1790-941">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="d1790-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d1790-942">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d1790-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d1790-943">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="d1790-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d1790-944">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d1790-945">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d1790-946">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d1790-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d1790-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="d1790-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d1790-948">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d1790-949">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d1790-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d1790-950">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d1790-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-951">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-952">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-952">Option</span></span> | <span data-ttu-id="d1790-953">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-953">Default value</span></span> | <span data-ttu-id="d1790-954">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d1790-955">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-956">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-956">Timeout for server activity.</span></span> <span data-ttu-id="d1790-957">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-958">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-959">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d1790-960">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-960">15 seconds</span></span> | <span data-ttu-id="d1790-961">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-962">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-963">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-964">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-965">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-965">15 seconds</span></span> | <span data-ttu-id="d1790-966">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-967">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-968">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d1790-969">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="d1790-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d1790-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-971">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-971">Option</span></span> | <span data-ttu-id="d1790-972">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-972">Default value</span></span> | <span data-ttu-id="d1790-973">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d1790-974">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-975">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-975">Timeout for server activity.</span></span> <span data-ttu-id="d1790-976">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d1790-977">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-978">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d1790-979">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-980">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-981">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-982">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-983">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-983">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-984">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-984">Option</span></span> | <span data-ttu-id="d1790-985">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-985">Default value</span></span> | <span data-ttu-id="d1790-986">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d1790-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d1790-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d1790-988">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-989">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-989">Timeout for server activity.</span></span> <span data-ttu-id="d1790-990">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-991">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-992">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d1790-993">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-993">15 seconds</span></span> | <span data-ttu-id="d1790-994">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-995">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-996">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-997">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d1790-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d1790-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d1790-999">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d1790-1000">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d1790-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d1790-1001">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d1790-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d1790-1002">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d1790-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d1790-1003">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-1003">Configure additional options</span></span>

<span data-ttu-id="d1790-1004">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d1790-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-1006">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d1790-1006">.NET Option</span></span> |  <span data-ttu-id="d1790-1007">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1007">Default value</span></span> | <span data-ttu-id="d1790-1008">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d1790-1009">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d1790-1010">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-1011">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-1012">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d1790-1013">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1013">Empty</span></span> | <span data-ttu-id="d1790-1014">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d1790-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d1790-1015">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1015">Empty</span></span> | <span data-ttu-id="d1790-1016">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d1790-1017">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1017">Empty</span></span> | <span data-ttu-id="d1790-1018">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d1790-1019">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-1019">5 seconds</span></span> | <span data-ttu-id="d1790-1020">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-1020">WebSockets only.</span></span> <span data-ttu-id="d1790-1021">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d1790-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d1790-1022">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d1790-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d1790-1023">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1023">Empty</span></span> | <span data-ttu-id="d1790-1024">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d1790-1025">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d1790-1026">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="d1790-1027">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d1790-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d1790-1028">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="d1790-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d1790-1029">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d1790-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d1790-1030">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d1790-1031">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d1790-1032">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d1790-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d1790-1033">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d1790-1034">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d1790-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d1790-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-1036">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-1036">JavaScript Option</span></span> | <span data-ttu-id="d1790-1037">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1037">Default Value</span></span> | <span data-ttu-id="d1790-1038">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d1790-1039">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d1790-1040">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-1041">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-1042">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-1043">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-1044">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d1790-1044">Java Option</span></span> | <span data-ttu-id="d1790-1045">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1045">Default Value</span></span> | <span data-ttu-id="d1790-1046">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d1790-1047">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d1790-1048">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-1049">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-1050">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d1790-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d1790-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d1790-1052">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1052">Empty</span></span> | <span data-ttu-id="d1790-1053">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d1790-1054">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d1790-1055">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d1790-1056">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d1790-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d1790-1057">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d1790-1058">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d1790-1059">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d1790-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d1790-1060">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d1790-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d1790-1061">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d1790-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d1790-1062">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d1790-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d1790-1063">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d1790-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d1790-1064">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d1790-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d1790-1065">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="d1790-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d1790-1066">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d1790-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d1790-1067">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d1790-1068">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d1790-1069">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d1790-1069">MessagePack serialization options</span></span>

<span data-ttu-id="d1790-1070">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d1790-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d1790-1071">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d1790-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-1072">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d1790-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d1790-1073">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d1790-1073">Configure server options</span></span>

<span data-ttu-id="d1790-1074">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="d1790-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d1790-1075">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-1075">Option</span></span> | <span data-ttu-id="d1790-1076">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1076">Default Value</span></span> | <span data-ttu-id="d1790-1077">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="d1790-1078">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-1078">15 seconds</span></span> | <span data-ttu-id="d1790-1079">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d1790-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d1790-1080">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-1081">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d1790-1082">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-1082">15 seconds</span></span> | <span data-ttu-id="d1790-1083">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d1790-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d1790-1084">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d1790-1085">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="d1790-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d1790-1086">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d1790-1086">All installed protocols</span></span> | <span data-ttu-id="d1790-1087">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="d1790-1088">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d1790-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d1790-1089">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d1790-1090">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d1790-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d1790-1091">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="d1790-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d1790-1092">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="d1790-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d1790-1093">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d1790-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d1790-1094">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d1790-1095">Essas opções são configuradas passando um delegado [para\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="d1790-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d1790-1096">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d1790-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d1790-1097">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-1097">Option</span></span> | <span data-ttu-id="d1790-1098">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1098">Default Value</span></span> | <span data-ttu-id="d1790-1099">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d1790-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-1100">32 KB</span></span> | <span data-ttu-id="d1790-1101">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d1790-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d1790-1102">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d1790-1103">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d1790-1104">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d1790-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d1790-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="d1790-1105">32 KB</span></span> | <span data-ttu-id="d1790-1106">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d1790-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d1790-1107">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d1790-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d1790-1108">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d1790-1108">All Transports are enabled.</span></span> | <span data-ttu-id="d1790-1109">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d1790-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d1790-1110">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-1110">See below.</span></span> | <span data-ttu-id="d1790-1111">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d1790-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d1790-1112">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d1790-1112">See below.</span></span> | <span data-ttu-id="d1790-1113">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d1790-1114">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d1790-1115">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-1115">Option</span></span> | <span data-ttu-id="d1790-1116">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1116">Default Value</span></span> | <span data-ttu-id="d1790-1117">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d1790-1118">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-1118">90 seconds</span></span> | <span data-ttu-id="d1790-1119">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d1790-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d1790-1120">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d1790-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d1790-1121">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="d1790-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d1790-1122">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-1122">Option</span></span> | <span data-ttu-id="d1790-1123">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1123">Default Value</span></span> | <span data-ttu-id="d1790-1124">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d1790-1125">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-1125">5 seconds</span></span> | <span data-ttu-id="d1790-1126">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d1790-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d1790-1127">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d1790-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d1790-1128">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d1790-1129">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d1790-1129">Configure client options</span></span>

<span data-ttu-id="d1790-1130">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d1790-1131">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d1790-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d1790-1132">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d1790-1132">Configure logging</span></span>

<span data-ttu-id="d1790-1133">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="d1790-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d1790-1134">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d1790-1135">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d1790-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d1790-1136">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d1790-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d1790-1137">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d1790-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d1790-1138">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d1790-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d1790-1139">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d1790-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d1790-1140">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1790-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d1790-1141">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d1790-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d1790-1142">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1790-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d1790-1143">Para desabilitar completamente o registro em `signalR.LogLevel.None` log, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="d1790-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d1790-1144">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d1790-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d1790-1145">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d1790-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d1790-1146">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d1790-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d1790-1147">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="d1790-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d1790-1148">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d1790-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d1790-1149">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d1790-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d1790-1150">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d1790-1150">Configure allowed transports</span></span>

<span data-ttu-id="d1790-1151">Os transportes usados pelo Signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d1790-1152">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d1790-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d1790-1153">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d1790-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="d1790-1154">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="d1790-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d1790-1155">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d1790-1156">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d1790-1156">Configure bearer authentication</span></span>

<span data-ttu-id="d1790-1157">Para fornecer dados de autenticação juntamente com solicitações do Signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d1790-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d1790-1158">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="d1790-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d1790-1159">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d1790-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d1790-1160">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="d1790-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d1790-1161">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d1790-1162">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d1790-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d1790-1163">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d1790-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d1790-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [>única\<de cadeia de caracteres ](https://reactivex.io/documentation/single.html)de RxJava.</span><span class="sxs-lookup"><span data-stu-id="d1790-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d1790-1165">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d1790-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d1790-1166">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d1790-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d1790-1167">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d1790-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-1169">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-1169">Option</span></span> | <span data-ttu-id="d1790-1170">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1170">Default value</span></span> | <span data-ttu-id="d1790-1171">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d1790-1172">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-1173">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-1173">Timeout for server activity.</span></span> <span data-ttu-id="d1790-1174">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-1175">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-1176">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d1790-1177">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-1177">15 seconds</span></span> | <span data-ttu-id="d1790-1178">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-1179">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d1790-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d1790-1180">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-1181">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="d1790-1182">No cliente .NET, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="d1790-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d1790-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-1184">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-1184">Option</span></span> | <span data-ttu-id="d1790-1185">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1185">Default value</span></span> | <span data-ttu-id="d1790-1186">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d1790-1187">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-1188">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-1188">Timeout for server activity.</span></span> <span data-ttu-id="d1790-1189">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d1790-1190">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-1191">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-1192">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-1193">Opção</span><span class="sxs-lookup"><span data-stu-id="d1790-1193">Option</span></span> | <span data-ttu-id="d1790-1194">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1194">Default value</span></span> | <span data-ttu-id="d1790-1195">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d1790-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d1790-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d1790-1197">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d1790-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d1790-1198">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-1198">Timeout for server activity.</span></span> <span data-ttu-id="d1790-1199">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-1200">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d1790-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d1790-1201">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d1790-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d1790-1202">15 s</span><span class="sxs-lookup"><span data-stu-id="d1790-1202">15 seconds</span></span> | <span data-ttu-id="d1790-1203">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d1790-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="d1790-1204">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d1790-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d1790-1205">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d1790-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d1790-1206">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d1790-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d1790-1207">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-1207">Configure additional options</span></span>

<span data-ttu-id="d1790-1208">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d1790-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d1790-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="d1790-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d1790-1210">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d1790-1210">.NET Option</span></span> |  <span data-ttu-id="d1790-1211">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1211">Default value</span></span> | <span data-ttu-id="d1790-1212">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d1790-1213">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d1790-1214">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-1215">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-1216">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d1790-1217">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1217">Empty</span></span> | <span data-ttu-id="d1790-1218">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d1790-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d1790-1219">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1219">Empty</span></span> | <span data-ttu-id="d1790-1220">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d1790-1221">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1221">Empty</span></span> | <span data-ttu-id="d1790-1222">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d1790-1223">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d1790-1223">5 seconds</span></span> | <span data-ttu-id="d1790-1224">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-1224">WebSockets only.</span></span> <span data-ttu-id="d1790-1225">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d1790-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d1790-1226">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d1790-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d1790-1227">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1227">Empty</span></span> | <span data-ttu-id="d1790-1228">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d1790-1229">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d1790-1230">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="d1790-1231">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d1790-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d1790-1232">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="d1790-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d1790-1233">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d1790-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d1790-1234">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d1790-1235">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d1790-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d1790-1236">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d1790-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d1790-1237">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d1790-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d1790-1238">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d1790-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d1790-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d1790-1240">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1790-1240">JavaScript Option</span></span> | <span data-ttu-id="d1790-1241">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1241">Default Value</span></span> | <span data-ttu-id="d1790-1242">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d1790-1243">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d1790-1244">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-1245">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-1246">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d1790-1247">Java</span><span class="sxs-lookup"><span data-stu-id="d1790-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="d1790-1248">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d1790-1248">Java Option</span></span> | <span data-ttu-id="d1790-1249">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="d1790-1249">Default Value</span></span> | <span data-ttu-id="d1790-1250">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1790-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d1790-1251">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d1790-1252">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d1790-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d1790-1253">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d1790-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d1790-1254">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="d1790-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d1790-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d1790-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d1790-1256">Vazio</span><span class="sxs-lookup"><span data-stu-id="d1790-1256">Empty</span></span> | <span data-ttu-id="d1790-1257">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1790-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d1790-1258">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d1790-1259">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="d1790-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d1790-1260">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d1790-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d1790-1261">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d1790-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
