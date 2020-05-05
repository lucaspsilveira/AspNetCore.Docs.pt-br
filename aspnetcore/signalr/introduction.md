---
title: Introdução ao ASP.NET CoreSignalR
author: bradygaster
description: Saiba como a biblioteca SignalR de ASP.NET Core simplifica a adição de funcionalidades em tempo real aos aplicativos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 4e968659d23330c4b1403ab998dda9c9e6ebe080
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775772"
---
# <a name="introduction-to-aspnet-core-signalr"></a>Introdução ao ASP.NET CoreSignalR

## <a name="what-is-signalr"></a>O que SignalRé?

ASP.NET Core SignalR é uma biblioteca de software livre que simplifica a adição de funcionalidades da Web em tempo real a aplicativos. A funcionalidade da Web em tempo real permite que o código do lado do servidor envie conteúdo aos clientes instantaneamente.

Bons candidatos para SignalR:

* Aplicativos que exigem atualizações frequentes do servidor. Por exemplo, jogos, redes sociais, votação, leilão, mapas e aplicativos de GPS.
* Painéis e aplicativos de monitoramento. Por exemplo, painéis de empresa, atualizações de vendas instantâneas ou alertas de viagem.
* Aplicativos de colaboração. Aplicativos de quadro de comunicação e software de reunião de equipe são exemplos de aplicativos de colaboração.
* Aplicativos que exigem notificações. Redes sociais, email, chat, jogos, alertas de viagem e muitos outros aplicativos usam notificações.

SignalRfornece uma API para criar [chamadas de procedimento remoto (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)de servidor para cliente. As RPCs chamam funções JavaScript em clientes do código .NET Core do lado do servidor.

Aqui estão alguns recursos do SignalR para ASP.NET Core:

* Lida com o gerenciamento de conexão automaticamente.
* Envia mensagens a todos os clientes conectados simultaneamente. Por exemplo, uma sala de bate-papo.
* Envia mensagens para clientes ou grupos de clientes específicos.
* Dimensiona para lidar com o aumento do tráfego.

A origem é hospedada em um [ SignalR repositório no GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Transportes

SignalRdá suporte às seguintes técnicas para lidar com a comunicação em tempo real (em ordem de fallback normal):

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Eventos enviados pelo servidor
* Sondagem longa

SignalRo escolhe automaticamente o melhor método de transporte que está dentro dos recursos do servidor e do cliente.

## <a name="hubs"></a>Hubs

SignalRusa *hubs* para se comunicar entre clientes e servidores.

Um hub é um pipeline de alto nível que permite que um cliente e um servidor chamem métodos entre si. SignalRmanipula a expedição entre limites de máquina automaticamente, permitindo que os clientes chamem métodos no servidor e vice-versa. Você pode passar parâmetros fortemente tipados para métodos, o que habilita a associação de modelo. SignalRfornece dois protocolos de Hub internos: um protocolo de texto baseado em JSON e um protocolo binário com base em [MessagePack](https://msgpack.org/).  O MessagePack geralmente cria mensagens menores em comparação com o JSON. Os navegadores mais antigos devem dar suporte ao [nível 2 do XHR](https://caniuse.com/#feat=xhr2) para fornecer suporte ao protocolo MessagePack.

Os hubs chamam o código do lado do cliente enviando mensagens que contêm o nome e os parâmetros do método do lado do cliente. Os objetos enviados como parâmetros de método são desserializados usando o protocolo configurado. O cliente tenta corresponder o nome a um método no código do lado do cliente. Quando o cliente encontra uma correspondência, ele chama o método e passa para ele os dados de parâmetro desserializados.

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao SignalR para ASP.NET Core](xref:tutorials/signalr)
* [Plataformas com suporte](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
