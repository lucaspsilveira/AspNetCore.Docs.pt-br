---
title: Configuração de ASP.NET Core SignalR
author: bradygaster
description: Saiba como configurar os SignalR aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406830"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="0bf9b-103">Configuração de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="0bf9b-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0bf9b-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-105">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0bf9b-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0bf9b-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0bf9b-108">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0bf9b-109">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0bf9b-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0bf9b-111">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0bf9b-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="0bf9b-113">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0bf9b-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="0bf9b-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0bf9b-116">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="0bf9b-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0bf9b-117">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0bf9b-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-118">MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0bf9b-120">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0bf9b-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="0bf9b-122">Configure server options</span></span>

<span data-ttu-id="0bf9b-123">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0bf9b-124">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-124">Option</span></span> | <span data-ttu-id="0bf9b-125">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-125">Default Value</span></span> | <span data-ttu-id="0bf9b-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0bf9b-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-127">30 seconds</span></span> | <span data-ttu-id="0bf9b-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0bf9b-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0bf9b-130">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-131">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-131">15 seconds</span></span> | <span data-ttu-id="0bf9b-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0bf9b-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-134">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-135">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-135">15 seconds</span></span> | <span data-ttu-id="0bf9b-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0bf9b-137">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0bf9b-138">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0bf9b-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="0bf9b-139">All installed protocols</span></span> | <span data-ttu-id="0bf9b-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-140">Protocols supported by this hub.</span></span> <span data-ttu-id="0bf9b-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0bf9b-142">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0bf9b-143">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0bf9b-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0bf9b-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0bf9b-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-146">32 KB</span></span> | <span data-ttu-id="0bf9b-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="0bf9b-148">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="0bf9b-149">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0bf9b-150">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="0bf9b-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0bf9b-151">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0bf9b-152">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0bf9b-153">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0bf9b-154">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-154">Option</span></span> | <span data-ttu-id="0bf9b-155">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-155">Default Value</span></span> | <span data-ttu-id="0bf9b-156">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0bf9b-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-157">32 KB</span></span> | <span data-ttu-id="0bf9b-158">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0bf9b-159">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0bf9b-160">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0bf9b-161">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0bf9b-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-162">32 KB</span></span> | <span data-ttu-id="0bf9b-163">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0bf9b-164">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0bf9b-165">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-165">All Transports are enabled.</span></span> | <span data-ttu-id="0bf9b-166">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0bf9b-167">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-167">See below.</span></span> | <span data-ttu-id="0bf9b-168">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0bf9b-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-169">See below.</span></span> | <span data-ttu-id="0bf9b-170">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="0bf9b-171">0</span><span class="sxs-lookup"><span data-stu-id="0bf9b-171">0</span></span> | <span data-ttu-id="0bf9b-172">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="0bf9b-173">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="0bf9b-174">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0bf9b-175">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-175">Option</span></span> | <span data-ttu-id="0bf9b-176">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-176">Default Value</span></span> | <span data-ttu-id="0bf9b-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0bf9b-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-178">90 seconds</span></span> | <span data-ttu-id="0bf9b-179">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0bf9b-180">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0bf9b-181">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0bf9b-182">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-182">Option</span></span> | <span data-ttu-id="0bf9b-183">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-183">Default Value</span></span> | <span data-ttu-id="0bf9b-184">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0bf9b-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-185">5 seconds</span></span> | <span data-ttu-id="0bf9b-186">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0bf9b-187">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0bf9b-188">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0bf9b-189">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="0bf9b-189">Configure client options</span></span>

<span data-ttu-id="0bf9b-190">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0bf9b-191">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0bf9b-192">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="0bf9b-192">Configure logging</span></span>

<span data-ttu-id="0bf9b-193">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0bf9b-194">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0bf9b-195">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-196">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0bf9b-197">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0bf9b-198">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0bf9b-199">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0bf9b-200">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0bf9b-201">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0bf9b-202">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0bf9b-203">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0bf9b-204">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0bf9b-205">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-205">The following table lists the available log levels.</span></span> <span data-ttu-id="0bf9b-206">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0bf9b-207">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0bf9b-208">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="0bf9b-208">String</span></span>                      | <span data-ttu-id="0bf9b-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0bf9b-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0bf9b-210">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0bf9b-211">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0bf9b-212">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0bf9b-213">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0bf9b-214">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0bf9b-215">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0bf9b-216">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0bf9b-217">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0bf9b-218">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0bf9b-219">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-219">Configure allowed transports</span></span>

<span data-ttu-id="0bf9b-220">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0bf9b-221">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0bf9b-222">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-222">All transports are enabled by default.</span></span>

<span data-ttu-id="0bf9b-223">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0bf9b-224">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0bf9b-225">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0bf9b-226">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0bf9b-227">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0bf9b-228">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0bf9b-229">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="0bf9b-229">Configure bearer authentication</span></span>

<span data-ttu-id="0bf9b-230">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0bf9b-231">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0bf9b-232">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0bf9b-233">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0bf9b-234">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0bf9b-235">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0bf9b-236">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0bf9b-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0bf9b-238">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0bf9b-239">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="0bf9b-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0bf9b-240">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-241">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-242">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-242">Option</span></span> | <span data-ttu-id="0bf9b-243">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-243">Default value</span></span> | <span data-ttu-id="0bf9b-244">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0bf9b-245">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-246">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-246">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-247">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-248">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-249">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-250">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-250">15 seconds</span></span> | <span data-ttu-id="0bf9b-251">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-252">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-253">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-254">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-255">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-255">15 seconds</span></span> | <span data-ttu-id="0bf9b-256">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-257">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-258">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0bf9b-259">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-261">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-261">Option</span></span> | <span data-ttu-id="0bf9b-262">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-262">Default value</span></span> | <span data-ttu-id="0bf9b-263">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0bf9b-264">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-265">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-265">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-266">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0bf9b-267">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-268">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0bf9b-269">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-270">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-271">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-272">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-273">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-273">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-274">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-274">Option</span></span> | <span data-ttu-id="0bf9b-275">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-275">Default value</span></span> | <span data-ttu-id="0bf9b-276">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0bf9b-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0bf9b-278">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-279">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-279">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-280">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-281">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-282">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0bf9b-283">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-283">15 seconds</span></span> | <span data-ttu-id="0bf9b-284">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-285">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-286">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-287">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0bf9b-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0bf9b-289">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-290">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-291">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-292">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0bf9b-293">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-293">Configure additional options</span></span>

<span data-ttu-id="0bf9b-294">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-295">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-296">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-296">.NET Option</span></span> |  <span data-ttu-id="0bf9b-297">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-297">Default value</span></span> | <span data-ttu-id="0bf9b-298">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-299">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0bf9b-300">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-301">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-302">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0bf9b-303">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-303">Empty</span></span> | <span data-ttu-id="0bf9b-304">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0bf9b-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-305">Empty</span></span> | <span data-ttu-id="0bf9b-306">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0bf9b-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-307">Empty</span></span> | <span data-ttu-id="0bf9b-308">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0bf9b-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-309">5 seconds</span></span> | <span data-ttu-id="0bf9b-310">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-310">WebSockets only.</span></span> <span data-ttu-id="0bf9b-311">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0bf9b-312">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0bf9b-313">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-313">Empty</span></span> | <span data-ttu-id="0bf9b-314">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0bf9b-315">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0bf9b-316">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="0bf9b-317">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0bf9b-318">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0bf9b-319">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="0bf9b-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0bf9b-320">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0bf9b-321">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0bf9b-322">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0bf9b-323">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0bf9b-324">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-326">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-326">JavaScript Option</span></span> | <span data-ttu-id="0bf9b-327">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-327">Default Value</span></span> | <span data-ttu-id="0bf9b-328">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0bf9b-329">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="0bf9b-330">Dicionário de cabeçalhos enviado com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="0bf9b-331">O envio de cabeçalhos no navegador não funciona para WebSockets ou <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> fluxo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0bf9b-332">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0bf9b-333">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-334">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-335">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="0bf9b-336">Especifica se as credenciais serão enviadas com a solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="0bf9b-337">Azure App serviço usa cookies para sessões adesivas e precisa que essa opção esteja habilitada para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="0bf9b-338">Para obter mais informações sobre o CORS com SignalR , consulte <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-339">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-339">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-340">Opção Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-340">Java Option</span></span> | <span data-ttu-id="0bf9b-341">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-341">Default Value</span></span> | <span data-ttu-id="0bf9b-342">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-343">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0bf9b-344">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-345">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-346">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0bf9b-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0bf9b-348">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-348">Empty</span></span> | <span data-ttu-id="0bf9b-349">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0bf9b-350">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0bf9b-351">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0bf9b-352">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0bf9b-353">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0bf9b-354">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-355">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0bf9b-356">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0bf9b-357">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0bf9b-358">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0bf9b-359">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0bf9b-360">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0bf9b-361">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0bf9b-362">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="0bf9b-363">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0bf9b-364">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="0bf9b-365">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0bf9b-366">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="0bf9b-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0bf9b-367">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0bf9b-368">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-368">MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-369">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0bf9b-370">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-371">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0bf9b-372">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="0bf9b-372">Configure server options</span></span>

<span data-ttu-id="0bf9b-373">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0bf9b-374">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-374">Option</span></span> | <span data-ttu-id="0bf9b-375">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-375">Default Value</span></span> | <span data-ttu-id="0bf9b-376">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0bf9b-377">30 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-377">30 seconds</span></span> | <span data-ttu-id="0bf9b-378">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0bf9b-379">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0bf9b-380">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-381">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-381">15 seconds</span></span> | <span data-ttu-id="0bf9b-382">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0bf9b-383">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-384">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-385">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-385">15 seconds</span></span> | <span data-ttu-id="0bf9b-386">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0bf9b-387">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0bf9b-388">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0bf9b-389">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="0bf9b-389">All installed protocols</span></span> | <span data-ttu-id="0bf9b-390">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-390">Protocols supported by this hub.</span></span> <span data-ttu-id="0bf9b-391">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0bf9b-392">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0bf9b-393">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0bf9b-394">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0bf9b-395">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0bf9b-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-396">32 KB</span></span> | <span data-ttu-id="0bf9b-397">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="0bf9b-398">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="0bf9b-399">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0bf9b-400">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="0bf9b-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0bf9b-401">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0bf9b-402">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0bf9b-403">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0bf9b-404">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-404">Option</span></span> | <span data-ttu-id="0bf9b-405">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-405">Default Value</span></span> | <span data-ttu-id="0bf9b-406">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0bf9b-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-407">32 KB</span></span> | <span data-ttu-id="0bf9b-408">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0bf9b-409">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0bf9b-410">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0bf9b-411">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0bf9b-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-412">32 KB</span></span> | <span data-ttu-id="0bf9b-413">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0bf9b-414">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0bf9b-415">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-415">All Transports are enabled.</span></span> | <span data-ttu-id="0bf9b-416">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0bf9b-417">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-417">See below.</span></span> | <span data-ttu-id="0bf9b-418">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0bf9b-419">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-419">See below.</span></span> | <span data-ttu-id="0bf9b-420">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="0bf9b-421">0</span><span class="sxs-lookup"><span data-stu-id="0bf9b-421">0</span></span> | <span data-ttu-id="0bf9b-422">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="0bf9b-423">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="0bf9b-424">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0bf9b-425">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-425">Option</span></span> | <span data-ttu-id="0bf9b-426">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-426">Default Value</span></span> | <span data-ttu-id="0bf9b-427">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0bf9b-428">90 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-428">90 seconds</span></span> | <span data-ttu-id="0bf9b-429">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0bf9b-430">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0bf9b-431">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0bf9b-432">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-432">Option</span></span> | <span data-ttu-id="0bf9b-433">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-433">Default Value</span></span> | <span data-ttu-id="0bf9b-434">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0bf9b-435">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-435">5 seconds</span></span> | <span data-ttu-id="0bf9b-436">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0bf9b-437">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0bf9b-438">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0bf9b-439">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="0bf9b-439">Configure client options</span></span>

<span data-ttu-id="0bf9b-440">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0bf9b-441">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0bf9b-442">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="0bf9b-442">Configure logging</span></span>

<span data-ttu-id="0bf9b-443">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0bf9b-444">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0bf9b-445">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-446">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0bf9b-447">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0bf9b-448">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0bf9b-449">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0bf9b-450">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0bf9b-451">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0bf9b-452">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0bf9b-453">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0bf9b-454">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0bf9b-455">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-455">The following table lists the available log levels.</span></span> <span data-ttu-id="0bf9b-456">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0bf9b-457">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0bf9b-458">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="0bf9b-458">String</span></span>                      | <span data-ttu-id="0bf9b-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0bf9b-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0bf9b-460">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0bf9b-461">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0bf9b-462">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0bf9b-463">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0bf9b-464">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0bf9b-465">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0bf9b-466">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0bf9b-467">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0bf9b-468">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0bf9b-469">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-469">Configure allowed transports</span></span>

<span data-ttu-id="0bf9b-470">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0bf9b-471">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0bf9b-472">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-472">All transports are enabled by default.</span></span>

<span data-ttu-id="0bf9b-473">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0bf9b-474">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0bf9b-475">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0bf9b-476">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0bf9b-477">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0bf9b-478">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0bf9b-479">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="0bf9b-479">Configure bearer authentication</span></span>

<span data-ttu-id="0bf9b-480">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0bf9b-481">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0bf9b-482">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0bf9b-483">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0bf9b-484">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0bf9b-485">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0bf9b-486">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0bf9b-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0bf9b-488">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0bf9b-489">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="0bf9b-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0bf9b-490">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-491">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-492">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-492">Option</span></span> | <span data-ttu-id="0bf9b-493">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-493">Default value</span></span> | <span data-ttu-id="0bf9b-494">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0bf9b-495">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-496">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-496">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-497">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-498">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-499">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-500">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-500">15 seconds</span></span> | <span data-ttu-id="0bf9b-501">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-502">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-503">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-504">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-505">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-505">15 seconds</span></span> | <span data-ttu-id="0bf9b-506">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-507">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-508">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0bf9b-509">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-511">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-511">Option</span></span> | <span data-ttu-id="0bf9b-512">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-512">Default value</span></span> | <span data-ttu-id="0bf9b-513">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0bf9b-514">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-515">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-515">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-516">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0bf9b-517">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-518">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0bf9b-519">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-520">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-521">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-522">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-523">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-523">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-524">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-524">Option</span></span> | <span data-ttu-id="0bf9b-525">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-525">Default value</span></span> | <span data-ttu-id="0bf9b-526">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0bf9b-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0bf9b-528">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-529">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-529">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-530">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-531">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-532">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0bf9b-533">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-533">15 seconds</span></span> | <span data-ttu-id="0bf9b-534">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-535">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-536">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-537">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0bf9b-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0bf9b-539">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-540">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-541">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-542">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0bf9b-543">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-543">Configure additional options</span></span>

<span data-ttu-id="0bf9b-544">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-545">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-546">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-546">.NET Option</span></span> |  <span data-ttu-id="0bf9b-547">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-547">Default value</span></span> | <span data-ttu-id="0bf9b-548">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-549">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0bf9b-550">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-551">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-552">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0bf9b-553">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-553">Empty</span></span> | <span data-ttu-id="0bf9b-554">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0bf9b-555">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-555">Empty</span></span> | <span data-ttu-id="0bf9b-556">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0bf9b-557">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-557">Empty</span></span> | <span data-ttu-id="0bf9b-558">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0bf9b-559">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-559">5 seconds</span></span> | <span data-ttu-id="0bf9b-560">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-560">WebSockets only.</span></span> <span data-ttu-id="0bf9b-561">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0bf9b-562">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0bf9b-563">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-563">Empty</span></span> | <span data-ttu-id="0bf9b-564">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0bf9b-565">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0bf9b-566">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="0bf9b-567">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0bf9b-568">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0bf9b-569">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="0bf9b-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0bf9b-570">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0bf9b-571">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0bf9b-572">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0bf9b-573">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0bf9b-574">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-576">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-576">JavaScript Option</span></span> | <span data-ttu-id="0bf9b-577">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-577">Default Value</span></span> | <span data-ttu-id="0bf9b-578">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0bf9b-579">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0bf9b-580">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0bf9b-581">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-582">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-583">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-584">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-584">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-585">Opção Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-585">Java Option</span></span> | <span data-ttu-id="0bf9b-586">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-586">Default Value</span></span> | <span data-ttu-id="0bf9b-587">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-588">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0bf9b-589">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-590">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-591">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0bf9b-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0bf9b-593">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-593">Empty</span></span> | <span data-ttu-id="0bf9b-594">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0bf9b-595">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0bf9b-596">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0bf9b-597">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0bf9b-598">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0bf9b-599">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-600">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0bf9b-601">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0bf9b-602">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0bf9b-603">`AddJsonProtocol`pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0bf9b-604">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0bf9b-605">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0bf9b-606">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0bf9b-607">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="0bf9b-608">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0bf9b-609">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="0bf9b-610">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0bf9b-611">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="0bf9b-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0bf9b-612">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0bf9b-613">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-613">MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-614">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0bf9b-615">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-616">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0bf9b-617">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="0bf9b-617">Configure server options</span></span>

<span data-ttu-id="0bf9b-618">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0bf9b-619">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-619">Option</span></span> | <span data-ttu-id="0bf9b-620">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-620">Default Value</span></span> | <span data-ttu-id="0bf9b-621">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0bf9b-622">30 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-622">30 seconds</span></span> | <span data-ttu-id="0bf9b-623">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0bf9b-624">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0bf9b-625">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-626">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-626">15 seconds</span></span> | <span data-ttu-id="0bf9b-627">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0bf9b-628">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-629">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-630">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-630">15 seconds</span></span> | <span data-ttu-id="0bf9b-631">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0bf9b-632">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0bf9b-633">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0bf9b-634">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="0bf9b-634">All installed protocols</span></span> | <span data-ttu-id="0bf9b-635">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-635">Protocols supported by this hub.</span></span> <span data-ttu-id="0bf9b-636">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0bf9b-637">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0bf9b-638">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0bf9b-639">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0bf9b-640">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0bf9b-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-641">32 KB</span></span> | <span data-ttu-id="0bf9b-642">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="0bf9b-643">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="0bf9b-644">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0bf9b-645">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="0bf9b-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0bf9b-646">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0bf9b-647">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0bf9b-648">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0bf9b-649">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-649">Option</span></span> | <span data-ttu-id="0bf9b-650">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-650">Default Value</span></span> | <span data-ttu-id="0bf9b-651">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0bf9b-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-652">32 KB</span></span> | <span data-ttu-id="0bf9b-653">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0bf9b-654">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0bf9b-655">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0bf9b-656">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0bf9b-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-657">32 KB</span></span> | <span data-ttu-id="0bf9b-658">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0bf9b-659">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0bf9b-660">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-660">All Transports are enabled.</span></span> | <span data-ttu-id="0bf9b-661">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0bf9b-662">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-662">See below.</span></span> | <span data-ttu-id="0bf9b-663">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0bf9b-664">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-664">See below.</span></span> | <span data-ttu-id="0bf9b-665">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0bf9b-666">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0bf9b-667">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-667">Option</span></span> | <span data-ttu-id="0bf9b-668">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-668">Default Value</span></span> | <span data-ttu-id="0bf9b-669">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0bf9b-670">90 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-670">90 seconds</span></span> | <span data-ttu-id="0bf9b-671">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0bf9b-672">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0bf9b-673">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0bf9b-674">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-674">Option</span></span> | <span data-ttu-id="0bf9b-675">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-675">Default Value</span></span> | <span data-ttu-id="0bf9b-676">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0bf9b-677">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-677">5 seconds</span></span> | <span data-ttu-id="0bf9b-678">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0bf9b-679">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0bf9b-680">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0bf9b-681">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="0bf9b-681">Configure client options</span></span>

<span data-ttu-id="0bf9b-682">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0bf9b-683">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0bf9b-684">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="0bf9b-684">Configure logging</span></span>

<span data-ttu-id="0bf9b-685">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0bf9b-686">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0bf9b-687">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-688">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0bf9b-689">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0bf9b-690">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0bf9b-691">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0bf9b-692">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0bf9b-693">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0bf9b-694">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0bf9b-695">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0bf9b-696">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0bf9b-697">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-697">The following table lists the available log levels.</span></span> <span data-ttu-id="0bf9b-698">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0bf9b-699">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0bf9b-700">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="0bf9b-700">String</span></span>                      | <span data-ttu-id="0bf9b-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0bf9b-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0bf9b-702">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0bf9b-703">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0bf9b-704">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0bf9b-705">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0bf9b-706">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0bf9b-707">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0bf9b-708">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0bf9b-709">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0bf9b-710">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0bf9b-711">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-711">Configure allowed transports</span></span>

<span data-ttu-id="0bf9b-712">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0bf9b-713">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0bf9b-714">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-714">All transports are enabled by default.</span></span>

<span data-ttu-id="0bf9b-715">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0bf9b-716">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0bf9b-717">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0bf9b-718">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0bf9b-719">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0bf9b-720">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0bf9b-721">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="0bf9b-721">Configure bearer authentication</span></span>

<span data-ttu-id="0bf9b-722">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0bf9b-723">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0bf9b-724">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0bf9b-725">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0bf9b-726">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0bf9b-727">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0bf9b-728">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0bf9b-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0bf9b-730">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0bf9b-731">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="0bf9b-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0bf9b-732">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-733">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-734">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-734">Option</span></span> | <span data-ttu-id="0bf9b-735">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-735">Default value</span></span> | <span data-ttu-id="0bf9b-736">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0bf9b-737">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-738">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-738">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-739">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-740">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-741">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-742">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-742">15 seconds</span></span> | <span data-ttu-id="0bf9b-743">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-744">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-745">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-746">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-747">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-747">15 seconds</span></span> | <span data-ttu-id="0bf9b-748">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-749">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-750">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0bf9b-751">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-753">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-753">Option</span></span> | <span data-ttu-id="0bf9b-754">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-754">Default value</span></span> | <span data-ttu-id="0bf9b-755">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0bf9b-756">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-757">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-757">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-758">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0bf9b-759">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-760">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0bf9b-761">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-762">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-763">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-764">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-765">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-765">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-766">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-766">Option</span></span> | <span data-ttu-id="0bf9b-767">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-767">Default value</span></span> | <span data-ttu-id="0bf9b-768">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0bf9b-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0bf9b-770">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-771">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-771">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-772">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-773">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-774">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0bf9b-775">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-775">15 seconds</span></span> | <span data-ttu-id="0bf9b-776">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-777">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-778">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-779">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0bf9b-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0bf9b-781">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-782">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-783">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-784">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0bf9b-785">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-785">Configure additional options</span></span>

<span data-ttu-id="0bf9b-786">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-787">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-788">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-788">.NET Option</span></span> |  <span data-ttu-id="0bf9b-789">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-789">Default value</span></span> | <span data-ttu-id="0bf9b-790">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-791">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0bf9b-792">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-793">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-794">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0bf9b-795">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-795">Empty</span></span> | <span data-ttu-id="0bf9b-796">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0bf9b-797">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-797">Empty</span></span> | <span data-ttu-id="0bf9b-798">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0bf9b-799">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-799">Empty</span></span> | <span data-ttu-id="0bf9b-800">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0bf9b-801">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-801">5 seconds</span></span> | <span data-ttu-id="0bf9b-802">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-802">WebSockets only.</span></span> <span data-ttu-id="0bf9b-803">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0bf9b-804">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0bf9b-805">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-805">Empty</span></span> | <span data-ttu-id="0bf9b-806">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0bf9b-807">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0bf9b-808">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="0bf9b-809">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0bf9b-810">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0bf9b-811">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="0bf9b-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0bf9b-812">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0bf9b-813">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0bf9b-814">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0bf9b-815">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0bf9b-816">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-818">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-818">JavaScript Option</span></span> | <span data-ttu-id="0bf9b-819">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-819">Default Value</span></span> | <span data-ttu-id="0bf9b-820">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0bf9b-821">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0bf9b-822">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0bf9b-823">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-824">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-825">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-826">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-826">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-827">Opção Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-827">Java Option</span></span> | <span data-ttu-id="0bf9b-828">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-828">Default Value</span></span> | <span data-ttu-id="0bf9b-829">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-830">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0bf9b-831">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-832">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-833">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0bf9b-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0bf9b-835">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-835">Empty</span></span> | <span data-ttu-id="0bf9b-836">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0bf9b-837">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0bf9b-838">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0bf9b-839">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0bf9b-840">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0bf9b-841">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-842">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0bf9b-843">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0bf9b-844">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0bf9b-845">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0bf9b-846">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0bf9b-847">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="0bf9b-848">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="0bf9b-849">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0bf9b-850">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="0bf9b-851">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0bf9b-852">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-852">MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-853">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0bf9b-854">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-855">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0bf9b-856">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="0bf9b-856">Configure server options</span></span>

<span data-ttu-id="0bf9b-857">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0bf9b-858">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-858">Option</span></span> | <span data-ttu-id="0bf9b-859">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-859">Default Value</span></span> | <span data-ttu-id="0bf9b-860">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0bf9b-861">30 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-861">30 seconds</span></span> | <span data-ttu-id="0bf9b-862">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0bf9b-863">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0bf9b-864">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-865">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-865">15 seconds</span></span> | <span data-ttu-id="0bf9b-866">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0bf9b-867">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-868">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-869">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-869">15 seconds</span></span> | <span data-ttu-id="0bf9b-870">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0bf9b-871">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0bf9b-872">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0bf9b-873">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="0bf9b-873">All installed protocols</span></span> | <span data-ttu-id="0bf9b-874">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-874">Protocols supported by this hub.</span></span> <span data-ttu-id="0bf9b-875">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0bf9b-876">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0bf9b-877">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="0bf9b-878">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="0bf9b-879">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0bf9b-880">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="0bf9b-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0bf9b-881">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0bf9b-882">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="0bf9b-883">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0bf9b-884">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-884">Option</span></span> | <span data-ttu-id="0bf9b-885">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-885">Default Value</span></span> | <span data-ttu-id="0bf9b-886">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0bf9b-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-887">32 KB</span></span> | <span data-ttu-id="0bf9b-888">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="0bf9b-889">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0bf9b-890">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0bf9b-891">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0bf9b-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-892">32 KB</span></span> | <span data-ttu-id="0bf9b-893">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="0bf9b-894">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0bf9b-895">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-895">All Transports are enabled.</span></span> | <span data-ttu-id="0bf9b-896">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0bf9b-897">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-897">See below.</span></span> | <span data-ttu-id="0bf9b-898">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0bf9b-899">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-899">See below.</span></span> | <span data-ttu-id="0bf9b-900">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0bf9b-901">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0bf9b-902">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-902">Option</span></span> | <span data-ttu-id="0bf9b-903">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-903">Default Value</span></span> | <span data-ttu-id="0bf9b-904">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0bf9b-905">90 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-905">90 seconds</span></span> | <span data-ttu-id="0bf9b-906">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0bf9b-907">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0bf9b-908">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0bf9b-909">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-909">Option</span></span> | <span data-ttu-id="0bf9b-910">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-910">Default Value</span></span> | <span data-ttu-id="0bf9b-911">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0bf9b-912">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-912">5 seconds</span></span> | <span data-ttu-id="0bf9b-913">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0bf9b-914">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0bf9b-915">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0bf9b-916">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="0bf9b-916">Configure client options</span></span>

<span data-ttu-id="0bf9b-917">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0bf9b-918">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0bf9b-919">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="0bf9b-919">Configure logging</span></span>

<span data-ttu-id="0bf9b-920">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0bf9b-921">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0bf9b-922">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-923">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0bf9b-924">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0bf9b-925">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0bf9b-926">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0bf9b-927">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0bf9b-928">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0bf9b-929">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0bf9b-930">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0bf9b-931">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0bf9b-932">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0bf9b-933">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0bf9b-934">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0bf9b-935">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0bf9b-936">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0bf9b-937">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-937">Configure allowed transports</span></span>

<span data-ttu-id="0bf9b-938">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0bf9b-939">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0bf9b-940">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-940">All transports are enabled by default.</span></span>

<span data-ttu-id="0bf9b-941">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0bf9b-942">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0bf9b-943">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0bf9b-944">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="0bf9b-944">Configure bearer authentication</span></span>

<span data-ttu-id="0bf9b-945">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0bf9b-946">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0bf9b-947">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0bf9b-948">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0bf9b-949">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0bf9b-950">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0bf9b-951">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0bf9b-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0bf9b-953">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0bf9b-954">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="0bf9b-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0bf9b-955">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-956">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-957">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-957">Option</span></span> | <span data-ttu-id="0bf9b-958">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-958">Default value</span></span> | <span data-ttu-id="0bf9b-959">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0bf9b-960">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-961">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-961">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-962">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-963">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-964">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-965">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-965">15 seconds</span></span> | <span data-ttu-id="0bf9b-966">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-967">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-968">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-969">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-970">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-970">15 seconds</span></span> | <span data-ttu-id="0bf9b-971">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-972">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-973">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0bf9b-974">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-976">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-976">Option</span></span> | <span data-ttu-id="0bf9b-977">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-977">Default value</span></span> | <span data-ttu-id="0bf9b-978">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0bf9b-979">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-980">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-980">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-981">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0bf9b-982">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-983">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0bf9b-984">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-985">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-986">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-987">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-988">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-988">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-989">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-989">Option</span></span> | <span data-ttu-id="0bf9b-990">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-990">Default value</span></span> | <span data-ttu-id="0bf9b-991">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0bf9b-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0bf9b-993">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-994">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-994">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-995">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-996">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-997">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0bf9b-998">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-998">15 seconds</span></span> | <span data-ttu-id="0bf9b-999">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-1000">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-1001">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-1002">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0bf9b-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0bf9b-1004">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-1005">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0bf9b-1006">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0bf9b-1007">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0bf9b-1008">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1008">Configure additional options</span></span>

<span data-ttu-id="0bf9b-1009">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-1011">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1011">.NET Option</span></span> |  <span data-ttu-id="0bf9b-1012">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1012">Default value</span></span> | <span data-ttu-id="0bf9b-1013">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-1014">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0bf9b-1015">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-1016">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-1017">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0bf9b-1018">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1018">Empty</span></span> | <span data-ttu-id="0bf9b-1019">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0bf9b-1020">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1020">Empty</span></span> | <span data-ttu-id="0bf9b-1021">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0bf9b-1022">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1022">Empty</span></span> | <span data-ttu-id="0bf9b-1023">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0bf9b-1024">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1024">5 seconds</span></span> | <span data-ttu-id="0bf9b-1025">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1025">WebSockets only.</span></span> <span data-ttu-id="0bf9b-1026">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0bf9b-1027">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0bf9b-1028">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1028">Empty</span></span> | <span data-ttu-id="0bf9b-1029">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0bf9b-1030">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0bf9b-1031">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="0bf9b-1032">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0bf9b-1033">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0bf9b-1034">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0bf9b-1035">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0bf9b-1036">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0bf9b-1037">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0bf9b-1038">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0bf9b-1039">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-1041">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1041">JavaScript Option</span></span> | <span data-ttu-id="0bf9b-1042">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1042">Default Value</span></span> | <span data-ttu-id="0bf9b-1043">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0bf9b-1044">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0bf9b-1045">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0bf9b-1046">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-1047">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-1048">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-1049">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-1050">Opção Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1050">Java Option</span></span> | <span data-ttu-id="0bf9b-1051">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1051">Default Value</span></span> | <span data-ttu-id="0bf9b-1052">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-1053">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0bf9b-1054">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-1055">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-1056">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0bf9b-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0bf9b-1058">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1058">Empty</span></span> | <span data-ttu-id="0bf9b-1059">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0bf9b-1060">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0bf9b-1061">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0bf9b-1062">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0bf9b-1063">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0bf9b-1064">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-1065">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0bf9b-1066">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0bf9b-1067">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0bf9b-1068">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0bf9b-1069">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0bf9b-1070">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="0bf9b-1071">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="0bf9b-1072">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0bf9b-1073">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="0bf9b-1074">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0bf9b-1075">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1075">MessagePack serialization options</span></span>

<span data-ttu-id="0bf9b-1076">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0bf9b-1077">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-1078">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0bf9b-1079">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1079">Configure server options</span></span>

<span data-ttu-id="0bf9b-1080">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0bf9b-1081">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1081">Option</span></span> | <span data-ttu-id="0bf9b-1082">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1082">Default Value</span></span> | <span data-ttu-id="0bf9b-1083">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-1084">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1084">15 seconds</span></span> | <span data-ttu-id="0bf9b-1085">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0bf9b-1086">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-1087">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0bf9b-1088">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1088">15 seconds</span></span> | <span data-ttu-id="0bf9b-1089">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0bf9b-1090">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0bf9b-1091">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0bf9b-1092">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1092">All installed protocols</span></span> | <span data-ttu-id="0bf9b-1093">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="0bf9b-1094">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0bf9b-1095">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0bf9b-1096">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="0bf9b-1097">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="0bf9b-1098">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0bf9b-1099">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0bf9b-1100">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0bf9b-1101">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="0bf9b-1102">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0bf9b-1103">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1103">Option</span></span> | <span data-ttu-id="0bf9b-1104">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1104">Default Value</span></span> | <span data-ttu-id="0bf9b-1105">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0bf9b-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1106">32 KB</span></span> | <span data-ttu-id="0bf9b-1107">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="0bf9b-1108">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0bf9b-1109">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0bf9b-1110">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0bf9b-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1111">32 KB</span></span> | <span data-ttu-id="0bf9b-1112">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="0bf9b-1113">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0bf9b-1114">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1114">All Transports are enabled.</span></span> | <span data-ttu-id="0bf9b-1115">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0bf9b-1116">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1116">See below.</span></span> | <span data-ttu-id="0bf9b-1117">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0bf9b-1118">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1118">See below.</span></span> | <span data-ttu-id="0bf9b-1119">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0bf9b-1120">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0bf9b-1121">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1121">Option</span></span> | <span data-ttu-id="0bf9b-1122">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1122">Default Value</span></span> | <span data-ttu-id="0bf9b-1123">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0bf9b-1124">90 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1124">90 seconds</span></span> | <span data-ttu-id="0bf9b-1125">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0bf9b-1126">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0bf9b-1127">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0bf9b-1128">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1128">Option</span></span> | <span data-ttu-id="0bf9b-1129">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1129">Default Value</span></span> | <span data-ttu-id="0bf9b-1130">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0bf9b-1131">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1131">5 seconds</span></span> | <span data-ttu-id="0bf9b-1132">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0bf9b-1133">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0bf9b-1134">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0bf9b-1135">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1135">Configure client options</span></span>

<span data-ttu-id="0bf9b-1136">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0bf9b-1137">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0bf9b-1138">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1138">Configure logging</span></span>

<span data-ttu-id="0bf9b-1139">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0bf9b-1140">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0bf9b-1141">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0bf9b-1142">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0bf9b-1143">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0bf9b-1144">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0bf9b-1145">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0bf9b-1146">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0bf9b-1147">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0bf9b-1148">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0bf9b-1149">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0bf9b-1150">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0bf9b-1151">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0bf9b-1152">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0bf9b-1153">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0bf9b-1154">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0bf9b-1155">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0bf9b-1156">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1156">Configure allowed transports</span></span>

<span data-ttu-id="0bf9b-1157">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0bf9b-1158">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0bf9b-1159">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="0bf9b-1160">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0bf9b-1161">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0bf9b-1162">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1162">Configure bearer authentication</span></span>

<span data-ttu-id="0bf9b-1163">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0bf9b-1164">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0bf9b-1165">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0bf9b-1166">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0bf9b-1167">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0bf9b-1168">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0bf9b-1169">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0bf9b-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0bf9b-1171">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0bf9b-1172">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0bf9b-1173">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-1175">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1175">Option</span></span> | <span data-ttu-id="0bf9b-1176">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1176">Default value</span></span> | <span data-ttu-id="0bf9b-1177">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0bf9b-1178">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-1179">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1179">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-1180">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-1181">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-1182">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0bf9b-1183">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1183">15 seconds</span></span> | <span data-ttu-id="0bf9b-1184">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-1185">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0bf9b-1186">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-1187">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="0bf9b-1188">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-1190">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1190">Option</span></span> | <span data-ttu-id="0bf9b-1191">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1191">Default value</span></span> | <span data-ttu-id="0bf9b-1192">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0bf9b-1193">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-1194">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1194">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-1195">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0bf9b-1196">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-1197">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-1198">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-1199">Opção</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1199">Option</span></span> | <span data-ttu-id="0bf9b-1200">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1200">Default value</span></span> | <span data-ttu-id="0bf9b-1201">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0bf9b-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0bf9b-1203">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0bf9b-1204">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1204">Timeout for server activity.</span></span> <span data-ttu-id="0bf9b-1205">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-1206">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0bf9b-1207">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` , para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0bf9b-1208">15 s</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1208">15 seconds</span></span> | <span data-ttu-id="0bf9b-1209">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="0bf9b-1210">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0bf9b-1211">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0bf9b-1212">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0bf9b-1213">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1213">Configure additional options</span></span>

<span data-ttu-id="0bf9b-1214">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0bf9b-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0bf9b-1216">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1216">.NET Option</span></span> |  <span data-ttu-id="0bf9b-1217">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1217">Default value</span></span> | <span data-ttu-id="0bf9b-1218">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-1219">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0bf9b-1220">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-1221">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-1222">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0bf9b-1223">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1223">Empty</span></span> | <span data-ttu-id="0bf9b-1224">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0bf9b-1225">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1225">Empty</span></span> | <span data-ttu-id="0bf9b-1226">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0bf9b-1227">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1227">Empty</span></span> | <span data-ttu-id="0bf9b-1228">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0bf9b-1229">5 segundos</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1229">5 seconds</span></span> | <span data-ttu-id="0bf9b-1230">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1230">WebSockets only.</span></span> <span data-ttu-id="0bf9b-1231">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0bf9b-1232">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0bf9b-1233">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1233">Empty</span></span> | <span data-ttu-id="0bf9b-1234">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0bf9b-1235">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0bf9b-1236">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="0bf9b-1237">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0bf9b-1238">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0bf9b-1239">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0bf9b-1240">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0bf9b-1241">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0bf9b-1242">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0bf9b-1243">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0bf9b-1244">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0bf9b-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0bf9b-1246">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1246">JavaScript Option</span></span> | <span data-ttu-id="0bf9b-1247">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1247">Default Value</span></span> | <span data-ttu-id="0bf9b-1248">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0bf9b-1249">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="0bf9b-1250">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0bf9b-1251">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-1252">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-1253">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0bf9b-1254">Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="0bf9b-1255">Opção Java</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1255">Java Option</span></span> | <span data-ttu-id="0bf9b-1256">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1256">Default Value</span></span> | <span data-ttu-id="0bf9b-1257">Descrição</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0bf9b-1258">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0bf9b-1259">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0bf9b-1260">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0bf9b-1261">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0bf9b-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0bf9b-1263">Vazio</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1263">Empty</span></span> | <span data-ttu-id="0bf9b-1264">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0bf9b-1265">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0bf9b-1266">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0bf9b-1267">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0bf9b-1268">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0bf9b-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
