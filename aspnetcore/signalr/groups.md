---
title: Gerenciar usuários e grupos noSignalR
author: bradygaster
description: Visão geral de SignalR ASP.NET Core gerenciamento de usuário e grupo.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776292"
---
# <a name="manage-users-and-groups-in-signalr"></a>Gerenciar usuários e grupos noSignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

SignalRpermite que as mensagens sejam enviadas a todas as conexões associadas a um usuário específico, bem como a grupos nomeados de conexões.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Usuários noSignalR

SignalRpermite que você envie mensagens para todas as conexões associadas a um usuário específico. Por padrão, SignalR o usa `ClaimTypes.NameIdentifier` o do `ClaimsPrincipal` associado à conexão como o identificador de usuário. Um único usuário pode ter várias conexões com um SignalR aplicativo. Por exemplo, um usuário pode estar conectado à área de trabalho e ao seu telefone. Cada dispositivo tem uma conexão SignalR separada, mas todos eles estão associados ao mesmo usuário. Se uma mensagem for enviada ao usuário, todas as conexões associadas a esse usuário receberão a mensagem. O identificador de usuário para uma conexão pode ser acessado pela `Context.UserIdentifier` Propriedade em seu hub.

Envie uma mensagem para um usuário específico passando o identificador de usuário para a `User` função em seu método de Hub, conforme mostrado no exemplo a seguir:

> [!NOTE]
> O identificador de usuário diferencia maiúsculas de minúsculas.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Grupos emSignalR

Um grupo é uma coleção de conexões associadas a um nome. As mensagens podem ser enviadas a todas as conexões em um grupo. Os grupos são a maneira recomendada de enviar para uma conexão ou várias conexões, pois os grupos são gerenciados pelo aplicativo. Uma conexão pode ser membro de vários grupos. Isso torna os grupos ideais para algo como um aplicativo de chat, onde cada sala pode ser representada como um grupo. As conexões podem ser adicionadas ou removidas de `AddToGroupAsync` grupos `RemoveFromGroupAsync` por meio dos métodos e.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

A associação de grupo não é preservada quando uma conexão é reconectada. A conexão precisa reingressar no grupo quando ele for restabelecido. Não é possível contar os membros de um grupo, pois essas informações não estarão disponíveis se o aplicativo for dimensionado para vários servidores.

Para proteger o acesso aos recursos usando grupos, use a funcionalidade de [autenticação e autorização](xref:signalr/authn-and-authz) no ASP.NET Core. Se você apenas adicionar usuários a um grupo quando as credenciais forem válidas para esse grupo, as mensagens enviadas para esse grupo irão apenas para usuários autorizados. No entanto, os grupos não são um recurso de segurança. As declarações de autenticação têm recursos que os grupos não têm, como expiração e revogação. Se a permissão de um usuário para acessar o grupo for revogada, você precisará detectá-lo manualmente e removê-lo do grupo.

> [!NOTE]
> Os nomes de grupo diferenciam maiúsculas de minúsculas.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
