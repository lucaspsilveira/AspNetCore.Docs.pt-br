---
title: Diferenças entre SignalR e ASP.NET CoreSignalR
author: bradygaster
description: Diferenças entre SignalR e ASP.NET CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772550"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Diferenças entre o signalr ASP.NET e o sinalizador de ASP.NET Core

ASP.NET Core Signalr não é compatível com clientes ou servidores para o Signalr ASP.NET. Este artigo detalha os recursos que foram removidos ou alterados no ASP.NET Core Signalr.

## <a name="how-to-identify-the-signalr-version"></a>Como identificar a versão do Signalr

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Pacote NuGet do servidor | [Microsoft. AspNet. Signalr](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Nenhum. Incluído na estrutura compartilhada [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| Pacotes NuGet do cliente | [Microsoft. AspNet. Signalr. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. Signalr. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. Signalr. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Pacote de NPM de cliente JavaScript | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Cliente Java | [Repositório do GitHub](https://github.com/SignalR/java-client) (preterido)  | Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Tipo de aplicativo do servidor | ASP.NET (System. Web) ou OWIN Self-host | ASP.NET Core |
| Plataformas de servidor com suporte | .NET Framework 4,5 ou posterior | .NET Core 3,0 ou posterior |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Pacote NuGet do servidor | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Pacotes NuGet do cliente | [Microsoft. AspNet. SignalR. Cliente](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR. Cliente](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Pacote de NPM de cliente JavaScript | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Cliente Java | [Repositório do GitHub](https://github.com/SignalR/java-client) (preterido)  | Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Tipo de aplicativo do servidor | ASP.NET (System. Web) ou OWIN Self-host | ASP.NET Core |
| Plataformas de servidor com suporte | .NET Framework 4,5 ou posterior | .NET Framework 4.6.1 ou posterior<br>.NET Core 2,1 ou posterior |

::: moniker-end

## <a name="feature-differences"></a>Diferenças de recursos

### <a name="automatic-reconnects"></a>Reconexões automáticas

::: moniker range=">= aspnetcore-3.0"

Em ASP.NET SignalR:

* Por padrão, SignalR o tentará se reconectar ao servidor se a conexão for descartada. 

Em ASP.NET Core SignalR:

* As reconexões automáticas são aceitas com o [cliente .net](xref:signalr/dotnet-client#automatically-reconnect) e o [cliente JavaScript](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Antes do ASP.NET Core 3,0, SignalR o não oferece suporte a reconexões automáticas. Se o cliente estiver desconectado, o usuário deverá iniciar explicitamente uma nova conexão para se reconectar. No ASP.NET SignalR, SignalR o tentará se reconectar ao servidor se a conexão for descartada.

::: moniker-end

### <a name="protocol-support"></a>Suporte a protocolo

ASP.NET Core SignalR dá suporte a JSON, bem como a um novo protocolo binário com base em [MessagePack](xref:signalr/messagepackhubprotocol). Além disso, os protocolos personalizados podem ser criados.

### <a name="transports"></a>Transportes

O transporte de quadro contínuo não tem suporte SignalRno ASP.NET Core.

## <a name="differences-on-the-server"></a>Diferenças no servidor

As bibliotecas SignalR do lado do servidor ASP.NET Core estão incluídas em [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), que é usado no modelo de **aplicativo Web** do ASP.NET Core Razor para projetos do e do MVC.

ASP.NET Core SignalR é um middleware ASP.NET Core. Ele deve ser configurado chamando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> em. `Startup.ConfigureServices`

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Para configurar o roteamento, mapeie as rotas para os <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> hubs dentro da chamada `Startup.Configure` do método no método.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Para configurar o roteamento, mapeie as rotas para os <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> hubs dentro da chamada `Startup.Configure` do método no método.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Sessões adesivas

O modelo de dimensionamento para SignalR ASP.net permite que os clientes se reconectem e enviem mensagens para qualquer servidor no farm. No ASP.NET Core SignalR, o cliente deve interagir com o mesmo servidor durante a conexão. Para expansão usando Redis, isso significa que as sessões adesivas são necessárias. Para o scale out usando o [serviço do Azure SignalR ](/azure/azure-signalr/), as sessões adesivas não são necessárias porque o serviço lida com conexões com os clientes.

### <a name="single-hub-per-connection"></a>Hub único por conexão

No ASP.NET Core SignalR, o modelo de conexão foi simplificado. As conexões são feitas diretamente em um único Hub, em vez de uma única conexão sendo usada para compartilhar o acesso a vários hubs.

### <a name="streaming"></a>Streaming

O SignalR ASP.NET Core agora dá suporte ao [streaming de dados](xref:signalr/streaming) do hub para o cliente.

### <a name="state"></a>Estado

A capacidade de passar um estado arbitrário entre clientes e o Hub (geralmente `HubState`chamado) foi removida, bem como suporte para mensagens de progresso. Não há nenhum equivalente de proxies de Hub no momento.

### <a name="persistentconnection-removal"></a>Remoção de PersistentConnection

No ASP.NET Core SignalR, a classe [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) foi removida.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core tem injeção de dependência (DI) incorporada à estrutura. Os serviços podem usar DI para acessar o [HubContext](xref:signalr/hubcontext). O `GlobalHost` objeto usado em ASP.NET SignalR para obter um `HubContext` não existe em ASP.NET Core. SignalR

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR não tem suporte para `HubPipeline` módulos.

## <a name="differences-on-the-client"></a>Diferenças no cliente

### <a name="typescript"></a>TypeScript

O cliente SignalR ASP.NET Core é gravado em [TypeScript](https://www.typescriptlang.org/). Você pode escrever em JavaScript ou TypeScript ao usar o [cliente JavaScript](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npm"></a>O cliente JavaScript está hospedado em NPM

::: moniker range=">= aspnetcore-3.0"

Em versões do ASP.NET, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio. Nas versões do ASP.NET Core, o [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) pacote NPM contém as bibliotecas JavaScript. Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** . Use o NPM para obter e instalar `@microsoft/signalr` o pacote NPM.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Em versões do ASP.NET, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio. Nas versões do ASP.NET Core, o [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) pacote NPM contém as bibliotecas JavaScript. Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** . Use o NPM para obter e instalar `@aspnet/signalr` o pacote NPM.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

A dependência do jQuery foi removida, no entanto, os projetos ainda podem usar o jQuery.

### <a name="internet-explorer-support"></a>Suporte do Internet Explorer

ASP.NET Core SignalR requer o Microsoft Internet Explorer 11 ou posterior ( SignalR ASP.NET com suporte Microsoft Internet Explorer 8 e posterior).

### <a name="javascript-client-method-syntax"></a>Sintaxe do método de cliente JavaScript

::: moniker range=">= aspnetcore-3.0"

A sintaxe JavaScript foi alterada da versão ASP.NET do SignalR. Em vez de usar `$connection` o objeto, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Use o método [on](/javascript/api/@microsoft/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

A sintaxe JavaScript foi alterada da versão ASP.NET do SignalR. Em vez de usar `$connection` o objeto, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Use o método [on](/javascript/api/@aspnet/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Depois de criar o método de cliente, inicie a conexão de Hub. Encadeamento um método [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar ou tratar erros.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Proxies de Hub

::: moniker range=">= aspnetcore-3.0"

Os proxies do Hub não são mais gerados automaticamente. Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) como uma cadeia de caracteres.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Os proxies do Hub não são mais gerados automaticamente. Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) como uma cadeia de caracteres.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET e outros clientes

O [Microsoft. AspNetCore.SignalR. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)O pacote NuGet do cliente contém as bibliotecas de cliente SignalR.net para ASP.NET Core.

Use o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> para criar e criar uma instância de uma conexão com um Hub.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Diferenças de dimensionamento

ASP.NET SignalR dá suporte a SQL Server e Redis. ASP.NET Core SignalR dá suporte SignalR ao serviço do Azure e Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalRexpansão com o barramento de serviço do Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalRscale out com Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalRdimensionamento com SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Serviço SignalR do Azure](/azure/azure-signalr/)
* [Redis backplane](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Plataformas com Suporte](xref:signalr/supported-platforms)
