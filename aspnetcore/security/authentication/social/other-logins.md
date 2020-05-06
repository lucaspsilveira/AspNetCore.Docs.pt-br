---
title: Provedores de autenticação OAuth externos
author: rick-anderson
description: Descobrir provedores de autenticação OAuth externos que funcionam com aplicativos ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: c61bbef26017f14df09f8ca6f494d29f79903b6b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776455"
---
# <a name="external-oauth-authentication-providers"></a>Provedores de autenticação OAuth externos

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd)e [Valeriy Novytskyy](https://github.com/01binary)

A lista a seguir inclui provedores de autenticação OAuth externos comuns que funcionam com ASP.NET Core aplicativos. Pacotes NuGet de terceiros, como aqueles mantidos pelo [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), podem ser usados para complementar os provedores de autenticação implementados pela equipe de ASP.NET Core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([instruções](https://developer.linkedin.com/docs/oauth2))

* [Instagram](https://www.instagram.com/developer/register/) ([instruções](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([instruções](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([instruções](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([instruções](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([instruções](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([instruções](https://developers.pinterest.com/docs/api/overview/?))

* [Bolso](https://getpocket.com/developer/apps/new) ([instruções](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([instruções](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([instruções](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([instruções](https://developer.vimeo.com/api/authentication))

* [Soundcloud](https://soundcloud.com/you/apps/new) ([instruções](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([instruções](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
