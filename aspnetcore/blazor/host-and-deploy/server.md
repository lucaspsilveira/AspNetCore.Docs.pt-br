---
title: Hospedar e implantar o Blazor servidor de ASP.NET Core
author: guardrex
description: Saiba como hospedar e implantar um Blazor aplicativo de servidor usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/server
ms.openlocfilehash: c02d005aa5e37bc359e1c104a19c387f1c16fa34
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243545"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="cbb2f-103">Hospedar e implantar Blazor servidor</span><span class="sxs-lookup"><span data-stu-id="cbb2f-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="cbb2f-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cbb2f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="cbb2f-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="cbb2f-105">Host configuration values</span></span>

<span data-ttu-id="cbb2f-106">[ Blazor Aplicativos de servidor](xref:blazor/hosting-models#blazor-server) podem aceitar [valores de configuração de host genéricos](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="cbb2f-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="cbb2f-107">Deployment</span></span>

<span data-ttu-id="cbb2f-108">Usando o [ Blazor modelo de hospedagem do servidor](xref:blazor/hosting-models#blazor-server), o Blazor é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="cbb2f-109">As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma [SignalR](xref:signalr/introduction) conexão.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="cbb2f-110">É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="cbb2f-111">O Visual Studio inclui o modelo de projeto de \*\* Blazor aplicativo do servidor\*\* ( `blazorserverside` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="cbb2f-112">Escalabilidade</span><span class="sxs-lookup"><span data-stu-id="cbb2f-112">Scalability</span></span>

<span data-ttu-id="cbb2f-113">Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="cbb2f-114">Consulte os seguintes recursos para abordar a Blazor escalabilidade do aplicativo do servidor:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="cbb2f-115">[Conceitos básicos de Blazor aplicativos de servidor](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="cbb2f-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="cbb2f-116">Servidor de implantação</span><span class="sxs-lookup"><span data-stu-id="cbb2f-116">Deployment server</span></span>

<span data-ttu-id="cbb2f-117">Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="cbb2f-118">A memória disponível no servidor afeta o:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="cbb2f-119">Número de circuitos ativos aos quais um servidor pode dar suporte.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="cbb2f-120">Latência da interface do usuário no cliente.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-120">UI latency on the client.</span></span>

<span data-ttu-id="cbb2f-121">Para obter orientação sobre a criação de Blazor aplicativos de servidor seguros e escalonáveis, consulte <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="cbb2f-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="cbb2f-122">Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="cbb2f-123">O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="cbb2f-124">Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: se você espera que seu aplicativo ofereça suporte a 5.000 usuários simultâneos, considere o orçamento de pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="cbb2f-125">configuração</span><span class="sxs-lookup"><span data-stu-id="cbb2f-125"> configuration</span></span>

Blazor<span data-ttu-id="cbb2f-126">Os aplicativos de servidor usam ASP.NET Core SignalR para se comunicar com o navegador.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="cbb2f-127">as [ SignalR condições de hospedagem e dimensionamento do](xref:signalr/publish-to-azure-web-app) se aplicam a Blazor Aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="cbb2f-128">funciona melhor ao usar o WebSocket como SignalR transporte devido à latência, confiabilidade e [segurança](xref:signalr/security)menores.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="cbb2f-129">A sondagem longa é usada pelo SignalR quando o WebSockets não está disponível ou quando o aplicativo é explicitamente configurado para usar sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="cbb2f-130">Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="cbb2f-131">Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [ SignalR diretrizes de publicação](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="cbb2f-132">Serviço do Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="cbb2f-132">Azure SignalR Service</span></span>

<span data-ttu-id="cbb2f-133">É recomendável usar [o SignalR serviço do Azure](/azure/azure-signalr) para Blazor aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="cbb2f-134">O serviço permite escalar verticalmente um Blazor aplicativo de servidor para um grande número de conexões simultâneas SignalR .</span><span class="sxs-lookup"><span data-stu-id="cbb2f-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="cbb2f-135">Além disso, o SignalR alcance global do serviço e os data centers de alto desempenho auxiliam significativamente na redução da latência devido à geografia.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="cbb2f-136">Para configurar um aplicativo (e, opcionalmente, provisionar) o serviço do Azure SignalR :</span><span class="sxs-lookup"><span data-stu-id="cbb2f-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="cbb2f-137">Habilite o serviço para dar suporte a *sessões adesivas*, em que os clientes são [redirecionados de volta para o mesmo servidor ao renderizar](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="cbb2f-138">Defina a `ServerStickyMode` opção ou o valor de configuração como `Required` .</span><span class="sxs-lookup"><span data-stu-id="cbb2f-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="cbb2f-139">Normalmente, um aplicativo cria a configuração usando **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="cbb2f-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="cbb2f-141">Configuração (use **uma** das seguintes abordagens):</span><span class="sxs-lookup"><span data-stu-id="cbb2f-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="cbb2f-142">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-142">`appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="cbb2f-143">As configurações do aplicativo de **configuração**do serviço de aplicativo  >  **Application settings** na portal do Azure (**nome**: `Azure:SignalR:ServerStickyMode` , **valor**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="cbb2f-144">Crie um perfil de publicação de aplicativos do Azure no Visual Studio para o Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="cbb2f-145">Adicione a dependência de \*\* SignalR serviço do Azure\*\* ao perfil.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="cbb2f-146">Se a assinatura do Azure não tiver uma instância de serviço do Azure já existente SignalR para atribuir ao aplicativo, selecione **criar uma nova SignalR instância de serviço do Azure** para provisionar uma nova instância de serviço.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="cbb2f-147">Publicar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="cbb2f-148">IIS</span><span class="sxs-lookup"><span data-stu-id="cbb2f-148">IIS</span></span>

<span data-ttu-id="cbb2f-149">Ao usar o IIS, habilite:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-149">When using IIS, enable:</span></span>

* <span data-ttu-id="cbb2f-150">[WebSockets no IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="cbb2f-151">[Sessões adesivas com Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="cbb2f-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="cbb2f-152">Kubernetes</span></span>

<span data-ttu-id="cbb2f-153">Crie uma definição de entrada com as seguintes [anotações de kubernetes para sessões adesivas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="cbb2f-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="cbb2f-154">Linux com o Nginx</span><span class="sxs-lookup"><span data-stu-id="cbb2f-154">Linux with Nginx</span></span>

<span data-ttu-id="cbb2f-155">Para SignalR que o WebSockets funcione corretamente, confirme se os `Upgrade` cabeçalhos e o proxy `Connection` estão definidos com os seguintes valores e se estão `$connection_upgrade` mapeados para:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="cbb2f-156">O valor do cabeçalho de atualização por padrão.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="cbb2f-157">`close`Quando o cabeçalho de atualização está ausente ou vazio.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="cbb2f-158">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="cbb2f-159">NGINX como um proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="cbb2f-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="cbb2f-160">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="cbb2f-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="cbb2f-161">Linux com o Apache</span><span class="sxs-lookup"><span data-stu-id="cbb2f-161">Linux with Apache</span></span>

<span data-ttu-id="cbb2f-162">Para hospedar um Blazor aplicativo por trás do Apache no Linux, configure `ProxyPass` para tráfego HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="cbb2f-163">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-163">In the following example:</span></span>

* <span data-ttu-id="cbb2f-164">O servidor Kestrel está em execução no computador host.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="cbb2f-165">O aplicativo escuta o tráfego na porta 5000.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="cbb2f-166">Habilite os seguintes módulos:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="cbb2f-167">Verifique se há erros de WebSocket no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="cbb2f-168">Erros de exemplo:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-168">Example errors:</span></span>

* <span data-ttu-id="cbb2f-169">O Firefox não pode estabelecer uma conexão com o servidor em ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="cbb2f-170">Erro: falha ao iniciar o transporte ' WebSockets ': erro: erro no transporte.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="cbb2f-171">Erro: falha ao iniciar o transporte ' LongPolling ': TypeError: this. Transport está indefinido</span><span class="sxs-lookup"><span data-stu-id="cbb2f-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="cbb2f-172">Erro: não é possível se conectar ao servidor com nenhum dos transportes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="cbb2f-173">Falha no WebSockets</span><span class="sxs-lookup"><span data-stu-id="cbb2f-173">WebSockets failed</span></span>
* <span data-ttu-id="cbb2f-174">Erro: não é possível enviar dados se a conexão não estiver no estado "conectado".</span><span class="sxs-lookup"><span data-stu-id="cbb2f-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="cbb2f-175">Para obter mais informações, consulte a [documentação do Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="cbb2f-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="cbb2f-176">Medir latência de rede</span><span class="sxs-lookup"><span data-stu-id="cbb2f-176">Measure network latency</span></span>

<span data-ttu-id="cbb2f-177">A [interoperabilidade js](xref:blazor/call-javascript-from-dotnet) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cbb2f-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="cbb2f-178">Para uma experiência de interface do usuário razoável, recomendamos uma latência de interface do usuário sustentada de 250 MS ou menos.</span><span class="sxs-lookup"><span data-stu-id="cbb2f-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
