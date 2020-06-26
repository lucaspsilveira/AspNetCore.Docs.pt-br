---
title: SignalRHospedagem e dimensionamento de produção ASP.NET Core
author: bradygaster
description: Saiba como evitar problemas de desempenho e dimensionamento em aplicativos que usam ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/scale
ms.openlocfilehash: cfa1a4c67649e1816f510a33cc53e559c4a59153
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408676"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="e15f0-103">SignalRHospedagem e dimensionamento de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e15f0-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="e15f0-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse), [Brady GASTER](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="e15f0-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="e15f0-105">Este artigo explica as considerações de hospedagem e dimensionamento para aplicativos de alto tráfego que usam ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="e15f0-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="e15f0-106">Sessões adesivas</span><span class="sxs-lookup"><span data-stu-id="e15f0-106">Sticky Sessions</span></span>

SignalR<span data-ttu-id="e15f0-107">requer que todas as solicitações HTTP para uma conexão específica sejam manipuladas pelo mesmo processo de servidor.</span><span class="sxs-lookup"><span data-stu-id="e15f0-107"> requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="e15f0-108">Quando SignalR o está em execução em um farm de servidores (vários servidores), "sessões adesivas" devem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="e15f0-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="e15f0-109">"Sessões adesivas" também são chamadas de afinidade de sessão por alguns balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="e15f0-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="e15f0-110">Azure App serviço usa [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) para rotear solicitações.</span><span class="sxs-lookup"><span data-stu-id="e15f0-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="e15f0-111">Habilitar a configuração "afinidade de ARR" em seu serviço de Azure App permitirá "sessões adesivas".</span><span class="sxs-lookup"><span data-stu-id="e15f0-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="e15f0-112">As únicas circunstâncias nas quais as sessões adesivas não são necessárias são:</span><span class="sxs-lookup"><span data-stu-id="e15f0-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="e15f0-113">Ao hospedar em um único servidor, em um único processo.</span><span class="sxs-lookup"><span data-stu-id="e15f0-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="e15f0-114">Ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="e15f0-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="e15f0-115">Quando todos os clientes são configurados para usar **apenas** WebSockets **e** a [configuração SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitada na configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="e15f0-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="e15f0-116">Em todas as outras circunstâncias (incluindo quando o backplane Redis é usado), o ambiente de servidor deve ser configurado para sessões adesivas.</span><span class="sxs-lookup"><span data-stu-id="e15f0-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="e15f0-117">Para obter orientação sobre como configurar o serviço de Azure App para o SignalR , consulte <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="e15f0-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="e15f0-118">Recursos de conexão TCP</span><span class="sxs-lookup"><span data-stu-id="e15f0-118">TCP connection resources</span></span>

<span data-ttu-id="e15f0-119">O número de conexões TCP simultâneas às quais um servidor Web pode dar suporte é limitado.</span><span class="sxs-lookup"><span data-stu-id="e15f0-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="e15f0-120">Os clientes HTTP padrão usam conexões *efêmeras* .</span><span class="sxs-lookup"><span data-stu-id="e15f0-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="e15f0-121">Essas conexões podem ser fechadas quando o cliente fica ocioso e reaberto mais tarde.</span><span class="sxs-lookup"><span data-stu-id="e15f0-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="e15f0-122">Por outro lado, uma SignalR conexão é *persistente*.</span><span class="sxs-lookup"><span data-stu-id="e15f0-122">On the other hand, a SignalR connection is *persistent*.</span></span> SignalR<span data-ttu-id="e15f0-123">as conexões permanecem abertas mesmo quando o cliente fica ocioso.</span><span class="sxs-lookup"><span data-stu-id="e15f0-123"> connections stay open even when the client goes idle.</span></span> <span data-ttu-id="e15f0-124">Em um aplicativo de alto tráfego que atende a vários clientes, essas conexões persistentes podem fazer com que os servidores atinjam seu número máximo de conexões.</span><span class="sxs-lookup"><span data-stu-id="e15f0-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="e15f0-125">As conexões persistentes também consomem memória adicional para acompanhar cada conexão.</span><span class="sxs-lookup"><span data-stu-id="e15f0-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="e15f0-126">O uso intensivo de recursos relacionados à conexão SignalR pode afetar outros aplicativos Web hospedados no mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="e15f0-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="e15f0-127">Quando SignalR o é aberto e mantém as últimas conexões TCP disponíveis, outros aplicativos Web no mesmo servidor também não têm mais conexões disponíveis.</span><span class="sxs-lookup"><span data-stu-id="e15f0-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="e15f0-128">Se um servidor ficar sem conexões, você verá erros de soquete aleatórios e erros de redefinição de conexão.</span><span class="sxs-lookup"><span data-stu-id="e15f0-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="e15f0-129">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e15f0-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="e15f0-130">Para impedir SignalR que o uso de recursos cause erros em outros aplicativos Web, execute SignalR em servidores diferentes dos outros aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="e15f0-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="e15f0-131">Para impedir que SignalR o uso de recursos cause erros em um SignalR aplicativo, escale horizontalmente para limitar o número de conexões que um servidor tem para lidar.</span><span class="sxs-lookup"><span data-stu-id="e15f0-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="e15f0-132">Expansão</span><span class="sxs-lookup"><span data-stu-id="e15f0-132">Scale out</span></span>

<span data-ttu-id="e15f0-133">Um aplicativo que usa o SignalR precisa manter o controle de todas as suas conexões, o que cria problemas para um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="e15f0-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="e15f0-134">Adicione um servidor e obtenha novas conexões que os outros servidores não conhecem.</span><span class="sxs-lookup"><span data-stu-id="e15f0-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="e15f0-135">Por exemplo, SignalR em cada servidor no diagrama a seguir, não há conhecimento das conexões nos outros servidores.</span><span class="sxs-lookup"><span data-stu-id="e15f0-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="e15f0-136">Quando SignalR em um dos servidores deseja enviar uma mensagem para todos os clientes, a mensagem vai apenas para os clientes conectados a esse servidor.</span><span class="sxs-lookup"><span data-stu-id="e15f0-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

<span data-ttu-id="e15f0-137">![Dimensionamento SignalR sem um backplane](scale/_static/scale-no-backplane.png)</span><span class="sxs-lookup"><span data-stu-id="e15f0-137">![Scaling SignalR without a backplane](scale/_static/scale-no-backplane.png)</span></span>

<span data-ttu-id="e15f0-138">As opções para resolver esse problema são o [ SignalR serviço do Azure](#azure-signalr-service) e o [backplane do Redis](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="e15f0-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="e15f0-139">Serviço do Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="e15f0-139">Azure SignalR Service</span></span>

<span data-ttu-id="e15f0-140">O serviço do Azure SignalR é um proxy em vez de um backplane.</span><span class="sxs-lookup"><span data-stu-id="e15f0-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="e15f0-141">Cada vez que um cliente inicia uma conexão com o servidor, o cliente é redirecionado para se conectar ao serviço.</span><span class="sxs-lookup"><span data-stu-id="e15f0-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="e15f0-142">Esse processo é ilustrado no diagrama a seguir:</span><span class="sxs-lookup"><span data-stu-id="e15f0-142">That process is illustrated in the following diagram:</span></span>

<span data-ttu-id="e15f0-143">![Estabelecendo uma conexão com o SignalR serviço do Azure](scale/_static/azure-signalr-service-one-connection.png)</span><span class="sxs-lookup"><span data-stu-id="e15f0-143">![Establishing a connection to the Azure SignalR Service](scale/_static/azure-signalr-service-one-connection.png)</span></span>

<span data-ttu-id="e15f0-144">O resultado é que o serviço gerencia todas as conexões de cliente, enquanto cada servidor precisa apenas de um pequeno número constante de conexões com o serviço, conforme mostrado no diagrama a seguir:</span><span class="sxs-lookup"><span data-stu-id="e15f0-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Clientes conectados ao serviço, servidores conectados ao serviço](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="e15f0-146">Essa abordagem de expansão tem várias vantagens em relação à alternativa do backplane Redis:</span><span class="sxs-lookup"><span data-stu-id="e15f0-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="e15f0-147">As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), não são necessárias, porque os clientes são redirecionados imediatamente para o serviço do Azure SignalR quando se conectam.</span><span class="sxs-lookup"><span data-stu-id="e15f0-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="e15f0-148">Um SignalR aplicativo pode escalar horizontalmente com base no número de mensagens enviadas, enquanto o serviço do Azure é SignalR dimensionado automaticamente para lidar com qualquer número de conexões.</span><span class="sxs-lookup"><span data-stu-id="e15f0-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service automatically scales to handle any number of connections.</span></span> <span data-ttu-id="e15f0-149">Por exemplo, pode haver milhares de clientes, mas se apenas algumas mensagens por segundo forem enviadas, o SignalR aplicativo não precisará escalar horizontalmente para vários servidores apenas para lidar com as próprias conexões.</span><span class="sxs-lookup"><span data-stu-id="e15f0-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="e15f0-150">Um SignalR aplicativo não usará significativamente mais recursos de conexão do que um aplicativo Web sem SignalR .</span><span class="sxs-lookup"><span data-stu-id="e15f0-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="e15f0-151">Por esses motivos, recomendamos o serviço do Azure SignalR para todos os SignalR aplicativos ASP.NET Core hospedados no Azure, incluindo serviço de aplicativo, VMS e contêineres.</span><span class="sxs-lookup"><span data-stu-id="e15f0-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="e15f0-152">Para obter mais informações, consulte a [ SignalR documentação do serviço do Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="e15f0-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="e15f0-153">Backplane de Redis</span><span class="sxs-lookup"><span data-stu-id="e15f0-153">Redis backplane</span></span>

<span data-ttu-id="e15f0-154">[Redis](https://redis.io/) é um repositório de chave-valor na memória que dá suporte a um sistema de mensagens com um modelo de publicação/assinatura.</span><span class="sxs-lookup"><span data-stu-id="e15f0-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="e15f0-155">O SignalR backplane Redis usa o recurso pub/sub para encaminhar mensagens para outros servidores.</span><span class="sxs-lookup"><span data-stu-id="e15f0-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="e15f0-156">Quando um cliente faz uma conexão, as informações de conexão são passadas para o backplane.</span><span class="sxs-lookup"><span data-stu-id="e15f0-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="e15f0-157">Quando um servidor deseja enviar uma mensagem a todos os clientes, ele envia ao backplane.</span><span class="sxs-lookup"><span data-stu-id="e15f0-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="e15f0-158">O backplane conhece todos os clientes conectados e em quais servidores eles estão.</span><span class="sxs-lookup"><span data-stu-id="e15f0-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="e15f0-159">Ele envia a mensagem a todos os clientes por meio de seus respectivos servidores.</span><span class="sxs-lookup"><span data-stu-id="e15f0-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="e15f0-160">Esse processo é ilustrado no diagrama a seguir:</span><span class="sxs-lookup"><span data-stu-id="e15f0-160">This process is illustrated in the following diagram:</span></span>

![Redis backplane, mensagem enviada de um servidor para todos os clientes](scale/_static/redis-backplane.png)

<span data-ttu-id="e15f0-162">O backplane Redis é a abordagem de expansão recomendada para aplicativos hospedados em sua própria infraestrutura.</span><span class="sxs-lookup"><span data-stu-id="e15f0-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="e15f0-163">Se houver uma latência de conexão significativa entre o data center e um data center do Azure, o serviço do Azure SignalR poderá não ser uma opção prática para aplicativos locais com requisitos de baixa latência ou alta taxa de transferência.</span><span class="sxs-lookup"><span data-stu-id="e15f0-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="e15f0-164">As vantagens do serviço do Azure SignalR anotadas anteriormente são desvantagens para o Redis backplane:</span><span class="sxs-lookup"><span data-stu-id="e15f0-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="e15f0-165">As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), são necessárias, exceto quando **ambas** das seguintes opções são verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="e15f0-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="e15f0-166">Todos os clientes estão configurados para usar **apenas** WebSockets.</span><span class="sxs-lookup"><span data-stu-id="e15f0-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="e15f0-167">A [configuração SkipNegotiation](xref:signalr/configuration#configure-additional-options) é habilitada na configuração do cliente.</span><span class="sxs-lookup"><span data-stu-id="e15f0-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="e15f0-168">Depois que uma conexão é iniciada em um servidor, a conexão precisa permanecer nesse servidor.</span><span class="sxs-lookup"><span data-stu-id="e15f0-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="e15f0-169">Um SignalR aplicativo deve escalar horizontalmente com base no número de clientes, mesmo que poucas mensagens estejam sendo enviadas.</span><span class="sxs-lookup"><span data-stu-id="e15f0-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="e15f0-170">Um SignalR aplicativo usa significativamente mais recursos de conexão do que um aplicativo Web sem o SignalR .</span><span class="sxs-lookup"><span data-stu-id="e15f0-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="e15f0-171">Limitações do IIS no sistema operacional do cliente Windows</span><span class="sxs-lookup"><span data-stu-id="e15f0-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="e15f0-172">O Windows 10 e o Windows 8. x são sistemas operacionais cliente.</span><span class="sxs-lookup"><span data-stu-id="e15f0-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="e15f0-173">O IIS em sistemas operacionais cliente tem um limite de 10 conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="e15f0-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> SignalR<span data-ttu-id="e15f0-174">as conexões do são:</span><span class="sxs-lookup"><span data-stu-id="e15f0-174">'s connections are:</span></span>

* <span data-ttu-id="e15f0-175">Transitório e frequentemente restabelecida.</span><span class="sxs-lookup"><span data-stu-id="e15f0-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="e15f0-176">**Não** Descartado imediatamente quando não é mais usado.</span><span class="sxs-lookup"><span data-stu-id="e15f0-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="e15f0-177">As condições anteriores tornam possível atingir o limite de 10 conexões em um sistema operacional cliente.</span><span class="sxs-lookup"><span data-stu-id="e15f0-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="e15f0-178">Quando um sistema operacional cliente é usado para desenvolvimento, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="e15f0-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="e15f0-179">Evite o IIS.</span><span class="sxs-lookup"><span data-stu-id="e15f0-179">Avoid IIS.</span></span>
* <span data-ttu-id="e15f0-180">Use Kestrel ou IIS Express como destinos de implantação.</span><span class="sxs-lookup"><span data-stu-id="e15f0-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="e15f0-181">Linux com o Nginx</span><span class="sxs-lookup"><span data-stu-id="e15f0-181">Linux with Nginx</span></span>

<span data-ttu-id="e15f0-182">Defina os `Connection` cabeçalhos e o proxy `Upgrade` para o seguinte para SignalR WebSockets:</span><span class="sxs-lookup"><span data-stu-id="e15f0-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="e15f0-183">Para obter mais informações, consulte [Nginx como um proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="e15f0-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="third-party-signalr-backplane-providers"></a><span data-ttu-id="e15f0-184">SignalRProvedores de backplane de terceiros</span><span class="sxs-lookup"><span data-stu-id="e15f0-184">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="e15f0-185">NCache</span><span class="sxs-lookup"><span data-stu-id="e15f0-185">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="e15f0-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="e15f0-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="e15f0-187">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e15f0-187">Next steps</span></span>

<span data-ttu-id="e15f0-188">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="e15f0-188">For more information, see the following resources:</span></span>

* <span data-ttu-id="e15f0-189">[Documentação do serviço do Azure SignalR](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="e15f0-189">[Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview)</span></span>
* [<span data-ttu-id="e15f0-190">Configurar um backplane Redis</span><span class="sxs-lookup"><span data-stu-id="e15f0-190">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
