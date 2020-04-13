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
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223979"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9b523-103">Configuração do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9b523-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9b523-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9b523-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9b523-105">ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9b523-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9b523-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="9b523-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9b523-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="9b523-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9b523-108">`AddJsonProtocol`pode ser adicionado após `Startup.ConfigureServices` [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in .</span><span class="sxs-lookup"><span data-stu-id="9b523-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9b523-109">O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto.</span><span class="sxs-lookup"><span data-stu-id="9b523-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9b523-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto é um objeto que pode ser usado para configurar serialização de argumentos e valores de devolução.</span><span class="sxs-lookup"><span data-stu-id="9b523-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9b523-111">Para obter mais informações, consulte a [documentação System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9b523-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9b523-112">Como exemplo, para configurar o serializador para não alterar o invólucro de nomes de propriedade, em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9b523-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9b523-113">No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9b523-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9b523-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="9b523-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9b523-115">Não é possível configurar a serialização JSON no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="9b523-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9b523-116">Mude para Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="9b523-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9b523-117">Se você precisar `Newtonsoft.Json` de recursos que `System.Text.Json`não são suportados em , consulte [Switch para Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9b523-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9b523-118">Opções de serialização do MessagePack</span><span class="sxs-lookup"><span data-stu-id="9b523-118">MessagePack serialization options</span></span>

<span data-ttu-id="9b523-119">A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="9b523-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9b523-120">Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="9b523-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9b523-121">Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="9b523-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9b523-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="9b523-122">Configure server options</span></span>

<span data-ttu-id="9b523-123">A tabela a seguir descreve opções para configurar hubs SignalR:</span><span class="sxs-lookup"><span data-stu-id="9b523-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9b523-124">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-124">Option</span></span> | <span data-ttu-id="9b523-125">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-125">Default Value</span></span> | <span data-ttu-id="9b523-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9b523-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-127">30 seconds</span></span> | <span data-ttu-id="9b523-128">O servidor considerará o cliente desconectado se não tiver recebido uma mensagem (incluindo manter-se vivo) neste intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9b523-129">Pode levar mais tempo do que esse intervalo de tempo para que o cliente seja realmente marcado desconectado, devido à forma como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="9b523-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9b523-130">O valor recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="9b523-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9b523-131">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-131">15 seconds</span></span> | <span data-ttu-id="9b523-132">Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="9b523-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9b523-133">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-134">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b523-135">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-135">15 seconds</span></span> | <span data-ttu-id="9b523-136">Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="9b523-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9b523-137">Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="9b523-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9b523-138">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="9b523-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9b523-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="9b523-139">All installed protocols</span></span> | <span data-ttu-id="9b523-140">Protocolos suportados por este hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9b523-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="9b523-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9b523-142">Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9b523-143">O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="9b523-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9b523-144">O número máximo de itens que podem ser protegidos para fluxos de upload do cliente.</span><span class="sxs-lookup"><span data-stu-id="9b523-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9b523-145">Se esse limite for atingido, o processamento das invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="9b523-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9b523-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b523-146">32 KB</span></span> | <span data-ttu-id="9b523-147">Tamanho máximo de uma única mensagem de hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="9b523-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9b523-148">As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .</span><span class="sxs-lookup"><span data-stu-id="9b523-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9b523-149">As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="9b523-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9b523-150">Opções avançadas de configuração HTTP</span><span class="sxs-lookup"><span data-stu-id="9b523-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9b523-151">Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9b523-152">Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="9b523-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9b523-153">A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="9b523-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9b523-154">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-154">Option</span></span> | <span data-ttu-id="9b523-155">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-155">Default Value</span></span> | <span data-ttu-id="9b523-156">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9b523-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b523-157">32 KB</span></span> | <span data-ttu-id="9b523-158">O número máximo de bytes recebidos do cliente que o buffer do servidor buffers antes de aplicar backpressure.</span><span class="sxs-lookup"><span data-stu-id="9b523-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9b523-159">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar backpressure, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9b523-160">Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9b523-161">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9b523-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b523-162">32 KB</span></span> | <span data-ttu-id="9b523-163">O número máximo de bytes enviados pelo aplicativo que o servidor buffers antes de observar a pressão de fundo.</span><span class="sxs-lookup"><span data-stu-id="9b523-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9b523-164">Aumentar esse valor permite que o servidor tampone mensagens maiores mais rapidamente sem esperar a pressão de fundo, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9b523-165">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="9b523-165">All Transports are enabled.</span></span> | <span data-ttu-id="9b523-166">Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="9b523-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9b523-167">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="9b523-167">See below.</span></span> | <span data-ttu-id="9b523-168">Opções adicionais específicas para o transporte de votação longa.</span><span class="sxs-lookup"><span data-stu-id="9b523-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9b523-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="9b523-169">See below.</span></span> | <span data-ttu-id="9b523-170">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9b523-171">O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="9b523-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9b523-172">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-172">Option</span></span> | <span data-ttu-id="9b523-173">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-173">Default Value</span></span> | <span data-ttu-id="9b523-174">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9b523-175">90 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-175">90 seconds</span></span> | <span data-ttu-id="9b523-176">A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9b523-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9b523-177">A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="9b523-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9b523-178">O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="9b523-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9b523-179">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-179">Option</span></span> | <span data-ttu-id="9b523-180">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-180">Default Value</span></span> | <span data-ttu-id="9b523-181">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9b523-182">5 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-182">5 seconds</span></span> | <span data-ttu-id="9b523-183">Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="9b523-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9b523-184">Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="9b523-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9b523-185">O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="9b523-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9b523-186">Configurar opções de cliente</span><span class="sxs-lookup"><span data-stu-id="9b523-186">Configure client options</span></span>

<span data-ttu-id="9b523-187">As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9b523-188">Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.</span><span class="sxs-lookup"><span data-stu-id="9b523-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9b523-189">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="9b523-189">Configure logging</span></span>

<span data-ttu-id="9b523-190">O registro é configurado no `ConfigureLogging` Cliente .NET usando o método.</span><span class="sxs-lookup"><span data-stu-id="9b523-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9b523-191">Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9b523-192">Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9b523-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9b523-193">Para registrar provedores de registro, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="9b523-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9b523-194">Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="9b523-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9b523-195">Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="9b523-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9b523-196">Ligue `AddConsole` para o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="9b523-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9b523-197">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="9b523-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9b523-198">Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="9b523-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9b523-199">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="9b523-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9b523-200">Em vez `LogLevel` de um valor, `string` você também pode fornecer um valor representando um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="9b523-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9b523-201">Isso é útil ao configurar o login do SignalR em `LogLevel` ambientes onde você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="9b523-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9b523-202">A tabela a seguir lista os níveis de registro disponíveis.</span><span class="sxs-lookup"><span data-stu-id="9b523-202">The following table lists the available log levels.</span></span> <span data-ttu-id="9b523-203">O valor que `configureLogging` você fornece para definir o nível **mínimo** de registro que será registrado.</span><span class="sxs-lookup"><span data-stu-id="9b523-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9b523-204">As mensagens registradas neste nível, **ou os níveis listados após ela na tabela,** serão registradas.</span><span class="sxs-lookup"><span data-stu-id="9b523-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9b523-205">String</span><span class="sxs-lookup"><span data-stu-id="9b523-205">String</span></span>                      | <span data-ttu-id="9b523-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9b523-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9b523-207">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="9b523-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9b523-208">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="9b523-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9b523-209">Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="9b523-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9b523-210">Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9b523-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9b523-211">O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro.</span><span class="sxs-lookup"><span data-stu-id="9b523-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9b523-212">É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro.</span><span class="sxs-lookup"><span data-stu-id="9b523-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9b523-213">O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9b523-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9b523-214">Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="9b523-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9b523-215">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="9b523-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9b523-216">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="9b523-216">Configure allowed transports</span></span>

<span data-ttu-id="9b523-217">Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9b523-218">Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="9b523-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9b523-219">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="9b523-219">All transports are enabled by default.</span></span>

<span data-ttu-id="9b523-220">Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:</span><span class="sxs-lookup"><span data-stu-id="9b523-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9b523-221">No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:</span><span class="sxs-lookup"><span data-stu-id="9b523-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9b523-222">Nesta versão do cliente Java, os websockets são os únicos transportes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="9b523-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9b523-223">No cliente Java, o transporte `withTransport` é selecionado `HttpHubConnectionBuilder`com o método no .</span><span class="sxs-lookup"><span data-stu-id="9b523-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9b523-224">O cliente Java é padrão ao usar o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9b523-225">O cliente SignalR Java ainda não suporta retorno de transporte.</span><span class="sxs-lookup"><span data-stu-id="9b523-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9b523-226">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="9b523-226">Configure bearer authentication</span></span>

<span data-ttu-id="9b523-227">Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="9b523-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9b523-228">No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9b523-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9b523-229">No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets).</span><span class="sxs-lookup"><span data-stu-id="9b523-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9b523-230">Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .</span><span class="sxs-lookup"><span data-stu-id="9b523-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9b523-231">No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9b523-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9b523-232">No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :</span><span class="sxs-lookup"><span data-stu-id="9b523-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9b523-233">No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9b523-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9b523-234">Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9b523-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9b523-235">Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="9b523-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9b523-236">Configure opções de tempo-out e mantenha-se viva</span><span class="sxs-lookup"><span data-stu-id="9b523-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9b523-237">Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="9b523-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b523-238">.NET</span><span class="sxs-lookup"><span data-stu-id="9b523-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b523-239">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-239">Option</span></span> | <span data-ttu-id="9b523-240">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-240">Default value</span></span> | <span data-ttu-id="9b523-241">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9b523-242">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-243">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-243">Timeout for server activity.</span></span> <span data-ttu-id="9b523-244">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b523-245">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-246">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9b523-247">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-247">15 seconds</span></span> | <span data-ttu-id="9b523-248">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b523-249">Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b523-250">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-251">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b523-252">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-252">15 seconds</span></span> | <span data-ttu-id="9b523-253">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="9b523-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b523-254">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b523-255">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="9b523-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9b523-256">No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="9b523-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9b523-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b523-258">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-258">Option</span></span> | <span data-ttu-id="9b523-259">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-259">Default value</span></span> | <span data-ttu-id="9b523-260">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9b523-261">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-262">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-262">Timeout for server activity.</span></span> <span data-ttu-id="9b523-263">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9b523-264">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-265">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9b523-266">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b523-267">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="9b523-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b523-268">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b523-269">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="9b523-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b523-270">Java</span><span class="sxs-lookup"><span data-stu-id="9b523-270">Java</span></span>](#tab/java)

| <span data-ttu-id="9b523-271">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-271">Option</span></span> | <span data-ttu-id="9b523-272">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-272">Default value</span></span> | <span data-ttu-id="9b523-273">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9b523-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9b523-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9b523-275">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-276">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-276">Timeout for server activity.</span></span> <span data-ttu-id="9b523-277">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9b523-278">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-279">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9b523-280">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-280">15 seconds</span></span> | <span data-ttu-id="9b523-281">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b523-282">Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9b523-283">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-284">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9b523-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9b523-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9b523-286">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b523-287">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="9b523-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b523-288">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b523-289">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="9b523-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9b523-290">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="9b523-290">Configure additional options</span></span>

<span data-ttu-id="9b523-291">Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="9b523-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b523-292">.NET</span><span class="sxs-lookup"><span data-stu-id="9b523-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b523-293">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="9b523-293">.NET Option</span></span> |  <span data-ttu-id="9b523-294">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-294">Default value</span></span> | <span data-ttu-id="9b523-295">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9b523-296">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9b523-297">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-298">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-299">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9b523-300">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-300">Empty</span></span> | <span data-ttu-id="9b523-301">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="9b523-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9b523-302">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-302">Empty</span></span> | <span data-ttu-id="9b523-303">Uma coleção de cookies HTTP para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9b523-304">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-304">Empty</span></span> | <span data-ttu-id="9b523-305">Credenciais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9b523-306">5 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-306">5 seconds</span></span> | <span data-ttu-id="9b523-307">Apenas WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-307">WebSockets only.</span></span> <span data-ttu-id="9b523-308">O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="9b523-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9b523-309">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="9b523-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9b523-310">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-310">Empty</span></span> | <span data-ttu-id="9b523-311">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9b523-312">Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9b523-313">Não usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9b523-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="9b523-314">Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9b523-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9b523-315">Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="9b523-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9b523-316">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="9b523-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9b523-317">Um proxy HTTP para usar ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9b523-318">Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9b523-319">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="9b523-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9b523-320">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9b523-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9b523-321">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="9b523-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9b523-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b523-323">Opção JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-323">JavaScript Option</span></span> | <span data-ttu-id="9b523-324">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-324">Default Value</span></span> | <span data-ttu-id="9b523-325">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9b523-326">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9b523-327">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-328">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-329">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b523-330">Java</span><span class="sxs-lookup"><span data-stu-id="9b523-330">Java</span></span>](#tab/java)

| <span data-ttu-id="9b523-331">Opção Java</span><span class="sxs-lookup"><span data-stu-id="9b523-331">Java Option</span></span> | <span data-ttu-id="9b523-332">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-332">Default Value</span></span> | <span data-ttu-id="9b523-333">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9b523-334">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9b523-335">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-336">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-337">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9b523-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9b523-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9b523-339">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-339">Empty</span></span> | <span data-ttu-id="9b523-340">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9b523-341">No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="9b523-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9b523-342">No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:</span><span class="sxs-lookup"><span data-stu-id="9b523-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9b523-343">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9b523-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9b523-344">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9b523-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9b523-345">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9b523-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9b523-346">ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9b523-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9b523-347">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="9b523-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9b523-348">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que pode ser adicionado após [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="9b523-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9b523-349">O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto.</span><span class="sxs-lookup"><span data-stu-id="9b523-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9b523-350">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto `JsonSerializerSettings` é um objeto JSON.NET que pode ser usado para configurar serialização de argumentos e valores de devolução.</span><span class="sxs-lookup"><span data-stu-id="9b523-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9b523-351">Para obter mais informações, consulte a [documentação JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9b523-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9b523-352">Como exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez `Startup.ConfigureServices`dos nomes padrão "camelCase", use o código a seguir em :</span><span class="sxs-lookup"><span data-stu-id="9b523-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9b523-353">No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9b523-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9b523-354">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="9b523-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9b523-355">Não é possível configurar a serialização JSON no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="9b523-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9b523-356">Opções de serialização do MessagePack</span><span class="sxs-lookup"><span data-stu-id="9b523-356">MessagePack serialization options</span></span>

<span data-ttu-id="9b523-357">A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="9b523-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9b523-358">Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="9b523-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9b523-359">Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="9b523-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9b523-360">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="9b523-360">Configure server options</span></span>

<span data-ttu-id="9b523-361">A tabela a seguir descreve opções para configurar hubs SignalR:</span><span class="sxs-lookup"><span data-stu-id="9b523-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9b523-362">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-362">Option</span></span> | <span data-ttu-id="9b523-363">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-363">Default Value</span></span> | <span data-ttu-id="9b523-364">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9b523-365">30 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-365">30 seconds</span></span> | <span data-ttu-id="9b523-366">O servidor considerará o cliente desconectado se não tiver recebido uma mensagem (incluindo manter-se vivo) neste intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9b523-367">Pode levar mais tempo do que esse intervalo de tempo para que o cliente seja realmente marcado desconectado, devido à forma como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="9b523-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9b523-368">O valor recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="9b523-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9b523-369">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-369">15 seconds</span></span> | <span data-ttu-id="9b523-370">Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="9b523-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9b523-371">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-372">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b523-373">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-373">15 seconds</span></span> | <span data-ttu-id="9b523-374">Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="9b523-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9b523-375">Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="9b523-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9b523-376">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="9b523-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9b523-377">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="9b523-377">All installed protocols</span></span> | <span data-ttu-id="9b523-378">Protocolos suportados por este hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-378">Protocols supported by this hub.</span></span> <span data-ttu-id="9b523-379">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="9b523-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9b523-380">Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9b523-381">O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="9b523-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9b523-382">As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .</span><span class="sxs-lookup"><span data-stu-id="9b523-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9b523-383">As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="9b523-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9b523-384">Opções avançadas de configuração HTTP</span><span class="sxs-lookup"><span data-stu-id="9b523-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9b523-385">Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9b523-386">Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="9b523-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9b523-387">A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="9b523-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9b523-388">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-388">Option</span></span> | <span data-ttu-id="9b523-389">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-389">Default Value</span></span> | <span data-ttu-id="9b523-390">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9b523-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b523-391">32 KB</span></span> | <span data-ttu-id="9b523-392">O número máximo de bytes recebidos do cliente que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="9b523-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9b523-393">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9b523-394">Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9b523-395">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9b523-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b523-396">32 KB</span></span> | <span data-ttu-id="9b523-397">O número máximo de bytes enviados pelo aplicativo que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="9b523-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9b523-398">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9b523-399">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="9b523-399">All Transports are enabled.</span></span> | <span data-ttu-id="9b523-400">Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="9b523-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9b523-401">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="9b523-401">See below.</span></span> | <span data-ttu-id="9b523-402">Opções adicionais específicas para o transporte de votação longa.</span><span class="sxs-lookup"><span data-stu-id="9b523-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9b523-403">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="9b523-403">See below.</span></span> | <span data-ttu-id="9b523-404">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9b523-405">O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="9b523-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9b523-406">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-406">Option</span></span> | <span data-ttu-id="9b523-407">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-407">Default Value</span></span> | <span data-ttu-id="9b523-408">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9b523-409">90 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-409">90 seconds</span></span> | <span data-ttu-id="9b523-410">A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9b523-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9b523-411">A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="9b523-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9b523-412">O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="9b523-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9b523-413">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-413">Option</span></span> | <span data-ttu-id="9b523-414">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-414">Default Value</span></span> | <span data-ttu-id="9b523-415">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9b523-416">5 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-416">5 seconds</span></span> | <span data-ttu-id="9b523-417">Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="9b523-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9b523-418">Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="9b523-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9b523-419">O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="9b523-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9b523-420">Configurar opções de cliente</span><span class="sxs-lookup"><span data-stu-id="9b523-420">Configure client options</span></span>

<span data-ttu-id="9b523-421">As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9b523-422">Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.</span><span class="sxs-lookup"><span data-stu-id="9b523-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9b523-423">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="9b523-423">Configure logging</span></span>

<span data-ttu-id="9b523-424">O registro é configurado no `ConfigureLogging` Cliente .NET usando o método.</span><span class="sxs-lookup"><span data-stu-id="9b523-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9b523-425">Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9b523-426">Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9b523-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9b523-427">Para registrar provedores de registro, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="9b523-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9b523-428">Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="9b523-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9b523-429">Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="9b523-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9b523-430">Ligue `AddConsole` para o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="9b523-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9b523-431">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="9b523-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9b523-432">Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="9b523-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9b523-433">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="9b523-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9b523-434">Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="9b523-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9b523-435">Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9b523-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9b523-436">O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro.</span><span class="sxs-lookup"><span data-stu-id="9b523-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9b523-437">É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro.</span><span class="sxs-lookup"><span data-stu-id="9b523-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9b523-438">O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9b523-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9b523-439">Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="9b523-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9b523-440">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="9b523-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9b523-441">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="9b523-441">Configure allowed transports</span></span>

<span data-ttu-id="9b523-442">Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9b523-443">Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="9b523-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9b523-444">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="9b523-444">All transports are enabled by default.</span></span>

<span data-ttu-id="9b523-445">Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:</span><span class="sxs-lookup"><span data-stu-id="9b523-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9b523-446">No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:</span><span class="sxs-lookup"><span data-stu-id="9b523-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9b523-447">Nesta versão do cliente Java, os websockets são os únicos transportes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="9b523-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9b523-448">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="9b523-448">Configure bearer authentication</span></span>

<span data-ttu-id="9b523-449">Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="9b523-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9b523-450">No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9b523-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9b523-451">No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets).</span><span class="sxs-lookup"><span data-stu-id="9b523-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9b523-452">Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .</span><span class="sxs-lookup"><span data-stu-id="9b523-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9b523-453">No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9b523-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9b523-454">No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :</span><span class="sxs-lookup"><span data-stu-id="9b523-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9b523-455">No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9b523-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9b523-456">Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9b523-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9b523-457">Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="9b523-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9b523-458">Configure opções de tempo-out e mantenha-se viva</span><span class="sxs-lookup"><span data-stu-id="9b523-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9b523-459">Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="9b523-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b523-460">.NET</span><span class="sxs-lookup"><span data-stu-id="9b523-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b523-461">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-461">Option</span></span> | <span data-ttu-id="9b523-462">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-462">Default value</span></span> | <span data-ttu-id="9b523-463">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9b523-464">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-465">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-465">Timeout for server activity.</span></span> <span data-ttu-id="9b523-466">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b523-467">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-468">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9b523-469">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-469">15 seconds</span></span> | <span data-ttu-id="9b523-470">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b523-471">Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b523-472">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-473">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b523-474">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-474">15 seconds</span></span> | <span data-ttu-id="9b523-475">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="9b523-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b523-476">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b523-477">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="9b523-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9b523-478">No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="9b523-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9b523-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b523-480">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-480">Option</span></span> | <span data-ttu-id="9b523-481">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-481">Default value</span></span> | <span data-ttu-id="9b523-482">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9b523-483">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-484">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-484">Timeout for server activity.</span></span> <span data-ttu-id="9b523-485">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9b523-486">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-487">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9b523-488">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b523-489">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="9b523-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b523-490">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b523-491">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="9b523-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b523-492">Java</span><span class="sxs-lookup"><span data-stu-id="9b523-492">Java</span></span>](#tab/java)

| <span data-ttu-id="9b523-493">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-493">Option</span></span> | <span data-ttu-id="9b523-494">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-494">Default value</span></span> | <span data-ttu-id="9b523-495">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9b523-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9b523-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9b523-497">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-498">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-498">Timeout for server activity.</span></span> <span data-ttu-id="9b523-499">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9b523-500">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-501">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9b523-502">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-502">15 seconds</span></span> | <span data-ttu-id="9b523-503">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b523-504">Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9b523-505">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-506">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9b523-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9b523-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9b523-508">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b523-509">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="9b523-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b523-510">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="9b523-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b523-511">Se o cliente não enviou uma `ClientTimeoutInterval` mensagem no conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="9b523-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9b523-512">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="9b523-512">Configure additional options</span></span>

<span data-ttu-id="9b523-513">Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="9b523-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b523-514">.NET</span><span class="sxs-lookup"><span data-stu-id="9b523-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b523-515">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="9b523-515">.NET Option</span></span> |  <span data-ttu-id="9b523-516">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-516">Default value</span></span> | <span data-ttu-id="9b523-517">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9b523-518">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9b523-519">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-520">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-521">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9b523-522">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-522">Empty</span></span> | <span data-ttu-id="9b523-523">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="9b523-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9b523-524">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-524">Empty</span></span> | <span data-ttu-id="9b523-525">Uma coleção de cookies HTTP para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9b523-526">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-526">Empty</span></span> | <span data-ttu-id="9b523-527">Credenciais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9b523-528">5 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-528">5 seconds</span></span> | <span data-ttu-id="9b523-529">Apenas WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-529">WebSockets only.</span></span> <span data-ttu-id="9b523-530">O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="9b523-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9b523-531">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="9b523-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9b523-532">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-532">Empty</span></span> | <span data-ttu-id="9b523-533">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9b523-534">Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9b523-535">Não usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9b523-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="9b523-536">Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9b523-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9b523-537">Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="9b523-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9b523-538">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="9b523-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9b523-539">Um proxy HTTP para usar ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9b523-540">Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9b523-541">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="9b523-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9b523-542">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9b523-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9b523-543">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="9b523-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9b523-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b523-545">Opção JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-545">JavaScript Option</span></span> | <span data-ttu-id="9b523-546">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-546">Default Value</span></span> | <span data-ttu-id="9b523-547">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9b523-548">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9b523-549">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-550">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-551">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b523-552">Java</span><span class="sxs-lookup"><span data-stu-id="9b523-552">Java</span></span>](#tab/java)

| <span data-ttu-id="9b523-553">Opção Java</span><span class="sxs-lookup"><span data-stu-id="9b523-553">Java Option</span></span> | <span data-ttu-id="9b523-554">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-554">Default Value</span></span> | <span data-ttu-id="9b523-555">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9b523-556">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9b523-557">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-558">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-559">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9b523-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9b523-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9b523-561">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-561">Empty</span></span> | <span data-ttu-id="9b523-562">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9b523-563">No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="9b523-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9b523-564">No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:</span><span class="sxs-lookup"><span data-stu-id="9b523-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9b523-565">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9b523-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9b523-566">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9b523-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9b523-567">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9b523-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9b523-568">ASP.NET Core SignalR suporta dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9b523-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9b523-569">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="9b523-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9b523-570">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que pode ser adicionado após [addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="9b523-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9b523-571">O `AddJsonProtocol` método requer um `options` delegado que recebe um objeto.</span><span class="sxs-lookup"><span data-stu-id="9b523-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9b523-572">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto `JsonSerializerSettings` é um objeto JSON.NET que pode ser usado para configurar serialização de argumentos e valores de devolução.</span><span class="sxs-lookup"><span data-stu-id="9b523-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9b523-573">Para obter mais informações, consulte a [documentação JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9b523-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9b523-574">Como exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez `Startup.ConfigureServices`dos nomes padrão "camelCase", use o código a seguir em :</span><span class="sxs-lookup"><span data-stu-id="9b523-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9b523-575">No cliente .NET, `AddJsonProtocol` o mesmo método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9b523-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9b523-576">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="9b523-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9b523-577">Não é possível configurar a serialização JSON no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="9b523-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9b523-578">Opções de serialização do MessagePack</span><span class="sxs-lookup"><span data-stu-id="9b523-578">MessagePack serialization options</span></span>

<span data-ttu-id="9b523-579">A serialização messagePack pode ser configurada fornecendo um delegado à chamada [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="9b523-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9b523-580">Consulte [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="9b523-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9b523-581">Não é possível configurar a serialização do MessagePack no cliente JavaScript no momento.</span><span class="sxs-lookup"><span data-stu-id="9b523-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9b523-582">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="9b523-582">Configure server options</span></span>

<span data-ttu-id="9b523-583">A tabela a seguir descreve opções para configurar hubs SignalR:</span><span class="sxs-lookup"><span data-stu-id="9b523-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9b523-584">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-584">Option</span></span> | <span data-ttu-id="9b523-585">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-585">Default Value</span></span> | <span data-ttu-id="9b523-586">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9b523-587">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-587">15 seconds</span></span> | <span data-ttu-id="9b523-588">Se o cliente não enviar uma mensagem inicial de aperto de mão dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="9b523-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9b523-589">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-590">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b523-591">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-591">15 seconds</span></span> | <span data-ttu-id="9b523-592">Se o servidor não enviou uma mensagem nesse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="9b523-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9b523-593">Ao `KeepAliveInterval`mudar, `ServerTimeout` / `serverTimeoutInMilliseconds` altere a configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="9b523-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9b523-594">O valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado é `KeepAliveInterval` o dobro do valor.</span><span class="sxs-lookup"><span data-stu-id="9b523-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9b523-595">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="9b523-595">All installed protocols</span></span> | <span data-ttu-id="9b523-596">Protocolos suportados por este hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-596">Protocols supported by this hub.</span></span> <span data-ttu-id="9b523-597">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos desta lista para desativar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="9b523-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9b523-598">Se `true`, mensagens de exceção detalhadas são devolvidas aos clientes quando uma exceção é lançada em um método hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9b523-599">O padrão `false`é, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="9b523-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9b523-600">As opções podem ser configuradas para todos `AddSignalR` os `Startup.ConfigureServices`hubs, fornecendo um delegado de opções para a chamada .</span><span class="sxs-lookup"><span data-stu-id="9b523-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9b523-601">As opções para um único hub `AddSignalR` sobrepõem as opções globais fornecidas e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="9b523-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9b523-602">Opções avançadas de configuração HTTP</span><span class="sxs-lookup"><span data-stu-id="9b523-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9b523-603">Use `HttpConnectionDispatcherOptions` para configurar configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9b523-604">Essas opções são configuradas passando um delegado `Startup.Configure`para [mapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="9b523-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9b523-605">A tabela a seguir descreve opções para configurar ASP.NET opções HTTP avançadas do Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="9b523-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9b523-606">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-606">Option</span></span> | <span data-ttu-id="9b523-607">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-607">Default Value</span></span> | <span data-ttu-id="9b523-608">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9b523-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b523-609">32 KB</span></span> | <span data-ttu-id="9b523-610">O número máximo de bytes recebidos do cliente que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="9b523-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9b523-611">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9b523-612">Os dados coletados `Authorize` automaticamente a partir dos atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9b523-613">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao hub.</span><span class="sxs-lookup"><span data-stu-id="9b523-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9b523-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b523-614">32 KB</span></span> | <span data-ttu-id="9b523-615">O número máximo de bytes enviados pelo aplicativo que o servidor buffers.</span><span class="sxs-lookup"><span data-stu-id="9b523-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9b523-616">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode impactar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="9b523-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9b523-617">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="9b523-617">All Transports are enabled.</span></span> | <span data-ttu-id="9b523-618">Um pouco sinaliza `HttpTransportType` o enum de valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="9b523-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9b523-619">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="9b523-619">See below.</span></span> | <span data-ttu-id="9b523-620">Opções adicionais específicas para o transporte de votação longa.</span><span class="sxs-lookup"><span data-stu-id="9b523-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9b523-621">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="9b523-621">See below.</span></span> | <span data-ttu-id="9b523-622">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9b523-623">O transporte de votação longa tem opções adicionais que podem ser configuradas usando a `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="9b523-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9b523-624">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-624">Option</span></span> | <span data-ttu-id="9b523-625">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-625">Default Value</span></span> | <span data-ttu-id="9b523-626">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9b523-627">90 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-627">90 seconds</span></span> | <span data-ttu-id="9b523-628">A quantidade máxima de tempo que o servidor espera por uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9b523-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9b523-629">A diminuição desse valor faz com que o cliente emita novas solicitações de pesquisa com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="9b523-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9b523-630">O transporte WebSocket tem opções adicionais `WebSockets` que podem ser configuradas usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="9b523-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9b523-631">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-631">Option</span></span> | <span data-ttu-id="9b523-632">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-632">Default Value</span></span> | <span data-ttu-id="9b523-633">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9b523-634">5 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-634">5 seconds</span></span> | <span data-ttu-id="9b523-635">Depois que o servidor fecha, se o cliente não fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="9b523-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9b523-636">Um delegado que pode ser `Sec-WebSocket-Protocol` usado para definir o cabeçalho como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="9b523-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9b523-637">O delegado recebe os valores solicitados pelo cliente como entrada e espera-se que devolva o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="9b523-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9b523-638">Configurar opções de cliente</span><span class="sxs-lookup"><span data-stu-id="9b523-638">Configure client options</span></span>

<span data-ttu-id="9b523-639">As opções do cliente `HubConnectionBuilder` podem ser configuradas no tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9b523-640">Ele também está disponível no cliente `HttpHubConnectionBuilder` Java, mas a subclasse é o `HubConnection` que contém as opções de configuração do construtor, bem como no próprio.</span><span class="sxs-lookup"><span data-stu-id="9b523-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9b523-641">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="9b523-641">Configure logging</span></span>

<span data-ttu-id="9b523-642">O registro é configurado no `ConfigureLogging` Cliente .NET usando o método.</span><span class="sxs-lookup"><span data-stu-id="9b523-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9b523-643">Os provedores de registro e os filtros podem ser registrados da mesma forma que estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9b523-644">Consulte a [documentação do Centro de login ASP.NET](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9b523-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9b523-645">Para registrar provedores de registro, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="9b523-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9b523-646">Consulte [a seção Provedores de registro incorporados](xref:fundamentals/logging/index#built-in-logging-providers) dos docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="9b523-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9b523-647">Por exemplo, para habilitar `Microsoft.Extensions.Logging.Console` o registro do console, instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="9b523-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9b523-648">Ligue `AddConsole` para o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="9b523-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9b523-649">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="9b523-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9b523-650">Forneça `LogLevel` um valor que indique o nível mínimo de mensagens de registro a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="9b523-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9b523-651">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="9b523-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9b523-652">Para desativar totalmente o `signalR.LogLevel.None` registro, `configureLogging` especifique no método.</span><span class="sxs-lookup"><span data-stu-id="9b523-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9b523-653">Para obter mais informações sobre o registro, consulte a [documentação SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9b523-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9b523-654">O cliente SignalR Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro.</span><span class="sxs-lookup"><span data-stu-id="9b523-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9b523-655">É uma API de registro de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de registro específico, trazendo uma dependência específica de registro.</span><span class="sxs-lookup"><span data-stu-id="9b523-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9b523-656">O trecho de código a `java.util.logging` seguir mostra como usar com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9b523-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9b523-657">Se você não configurar o login em suas dependências, o SLF4J carregará um logger padrão sem operação com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="9b523-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9b523-658">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="9b523-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9b523-659">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="9b523-659">Configure allowed transports</span></span>

<span data-ttu-id="9b523-660">Os transportes usados pelo SignalR `WithUrl` podem`withUrl` ser configurados na chamada (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9b523-661">Um bitwise-OR dos `HttpTransportType` valores de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="9b523-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9b523-662">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="9b523-662">All transports are enabled by default.</span></span>

<span data-ttu-id="9b523-663">Por exemplo, desativar o transporte de eventos enviados pelo servidor, mas permitir conexões de Soquetes web e pesquisa longa:</span><span class="sxs-lookup"><span data-stu-id="9b523-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9b523-664">No cliente JavaScript, os transportes são `transport` configurados definindo o `withUrl`campo no objeto de opções fornecido para:</span><span class="sxs-lookup"><span data-stu-id="9b523-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9b523-665">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="9b523-665">Configure bearer authentication</span></span>

<span data-ttu-id="9b523-666">Para fornecer dados de autenticação junto `AccessTokenProvider` com`accessTokenFactory` as solicitações signalR, use a opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="9b523-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9b523-667">No .NET Client, este token de acesso é passado como um token HTTP "Autenticação do Portador" (Usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9b523-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9b523-668">No cliente JavaScript, o token de acesso é usado como um token bearer, **exceto** em alguns casos em que as APIs do navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados por servidor e solicitações websockets).</span><span class="sxs-lookup"><span data-stu-id="9b523-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9b523-669">Nesses casos, o token de acesso é fornecido `access_token`como um valor de seqüência de consulta .</span><span class="sxs-lookup"><span data-stu-id="9b523-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9b523-670">No cliente .NET, `AccessTokenProvider` a opção pode ser especificada usando o delegado de opções em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="9b523-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9b523-671">No cliente JavaScript, o token de acesso `accessTokenFactory` é configurado `withUrl`definindo o campo no objeto opções em :</span><span class="sxs-lookup"><span data-stu-id="9b523-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9b523-672">No cliente SignalR Java, você pode configurar um token portador para ser usado para autenticação, fornecendo uma fábrica de tokens de acesso ao [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9b523-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9b523-673">Use [comAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [>de cadeia única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="9b523-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9b523-674">Com uma chamada para [Single.deadiar,](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="9b523-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9b523-675">Configure opções de tempo-out e mantenha-se viva</span><span class="sxs-lookup"><span data-stu-id="9b523-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9b523-676">Opções adicionais para configurar o tempo hádiato e o comportamento de manter-se vivo estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="9b523-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b523-677">.NET</span><span class="sxs-lookup"><span data-stu-id="9b523-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b523-678">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-678">Option</span></span> | <span data-ttu-id="9b523-679">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-679">Default value</span></span> | <span data-ttu-id="9b523-680">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9b523-681">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-682">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-682">Timeout for server activity.</span></span> <span data-ttu-id="9b523-683">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `Closed` considerará`onclose` o servidor desconectado e aciona o evento (em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b523-684">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-685">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9b523-686">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-686">15 seconds</span></span> | <span data-ttu-id="9b523-687">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b523-688">Se o servidor não enviar uma resposta de aperto de mão neste `Closed` intervalo, o cliente cancela o aperto de mão e aciona o evento (em`onclose` JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9b523-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b523-689">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-690">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9b523-691">No Cliente .NET, os valores `TimeSpan` de tempo são especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="9b523-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9b523-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b523-693">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-693">Option</span></span> | <span data-ttu-id="9b523-694">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-694">Default value</span></span> | <span data-ttu-id="9b523-695">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9b523-696">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-697">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-697">Timeout for server activity.</span></span> <span data-ttu-id="9b523-698">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onclose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9b523-699">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-700">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor para permitir que o tempo para pings chegue.</span><span class="sxs-lookup"><span data-stu-id="9b523-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b523-701">Java</span><span class="sxs-lookup"><span data-stu-id="9b523-701">Java</span></span>](#tab/java)

| <span data-ttu-id="9b523-702">Opção</span><span class="sxs-lookup"><span data-stu-id="9b523-702">Option</span></span> | <span data-ttu-id="9b523-703">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-703">Default value</span></span> | <span data-ttu-id="9b523-704">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9b523-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9b523-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9b523-706">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="9b523-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b523-707">Tempo para atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-707">Timeout for server activity.</span></span> <span data-ttu-id="9b523-708">Se o servidor não enviou uma mensagem nesse intervalo, o cliente `onClose` considerará o servidor desconectado e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9b523-709">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo.</span><span class="sxs-lookup"><span data-stu-id="9b523-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b523-710">O valor recomendado é um número pelo `KeepAliveInterval` menos o dobro do valor do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="9b523-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9b523-711">15 s</span><span class="sxs-lookup"><span data-stu-id="9b523-711">15 seconds</span></span> | <span data-ttu-id="9b523-712">Tempo para aperto de mão inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="9b523-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b523-713">Se o servidor não enviar uma resposta de aperto de mão neste `onClose` intervalo, o cliente cancela o aperto de mão e aciona o evento.</span><span class="sxs-lookup"><span data-stu-id="9b523-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9b523-714">Este é um ajuste avançado que só deve ser modificado se erros de tempo de aperto de mão estiverem ocorrendo devido à severa latência da rede.</span><span class="sxs-lookup"><span data-stu-id="9b523-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b523-715">Para obter mais detalhes sobre o processo de aperto de mão, consulte a especificação do [protocolo SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9b523-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9b523-716">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="9b523-716">Configure additional options</span></span>

<span data-ttu-id="9b523-717">Opções adicionais podem `WithUrl` ser`withUrl` configuradas no `HubConnectionBuilder` método (em JavaScript) `HttpHubConnectionBuilder` on ou nas várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="9b523-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b523-718">.NET</span><span class="sxs-lookup"><span data-stu-id="9b523-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b523-719">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="9b523-719">.NET Option</span></span> |  <span data-ttu-id="9b523-720">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-720">Default value</span></span> | <span data-ttu-id="9b523-721">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9b523-722">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9b523-723">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-724">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-725">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9b523-726">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-726">Empty</span></span> | <span data-ttu-id="9b523-727">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="9b523-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9b523-728">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-728">Empty</span></span> | <span data-ttu-id="9b523-729">Uma coleção de cookies HTTP para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9b523-730">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-730">Empty</span></span> | <span data-ttu-id="9b523-731">Credenciais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9b523-732">5 segundos</span><span class="sxs-lookup"><span data-stu-id="9b523-732">5 seconds</span></span> | <span data-ttu-id="9b523-733">Apenas WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-733">WebSockets only.</span></span> <span data-ttu-id="9b523-734">O tempo máximo que o cliente espera após o fechamento para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="9b523-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9b523-735">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="9b523-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9b523-736">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-736">Empty</span></span> | <span data-ttu-id="9b523-737">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9b523-738">Um delegado que pode ser usado `HttpMessageHandler` para configurar ou substituir o usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9b523-739">Não usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9b523-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="9b523-740">Este delegado deve retornar um valor não nulo, e ele recebe o valor padrão como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9b523-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9b523-741">Modifique as configurações desse valor padrão e `HttpMessageHandler` retorne-o ou retorne uma nova instância.</span><span class="sxs-lookup"><span data-stu-id="9b523-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9b523-742">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookies e Cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="9b523-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9b523-743">Um proxy HTTP para usar ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9b523-744">Defina este booleano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b523-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9b523-745">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="9b523-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9b523-746">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9b523-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9b523-747">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que podem ser usados para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="9b523-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9b523-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b523-749">Opção JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b523-749">JavaScript Option</span></span> | <span data-ttu-id="9b523-750">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-750">Default Value</span></span> | <span data-ttu-id="9b523-751">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9b523-752">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9b523-753">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-754">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-755">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b523-756">Java</span><span class="sxs-lookup"><span data-stu-id="9b523-756">Java</span></span>](#tab/java)

| <span data-ttu-id="9b523-757">Opção Java</span><span class="sxs-lookup"><span data-stu-id="9b523-757">Java Option</span></span> | <span data-ttu-id="9b523-758">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="9b523-758">Default Value</span></span> | <span data-ttu-id="9b523-759">Descrição</span><span class="sxs-lookup"><span data-stu-id="9b523-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9b523-760">Uma função que retorna uma seqüência que é fornecida como um token de autenticação bearer em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9b523-761">Defina `true` isso para pular a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="9b523-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b523-762">**Somente suportado quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9b523-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b523-763">Essa configuração não pode ser ativada ao usar o Serviço Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b523-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9b523-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9b523-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9b523-765">Vazio</span><span class="sxs-lookup"><span data-stu-id="9b523-765">Empty</span></span> | <span data-ttu-id="9b523-766">Um mapa de cabeçalhos HTTP adicionais para enviar a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b523-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9b523-767">No Cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecidas: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="9b523-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9b523-768">No Cliente JavaScript, essas opções podem ser fornecidas `withUrl`em um objeto JavaScript fornecido para:</span><span class="sxs-lookup"><span data-stu-id="9b523-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9b523-769">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9b523-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9b523-770">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9b523-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
