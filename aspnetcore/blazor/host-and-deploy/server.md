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
ms.openlocfilehash: 891714250bc4e8b0b241e589efb268b8f8e9f0d5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103540"
---
# <a name="host-and-deploy-blazor-server"></a>Hospedar e implantar Blazor servidor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

[ Blazor Aplicativos de servidor](xref:blazor/hosting-models#blazor-server) podem aceitar [valores de configuração de host genéricos](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implantação

Usando o [ Blazor modelo de hospedagem do servidor](xref:blazor/hosting-models#blazor-server), o Blazor é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma [SignalR](xref:signalr/introduction) conexão.

É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core. O Visual Studio inclui o modelo de projeto de ** Blazor aplicativo do servidor** ( `blazorserverside` modelo ao usar o comando [dotnet novo](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Escalabilidade

Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um Blazor aplicativo de servidor. Consulte os seguintes recursos para abordar a Blazor escalabilidade do aplicativo do servidor:

* [Conceitos básicos de Blazor aplicativos de servidor](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>Servidor de implantação

Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem. A memória disponível no servidor afeta o:

* Número de circuitos ativos aos quais um servidor pode dar suporte.
* Latência da interface do usuário no cliente.

Para obter orientação sobre a criação de Blazor aplicativos de servidor seguros e escalonáveis, consulte <xref:blazor/security/server/threat-mitigation> .

Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*. O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente. Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: se você espera que seu aplicativo ofereça suporte a 5.000 usuários simultâneos, considere o orçamento de pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).

### <a name="signalr-configuration"></a>SignalRconfiguração

BlazorOs aplicativos de servidor usam ASP.NET Core SignalR para se comunicar com o navegador. as [ SignalR condições de hospedagem e dimensionamento do](xref:signalr/publish-to-azure-web-app) se aplicam a Blazor Aplicativos de servidor.

Blazorfunciona melhor ao usar o WebSocket como SignalR transporte devido à latência, confiabilidade e [segurança](xref:signalr/security)menores. A sondagem longa é usada pelo SignalR quando o WebSockets não está disponível ou quando o aplicativo é explicitamente configurado para usar sondagem longa. Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço. Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [ SignalR diretrizes de publicação](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Serviço do Azure SignalR

É recomendável usar [o SignalR serviço do Azure](/azure/azure-signalr) para Blazor aplicativos de servidor. O serviço permite escalar verticalmente um Blazor aplicativo de servidor para um grande número de conexões simultâneas SignalR . Além disso, o SignalR alcance global do serviço e os data centers de alto desempenho auxiliam significativamente na redução da latência devido à geografia. Para configurar um aplicativo (e, opcionalmente, provisionar) o serviço do Azure SignalR :

1. Habilite o serviço para dar suporte a *sessões adesivas*, em que os clientes são [redirecionados de volta para o mesmo servidor ao renderizar](xref:blazor/hosting-models#connection-to-the-server). Defina a `ServerStickyMode` opção ou o valor de configuração como `Required` . Normalmente, um aplicativo cria a configuração usando **uma** das seguintes abordagens:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuração (use **uma** das seguintes abordagens):
  
     * *appsettings.jsem*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * As configurações do aplicativo de **configuração**do serviço de aplicativo  >  **Application settings** na portal do Azure (**nome**: `Azure:SignalR:ServerStickyMode` , **valor**: `Required` ).

1. Crie um perfil de publicação de aplicativos do Azure no Visual Studio para o Blazor aplicativo de servidor.
1. Adicione a dependência de ** SignalR serviço do Azure** ao perfil. Se a assinatura do Azure não tiver uma instância de serviço do Azure já existente SignalR para atribuir ao aplicativo, selecione **criar uma nova SignalR instância de serviço do Azure** para provisionar uma nova instância de serviço.
1. Publicar o aplicativo no Azure.

#### <a name="iis"></a>IIS

Ao usar o IIS, habilite:

* [WebSockets no IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessões adesivas com Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Crie uma definição de entrada com as seguintes [anotações de kubernetes para sessões adesivas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

Para SignalR que o WebSockets funcione corretamente, confirme se os `Upgrade` cabeçalhos e o proxy `Connection` estão definidos com os seguintes valores e se estão `$connection_upgrade` mapeados para:

* O valor do cabeçalho de atualização por padrão.
* `close`Quando o cabeçalho de atualização está ausente ou vazio.

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

* [NGINX como um proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux com o Apache

Para hospedar um Blazor aplicativo por trás do Apache no Linux, configure `ProxyPass` para tráfego HTTP e WebSockets.

No exemplo a seguir:

* O servidor Kestrel está em execução no computador host.
* O aplicativo escuta o tráfego na porta 5000.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Habilite os seguintes módulos:

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Verifique se há erros de WebSocket no console do navegador. Erros de exemplo:

* O Firefox não pode estabelecer uma conexão com o servidor em ws://the-domain-name.tld/_blazor?id=XXX.
* Erro: falha ao iniciar o transporte ' WebSockets ': erro: erro no transporte.
* Erro: falha ao iniciar o transporte ' LongPolling ': TypeError: this. Transport está indefinido
* Erro: não é possível se conectar ao servidor com nenhum dos transportes disponíveis. Falha no WebSockets
* Erro: não é possível enviar dados se a conexão não estiver no estado "conectado".

Para obter mais informações, consulte a [documentação do Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Medir latência de rede

A [interoperabilidade js](xref:blazor/call-javascript-from-dotnet) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:

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

Para uma experiência de interface do usuário razoável, recomendamos uma latência de interface do usuário sustentada de 250 MS ou menos.
