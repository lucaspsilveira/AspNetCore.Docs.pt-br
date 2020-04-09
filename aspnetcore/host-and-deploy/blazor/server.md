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
# <a name="host-and-deploy-opno-locblazor-server"></a>Hospedar e Blazor implantar o Servidor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

Os aplicativos do servidor podem aceitar [valores de configuração do Host genéricos](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Implantação

Usando o [ Blazor modelo de hospedagem do Servidor,](xref:blazor/hosting-models#blazor-server) Blazor é executado no servidor a partir de um aplicativo ASP.NET Core. Atualizações de iu, tratamento de eventos e chamadas [SignalR](xref:signalr/introduction) JavaScript são tratadas por uma conexão.

É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core. O Visual Studio ** Blazor ** inclui o modelo de projeto server app (modelo`blazorserverside` ao usar o novo comando [dotnet).](/dotnet/core/tools/dotnet-new)

## <a name="scalability"></a>Escalabilidade

Planeje uma implantação para fazer o melhor Blazor uso da infra-estrutura disponível para um aplicativo server. Veja os seguintes Blazor recursos para abordar a escalabilidade do aplicativo do Servidor:

* [Fundamentos dos Blazor aplicativos do Servidor](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Servidor de implantação

Ao considerar a escalabilidade de um único servidor (escalar), a memória disponível para um aplicativo é provavelmente o primeiro recurso que o aplicativo irá esgotar à medida que as demandas do usuário aumentam. A memória disponível no servidor afeta o:

* Número de circuitos ativos que um servidor pode suportar.
* Latência de ui sobre o cliente.

Para obter orientações sobre Blazor a construção <xref:security/blazor/server>de aplicativos de servidor seguros e escaláveis, consulte .

Cada circuito usa aproximadamente 250 KB de memória para um aplicativo mínimo estilo *Hello World.* O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção do estado associados a cada componente. Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infra-estrutura, mas a seguinte linha de base pode ser um ponto de partida no planejamento de sua meta de implantação: Se você espera que seu aplicativo suporte 5.000 usuários simultâneos, considere orçar pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~273 KB por usuário).

### <a name="opno-locsignalr-configuration"></a>SignalRConfiguração

BlazorOs aplicativos do SignalR servidor usam ASP.NET Core para se comunicar com o navegador. [As condições de hospedagem e dimensionamento se aplicam aos aplicativos do Server. SignalR](xref:signalr/publish-to-azure-web-app) Blazor

Blazorfunciona melhor ao usar WebSockets como SignalR transporte devido à menor latência, confiabilidade e [segurança](xref:signalr/security). A pesquisa longa SignalR é usada quando os WebSockets não estão disponíveis ou quando o aplicativo está configurado explicitamente para usar a Pesquisa Longa. Ao implantar no Azure App Service, configure o aplicativo para usar WebSockets nas configurações do portal Azure para o serviço. Para obter detalhes sobre a configuração do aplicativo para o Azure App Service, consulte as [ SignalR diretrizes de publicação](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-opno-locsignalr-service"></a>Serviço Azure SignalR

Recomendamos o uso dos Blazor aplicativos [Azure SignalR Service](/azure/azure-signalr) for Server. O serviço permite dimensionar um Blazor aplicativo server para SignalR um grande número de conexões simultâneas. Além disso, SignalR o alcance global do serviço e os data centers de alto desempenho ajudam significativamente na redução da latência devido à geografia. Para configurar um aplicativo (e opcionalmente provisionar) o Serviço Azure: SignalR

1. Habilite o serviço para suportar *sessões pegajosas,* onde os clientes são [redirecionados de volta para o mesmo servidor ao pré-renderização](xref:blazor/hosting-models#connection-to-the-server). Defina `ServerStickyMode` a opção `Required`ou o valor de configuração para . Normalmente, um aplicativo cria a configuração usando **uma** das seguintes abordagens:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuração (use **uma** das seguintes abordagens):
  
     * *appsettings.json:*

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Configuração**configuração** **do** > aplicativo do aplicativo no portal `Azure:SignalR:ServerStickyMode`Azure `Required`(**Nome**: , **Valor**: ).

1. Crie um perfil de publicação do Azure Apps no Visual Studio para o Blazor aplicativo Server.
1. Adicione a dependência **do Serviço SignalR Azure** ao perfil. Se a assinatura do Azure não tiver uma SignalR instância de serviço azure pré-existente para atribuir ao aplicativo, selecione **Criar uma nova instância do Azure SignalR Service** para prover uma nova instância de serviço.
1. Publicar o aplicativo no Azure.

#### <a name="iis"></a>IIS

Ao usar o IIS, habilite:

* [WebSockets no IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessões pegajosas com roteamento de solicitação de aplicativo](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Crie uma definição de ingress com as seguintes [anotações kubernetes para sessões pegajosas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

#### <a name="linux-with-nginx"></a>Linux com o Nginx

Para SignalR que os WebSockets funcionem corretamente, confirme se os proxy's `Upgrade` e `Connection` os cabeçalhos estão definidos para os seguintes valores e que `$connection_upgrade` estão mapeados para:

* O valor do cabeçalho de upgrade por padrão.
* `close`quando o cabeçalho Atualizar estiver faltando ou vazio.

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

Para obter mais informações, consulte os seguintes artigos:

* [NGINX como proxy de Soquete Web](https://www.nginx.com/blog/websocket-nginx/)
* [Proxying WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Medir a latência da rede

[O interop JS](xref:blazor/call-javascript-from-dotnet) pode ser usado para medir a latência da rede, como o exemplo a seguir demonstra:

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

Para uma experiência de IU razoável, recomendamos uma latência sustentada de IU de 250ms ou menos.
