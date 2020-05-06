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
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777287"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR de host em serviços em segundo plano

Por [Brady GASTER](https://twitter.com/bradygaster)

Este artigo fornece diretrizes para:

* Hubs SignalR de hospedagem usando um processo de trabalho em segundo plano hospedado com ASP.NET Core.
* Envio de mensagens para clientes conectados de dentro de um [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)do .NET Core.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Habilitar SignalR na inicialização

::: moniker range=">= aspnetcore-3.0"

Hospedar hubs SignalR de ASP.NET Core no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core. No `Startup.ConfigureServices` método, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para SignalRdar suporte ao. No `Startup.Configure`, o `MapHub` método é chamado no `UseEndpoints` retorno de chamada para conectar os pontos de extremidade do Hub no pipeline de solicitação de ASP.NET Core.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Hospedar hubs SignalR de ASP.NET Core no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core. No `Startup.ConfigureServices` método, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para SignalRdar suporte ao. No `Startup.Configure`, o `UseSignalR` método é chamado para conectar os pontos de extremidade do Hub no pipeline de solicitação ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

No exemplo anterior, a `ClockHub` classe implementa a `Hub<T>` classe para criar um hub fortemente tipado. O `ClockHub` foi configurado na `Startup` classe para responder às solicitações no ponto de extremidade `/hubs/clock`.

Para obter mais informações sobre hubs com rigidez de tipos, consulte [usar hubs no SignalR para ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Essa funcionalidade não está limitada à [classe\<t>do Hub](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Qualquer classe que herda do [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), também funcionará.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

A interface usada pelo fortemente tipado `ClockHub` é a `IClock` interface.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Chamar um SignalR Hub de um serviço em segundo plano

Durante a inicialização, `Worker` a classe, `BackgroundService`a, é habilitada usando `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Como SignalR o também é habilitado durante a `Startup` fase, na qual cada Hub é anexado a um ponto de extremidade individual no pipeline de solicitação HTTP do ASP.NET Core, cada Hub é `IHubContext<T>` representado por um no servidor. Usando os recursos de DI do ASP.NET Core, outras classes instanciadas pela camada de hospedagem `BackgroundService` , como classes, classes do controlador Razor MVC ou modelos de página, podem obter referências a hubs do lado do servidor `IHubContext<ClockHub, IClock>` aceitando instâncias do durante a construção.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Como o `ExecuteAsync` método é chamado iterativamente no serviço em segundo plano, a data e hora atuais do servidor são enviadas para os clientes conectados usando `ClockHub`o.

## <a name="react-to-signalr-events-with-background-services"></a>Reagir a SignalR eventos com serviços em segundo plano

Como um aplicativo de página única usando o cliente JavaScript SignalR para o ou um aplicativo de desktop .net pode fazer uso <xref:signalr/dotnet-client>do, `BackgroundService` a `IHostedService` ou a implementação também pode ser usada para SignalR conectar-se a hubs e responder a eventos.

A `ClockHubClient` classe implementa a `IClock` interface e a `IHostedService` interface. Dessa forma, ele pode ser habilitado `Startup` durante a execução contínua e responder a eventos de Hub do servidor.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante a inicialização, `ClockHubClient` o cria uma instância de `HubConnection` a e habilita `IClock.ShowTime` o método como o manipulador para o evento `ShowTime` do Hub.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Na `IHostedService.StartAsync` implementação, o é `HubConnection` iniciado de forma assíncrona.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante o `IHostedService.StopAsync` método, o `HubConnection` é Descartado de forma assíncrona.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Hubs com rigidez de tipos](xref:signalr/hubs#strongly-typed-hubs)
