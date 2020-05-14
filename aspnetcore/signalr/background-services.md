---
title: ASP.NET Core SignalR de host em serviços em segundo plano
author: bradygaster
description: Saiba como enviar mensagens para SignalR clientes de classes BackgroundService do .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 7bc3b9535055e3fccf23ffa4638bd29676910348
ms.sourcegitcommit: e87dfa08fec0be1008249b1be678e5f79dcc5acb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382563"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR de host em serviços em segundo plano

Por [Brady GASTER](https://twitter.com/bradygaster)

Este artigo fornece diretrizes para:

* SignalRHubs de hospedagem usando um processo de trabalho em segundo plano hospedado com ASP.NET Core.
* Envio de mensagens para clientes conectados de dentro de um [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)do .NET Core.

::: moniker range=">= aspnetcore-3.0"

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(como baixar)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(como baixar)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>Habilitar SignalR na inicialização

::: moniker range=">= aspnetcore-3.0"

Hospedar SignalR hubs de ASP.NET Core no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo web ASP.NET Core. No `Startup.ConfigureServices` método, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para dar suporte ao SignalR . No `Startup.Configure` , o `MapHub` método é chamado no `UseEndpoints` retorno de chamada para conectar os pontos de extremidade do Hub no pipeline de solicitação de ASP.NET Core.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

Hospedar SignalR hubs de ASP.NET Core no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo web ASP.NET Core. No `Startup.ConfigureServices` método, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para dar suporte ao SignalR . No `Startup.Configure` , o `UseSignalR` método é chamado para conectar os pontos de extremidade do Hub no pipeline de solicitação ASP.NET Core.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

No exemplo anterior, a `ClockHub` classe implementa a `Hub<T>` classe para criar um hub fortemente tipado. O `ClockHub` foi configurado na `Startup` classe para responder às solicitações no ponto de extremidade `/hubs/clock` .

Para obter mais informações sobre hubs com rigidez de tipos, consulte [usar hubs no SignalR para ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Essa funcionalidade não está limitada à [classe \< t>do Hub](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Qualquer classe que herda do [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funciona.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

A interface usada pelo fortemente tipado `ClockHub` é a `IClock` interface.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>Chamar um SignalR Hub de um serviço em segundo plano

Durante a inicialização, a `Worker` classe, a `BackgroundService` , é habilitada usando `AddHostedService` .

```csharp
services.AddHostedService<Worker>();
```

Como SignalR o também é habilitado durante a `Startup` fase, na qual cada Hub é anexado a um ponto de extremidade individual no pipeline de solicitação HTTP do ASP.NET Core, cada Hub é representado por um `IHubContext<T>` no servidor. Usando os recursos de DI do ASP.NET Core, outras classes instanciadas pela camada de hospedagem, como `BackgroundService` classes, classes do controlador MVC ou Razor modelos de página, podem obter referências a hubs do lado do servidor aceitando instâncias do `IHubContext<ClockHub, IClock>` durante a construção.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

Como o `ExecuteAsync` método é chamado iterativamente no serviço em segundo plano, a data e hora atuais do servidor são enviadas para os clientes conectados usando o `ClockHub` .

## <a name="react-to-signalr-events-with-background-services"></a>Reagir a SignalR eventos com serviços em segundo plano

Como um aplicativo de página única usando o cliente JavaScript para o SignalR ou um aplicativo de desktop .net pode fazer uso do <xref:signalr/dotnet-client> , a ou a `BackgroundService` `IHostedService` implementação também pode ser usada para conectar-se a SignalR hubs e responder a eventos.

A `ClockHubClient` classe implementa a `IClock` interface e a `IHostedService` interface. Dessa forma, ele pode ser habilitado durante `Startup` a execução contínua e responder a eventos de Hub do servidor.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante a inicialização, o `ClockHubClient` cria uma instância de a `HubConnection` e habilita o `IClock.ShowTime` método como o manipulador para o evento do Hub `ShowTime` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Na `IHostedService.StartAsync` implementação, o `HubConnection` é iniciado de forma assíncrona.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante o `IHostedService.StopAsync` método, o `HubConnection` é Descartado de forma assíncrona.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Na `IHostedService.StartAsync` implementação, o `HubConnection` é iniciado de forma assíncrona.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante o `IHostedService.StopAsync` método, o `HubConnection` é Descartado de forma assíncrona.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Hubs com rigidez de tipos](xref:signalr/hubs#strongly-typed-hubs)
