---
title: Host e implantar Blazor ASP.NET Core Server
author: guardrex
description: Aprenda a hospedar e Blazor implantar um aplicativo server usando ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025422"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="1f68d-103">Hospedar e Blazor implantar o Servidor</span><span class="sxs-lookup"><span data-stu-id="1f68d-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="1f68d-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1f68d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="1f68d-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="1f68d-105">Host configuration values</span></span>

<span data-ttu-id="1f68d-106">Os aplicativos do servidor podem aceitar [valores de configuração do Host genéricos](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="1f68d-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="1f68d-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="1f68d-107">Deployment</span></span>

<span data-ttu-id="1f68d-108">Usando o [ Blazor modelo de hospedagem do Servidor,](xref:blazor/hosting-models#blazor-server) Blazor é executado no servidor a partir de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f68d-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="1f68d-109">Atualizações de iu, tratamento de eventos e chamadas [SignalR](xref:signalr/introduction) JavaScript são tratadas por uma conexão.</span><span class="sxs-lookup"><span data-stu-id="1f68d-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="1f68d-110">É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f68d-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="1f68d-111">O Visual Studio \*\* Blazor \*\* inclui o modelo de projeto server app (modelo`blazorserverside` ao usar o novo comando [dotnet).](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="1f68d-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="1f68d-112">Escalabilidade</span><span class="sxs-lookup"><span data-stu-id="1f68d-112">Scalability</span></span>

<span data-ttu-id="1f68d-113">Planeje uma implantação para fazer o melhor Blazor uso da infra-estrutura disponível para um aplicativo server.</span><span class="sxs-lookup"><span data-stu-id="1f68d-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="1f68d-114">Veja os seguintes Blazor recursos para abordar a escalabilidade do aplicativo do Servidor:</span><span class="sxs-lookup"><span data-stu-id="1f68d-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="1f68d-115">[Fundamentos dos Blazor aplicativos do Servidor](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="1f68d-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="1f68d-116">Servidor de implantação</span><span class="sxs-lookup"><span data-stu-id="1f68d-116">Deployment server</span></span>

<span data-ttu-id="1f68d-117">Ao considerar a escalabilidade de um único servidor (escalar), a memória disponível para um aplicativo é provavelmente o primeiro recurso que o aplicativo irá esgotar à medida que as demandas do usuário aumentam.</span><span class="sxs-lookup"><span data-stu-id="1f68d-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="1f68d-118">A memória disponível no servidor afeta o:</span><span class="sxs-lookup"><span data-stu-id="1f68d-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="1f68d-119">Número de circuitos ativos que um servidor pode suportar.</span><span class="sxs-lookup"><span data-stu-id="1f68d-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="1f68d-120">Latência de ui sobre o cliente.</span><span class="sxs-lookup"><span data-stu-id="1f68d-120">UI latency on the client.</span></span>

<span data-ttu-id="1f68d-121">Para obter orientações sobre Blazor a construção <xref:security/blazor/server>de aplicativos de servidor seguros e escaláveis, consulte .</span><span class="sxs-lookup"><span data-stu-id="1f68d-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="1f68d-122">Cada circuito usa aproximadamente 250 KB de memória para um aplicativo mínimo estilo *Hello World.*</span><span class="sxs-lookup"><span data-stu-id="1f68d-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="1f68d-123">O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção do estado associados a cada componente.</span><span class="sxs-lookup"><span data-stu-id="1f68d-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="1f68d-124">Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infra-estrutura, mas a seguinte linha de base pode ser um ponto de partida no planejamento de sua meta de implantação: Se você espera que seu aplicativo suporte 5.000 usuários simultâneos, considere orçar pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~273 KB por usuário).</span><span class="sxs-lookup"><span data-stu-id="1f68d-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a>SignalR<span data-ttu-id="1f68d-125">Configuração</span><span class="sxs-lookup"><span data-stu-id="1f68d-125"> configuration</span></span>

Blazor<span data-ttu-id="1f68d-126">Os aplicativos do SignalR servidor usam ASP.NET Core para se comunicar com o navegador.</span><span class="sxs-lookup"><span data-stu-id="1f68d-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="1f68d-127">[As condições de hospedagem e dimensionamento se aplicam aos aplicativos do Server. SignalR](xref:signalr/publish-to-azure-web-app) Blazor</span><span class="sxs-lookup"><span data-stu-id="1f68d-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="1f68d-128">funciona melhor ao usar WebSockets como SignalR transporte devido à menor latência, confiabilidade e [segurança](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="1f68d-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="1f68d-129">A pesquisa longa SignalR é usada quando os WebSockets não estão disponíveis ou quando o aplicativo está configurado explicitamente para usar a Pesquisa Longa.</span><span class="sxs-lookup"><span data-stu-id="1f68d-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="1f68d-130">Ao implantar no Azure App Service, configure o aplicativo para usar WebSockets nas configurações do portal Azure para o serviço.</span><span class="sxs-lookup"><span data-stu-id="1f68d-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="1f68d-131">Para obter detalhes sobre a configuração do aplicativo para o Azure App Service, consulte as [ SignalR diretrizes de publicação](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="1f68d-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="1f68d-132">Serviço Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="1f68d-132">Azure SignalR Service</span></span>

<span data-ttu-id="1f68d-133">Recomendamos o uso dos Blazor aplicativos [Azure SignalR Service](/azure/azure-signalr) for Server.</span><span class="sxs-lookup"><span data-stu-id="1f68d-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="1f68d-134">O serviço permite dimensionar um Blazor aplicativo server para SignalR um grande número de conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="1f68d-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="1f68d-135">Além disso, SignalR o alcance global do serviço e os data centers de alto desempenho ajudam significativamente na redução da latência devido à geografia.</span><span class="sxs-lookup"><span data-stu-id="1f68d-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="1f68d-136">Para configurar um aplicativo (e opcionalmente provisionar) o Serviço Azure: SignalR</span><span class="sxs-lookup"><span data-stu-id="1f68d-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="1f68d-137">Habilite o serviço para suportar *sessões pegajosas,* onde os clientes são [redirecionados de volta para o mesmo servidor ao pré-renderização](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="1f68d-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="1f68d-138">Defina `ServerStickyMode` a opção `Required`ou o valor de configuração para .</span><span class="sxs-lookup"><span data-stu-id="1f68d-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="1f68d-139">Normalmente, um aplicativo cria a configuração usando **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="1f68d-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="1f68d-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1f68d-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="1f68d-141">Configuração (use **uma** das seguintes abordagens):</span><span class="sxs-lookup"><span data-stu-id="1f68d-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="1f68d-142">*appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="1f68d-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="1f68d-143">Configuração**configuração** **do** > aplicativo do aplicativo no portal `Azure:SignalR:ServerStickyMode`Azure `Required`(**Nome**: , **Valor**: ).</span><span class="sxs-lookup"><span data-stu-id="1f68d-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="1f68d-144">Crie um perfil de publicação do Azure Apps no Visual Studio para o Blazor aplicativo Server.</span><span class="sxs-lookup"><span data-stu-id="1f68d-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="1f68d-145">Adicione a dependência **do Serviço SignalR Azure** ao perfil.</span><span class="sxs-lookup"><span data-stu-id="1f68d-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="1f68d-146">Se a assinatura do Azure não tiver uma SignalR instância de serviço azure pré-existente para atribuir ao aplicativo, selecione **Criar uma nova instância do Azure SignalR Service** para prover uma nova instância de serviço.</span><span class="sxs-lookup"><span data-stu-id="1f68d-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="1f68d-147">Publicar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="1f68d-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="1f68d-148">IIS</span><span class="sxs-lookup"><span data-stu-id="1f68d-148">IIS</span></span>

<span data-ttu-id="1f68d-149">Ao usar o IIS, habilite:</span><span class="sxs-lookup"><span data-stu-id="1f68d-149">When using IIS, enable:</span></span>

* <span data-ttu-id="1f68d-150">[WebSockets no IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="1f68d-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="1f68d-151">[Sessões pegajosas com roteamento de solicitação de aplicativo](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="1f68d-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="1f68d-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="1f68d-152">Kubernetes</span></span>

<span data-ttu-id="1f68d-153">Crie uma definição de ingress com as seguintes [anotações kubernetes para sessões pegajosas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="1f68d-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="1f68d-154">Linux com o Nginx</span><span class="sxs-lookup"><span data-stu-id="1f68d-154">Linux with Nginx</span></span>

<span data-ttu-id="1f68d-155">Para SignalR que os WebSockets funcionem corretamente, confirme se os proxy's `Upgrade` e `Connection` os cabeçalhos estão definidos para os seguintes valores e que `$connection_upgrade` estão mapeados para:</span><span class="sxs-lookup"><span data-stu-id="1f68d-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="1f68d-156">O valor do cabeçalho de upgrade por padrão.</span><span class="sxs-lookup"><span data-stu-id="1f68d-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="1f68d-157">`close`quando o cabeçalho Atualizar estiver faltando ou vazio.</span><span class="sxs-lookup"><span data-stu-id="1f68d-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="1f68d-158">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="1f68d-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="1f68d-159">NGINX como proxy de Soquete Web</span><span class="sxs-lookup"><span data-stu-id="1f68d-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="1f68d-160">Proxying WebSocket</span><span class="sxs-lookup"><span data-stu-id="1f68d-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="1f68d-161">Medir a latência da rede</span><span class="sxs-lookup"><span data-stu-id="1f68d-161">Measure network latency</span></span>

<span data-ttu-id="1f68d-162">[O interop JS](xref:blazor/call-javascript-from-dotnet) pode ser usado para medir a latência da rede, como o exemplo a seguir demonstra:</span><span class="sxs-lookup"><span data-stu-id="1f68d-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="1f68d-163">Para uma experiência de IU razoável, recomendamos uma latência sustentada de IU de 250ms ou menos.</span><span class="sxs-lookup"><span data-stu-id="1f68d-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
