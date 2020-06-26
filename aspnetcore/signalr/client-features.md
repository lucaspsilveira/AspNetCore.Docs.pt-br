---
title: SignalRClientes ASP.NET Core
author: bradygaster
description: Saiba quais recursos têm suporte nos vários clientes ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/client-features
ms.openlocfilehash: 10752e8cace82dc08721af7d38c0250182e9bfb0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408481"
---
# <a name="aspnet-core-signalr-clients"></a>SignalRClientes ASP.NET Core

## <a name="versioning-support-and-compatibility"></a>Controle de versão, suporte e compatibilidade

Os SignalR clientes são fornecidos junto com os componentes do servidor e têm a versão de correspondência. Qualquer cliente com suporte pode se conectar com segurança a qualquer servidor com suporte, e quaisquer problemas de compatibilidade seriam considerados bugs a serem corrigidos. SignalROs clientes têm suporte no mesmo ciclo de vida de suporte que o restante do .NET Core. Consulte [a política de suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) para obter detalhes.

Muitos recursos exigem um cliente **e** servidor compatíveis. Veja abaixo uma tabela mostrando as versões mínimas para vários recursos.

As versões 1. x do SignalR mapa para as versões do .NET Core 2,1 e 2,2 e têm o mesmo tempo de vida. Para a versão 3. x e superior, a SignalR versão corresponde exatamente ao restante do .net e tem o mesmo ciclo de vida de suporte.

| Versão do SignalR | Versão do .NET Core | Nível de suporte | Fim do suporte |
| - | - | - | - |
| 1,0. x | 2.1.x | Suporte a longo prazo | 21 de agosto de 2021 |
| 1.1. x | 2.2. x | Fim da vida útil | 23 de dezembro de 2019 |
| 3. x ou superior | *igual à SignalR versão* | Consulte a [política de suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**Observação:** No ASP.NET Core 3,0, o cliente JavaScript *moveu* para o `@microsoft/signalr` pacote NPM.

## <a name="feature-distribution"></a>Distribuição de recursos

A tabela a seguir mostra os recursos e o suporte para os clientes que oferecem suporte em tempo real. Para cada recurso, a versão *mínima* que dá suporte a esse recurso está listada. Se nenhuma versão estiver listada, o recurso não terá suporte.

| Recurso | Servidor | Cliente .NET | Cliente JavaScript | Cliente Java |
| ---- | :-: | :-: | :-: | :-: |
| Suporte do serviço do Azure SignalR |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming de servidor para cliente](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming de cliente para servidor](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Reconexão automática ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Transporte de WebSockets |2.1.0|1.0.0|1.0.0|1.0.0|
| Transporte de eventos enviados pelo servidor |2.1.0|1.0.0|1.0.0|❌|
| Transporte de sondagem longa |2.1.0|1.0.0|1.0.0|3.0.0|
| Protocolo de Hub JSON |2.1.0|1.0.0|1.0.0|1.0.0|
| Protocolo de Hub do MessagePack |2.1.0|1.0.0|1.0.0|❌|

O suporte à habilitação de recursos de cliente adicionais é acompanhado em [nosso Issue Tracker](https://github.com/dotnet/AspNetCore/issues).

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao SignalR para ASP.NET Core](xref:tutorials/signalr)
* [Plataformas com suporte](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
