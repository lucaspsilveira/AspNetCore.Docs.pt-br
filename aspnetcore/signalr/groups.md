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
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="d5ccd-103">Gerenciar usuários e grupos noSignalR</span><span class="sxs-lookup"><span data-stu-id="d5ccd-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="d5ccd-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="d5ccd-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="d5ccd-105">permite que as mensagens sejam enviadas a todas as conexões associadas a um usuário específico, bem como a grupos nomeados de conexões.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="d5ccd-106">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d5ccd-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="d5ccd-107">Usuários noSignalR</span><span class="sxs-lookup"><span data-stu-id="d5ccd-107">Users in SignalR</span></span>

<span data-ttu-id="d5ccd-108">Um único usuário no SignalR pode ter várias conexões com um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="d5ccd-109">Por exemplo, um usuário pode estar conectado à área de trabalho e ao seu telefone.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="d5ccd-110">Cada dispositivo tem uma SignalR conexão separada, mas todos eles estão associados ao mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="d5ccd-111">Se uma mensagem for enviada ao usuário, todas as conexões associadas a esse usuário receberão a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="d5ccd-112">O identificador de usuário para uma conexão pode ser acessado pela `Context.UserIdentifier` propriedade no Hub.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="d5ccd-113">Por padrão, SignalR o usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado à conexão como o identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="d5ccd-114">Para personalizar esse comportamento, consulte [usar declarações para personalizar o tratamento de identidades](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="d5ccd-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="d5ccd-115">Envie uma mensagem para um usuário específico passando o identificador de usuário para a `User` função em um método de Hub, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5ccd-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="d5ccd-116">O identificador de usuário diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="d5ccd-117">Grupos emSignalR</span><span class="sxs-lookup"><span data-stu-id="d5ccd-117">Groups in SignalR</span></span>

<span data-ttu-id="d5ccd-118">Um grupo é uma coleção de conexões associadas a um nome.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="d5ccd-119">As mensagens podem ser enviadas a todas as conexões em um grupo.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="d5ccd-120">Os grupos são a maneira recomendada de enviar para uma conexão ou várias conexões, pois os grupos são gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="d5ccd-121">Uma conexão pode ser membro de vários grupos.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="d5ccd-122">Os grupos são ideais para algo como um aplicativo de chat, onde cada sala pode ser representada como um grupo.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="d5ccd-123">As conexões são adicionadas ou removidas dos grupos por meio dos `AddToGroupAsync` `RemoveFromGroupAsync` métodos e.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="d5ccd-124">A associação de grupo não é preservada quando uma conexão é reconectada.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="d5ccd-125">A conexão precisa reingressar no grupo quando ele for restabelecido.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="d5ccd-126">Não é possível contar os membros de um grupo, pois essas informações não estarão disponíveis se o aplicativo for dimensionado para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="d5ccd-127">Para proteger o acesso aos recursos usando grupos, use a funcionalidade de [autenticação e autorização](xref:signalr/authn-and-authz) no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="d5ccd-128">Se um usuário for adicionado a um grupo somente quando as credenciais forem válidas para esse grupo, as mensagens enviadas para esse grupo irão apenas para usuários autorizados.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="d5ccd-129">No entanto, os grupos não são um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-129">However, groups are not a security feature.</span></span> <span data-ttu-id="d5ccd-130">As declarações de autenticação têm recursos que os grupos não têm, como expiração e revogação.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="d5ccd-131">Se a permissão de um usuário para acessar o grupo for revogada, o aplicativo deverá remover o usuário do grupo explicitamente.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="d5ccd-132">Os nomes de grupo diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d5ccd-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="d5ccd-133">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="d5ccd-133">Related resources</span></span>

* [<span data-ttu-id="d5ccd-134">Introdução</span><span class="sxs-lookup"><span data-stu-id="d5ccd-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d5ccd-135">Hubs</span><span class="sxs-lookup"><span data-stu-id="d5ccd-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d5ccd-136">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="d5ccd-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
