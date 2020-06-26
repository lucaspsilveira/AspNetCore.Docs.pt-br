---
title: Gerenciar usuários e grupos noSignalR
author: bradygaster
description: Visão geral de ASP.NET Core SignalR Gerenciamento de usuário e grupo.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af76402d02ee4273deadac246f275c1ae7ad84ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408507"
---
# <a name="manage-users-and-groups-in-signalr"></a>Gerenciar usuários e grupos noSignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

SignalRpermite que as mensagens sejam enviadas a todas as conexões associadas a um usuário específico, bem como a grupos nomeados de conexões.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Usuários noSignalR

Um único usuário no SignalR pode ter várias conexões com um aplicativo. Por exemplo, um usuário pode estar conectado à área de trabalho e ao seu telefone. Cada dispositivo tem uma SignalR conexão separada, mas todos eles estão associados ao mesmo usuário. Se uma mensagem for enviada ao usuário, todas as conexões associadas a esse usuário receberão a mensagem. O identificador de usuário para uma conexão pode ser acessado pela `Context.UserIdentifier` propriedade no Hub.

Por padrão, SignalR o usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado à conexão como o identificador de usuário. Para personalizar esse comportamento, consulte [usar declarações para personalizar o tratamento de identidades](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

Envie uma mensagem para um usuário específico passando o identificador de usuário para a `User` função em um método de Hub, conforme mostrado no exemplo a seguir:

> [!NOTE]
> O identificador de usuário diferencia maiúsculas de minúsculas.

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Grupos emSignalR

Um grupo é uma coleção de conexões associadas a um nome. As mensagens podem ser enviadas a todas as conexões em um grupo. Os grupos são a maneira recomendada de enviar para uma conexão ou várias conexões, pois os grupos são gerenciados pelo aplicativo. Uma conexão pode ser membro de vários grupos. Os grupos são ideais para algo como um aplicativo de chat, onde cada sala pode ser representada como um grupo. As conexões são adicionadas ou removidas dos grupos por meio dos `AddToGroupAsync` `RemoveFromGroupAsync` métodos e.

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

A associação de grupo não é preservada quando uma conexão é reconectada. A conexão precisa reingressar no grupo quando ele for restabelecido. Não é possível contar os membros de um grupo, pois essas informações não estarão disponíveis se o aplicativo for dimensionado para vários servidores.

Para proteger o acesso aos recursos usando grupos, use a funcionalidade de [autenticação e autorização](xref:signalr/authn-and-authz) no ASP.NET Core. Se um usuário for adicionado a um grupo somente quando as credenciais forem válidas para esse grupo, as mensagens enviadas para esse grupo irão apenas para usuários autorizados. No entanto, os grupos não são um recurso de segurança. As declarações de autenticação têm recursos que os grupos não têm, como expiração e revogação. Se a permissão de um usuário para acessar o grupo for revogada, o aplicativo deverá remover o usuário do grupo explicitamente.

> [!NOTE]
> Os nomes de grupo diferenciam maiúsculas de minúsculas.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
