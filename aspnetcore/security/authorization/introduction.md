---
title: Introdução à autorização no ASP.NET Core
author: rick-anderson
description: Aprenda as noções básicas de autorização e como funciona a autorização em aplicativos ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: b0d6c91adfb5dc273aeb662cb2c249626ef1d3c6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405426"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Introdução à autorização no ASP.NET Core

<a name="security-authorization-introduction"></a>

A autorização refere-se ao processo que determina o que um usuário pode fazer. Por exemplo, um usuário administrativo tem permissão para criar uma biblioteca de documentos, adicionar documentos, editar documentos e excluí-los. Um usuário não administrativo que trabalha com a biblioteca está autorizado apenas a ler os documentos.

A autorização é ortogonal e independente da autenticação. No entanto, a autorização requer um mecanismo de autenticação. A autenticação é o processo de garantir quem é um usuário. A autenticação pode criar uma ou mais identidades para o usuário atual.

Para obter mais informações sobre autenticação no ASP.NET Core, consulte <xref:security/authentication/index> .

## <a name="authorization-types"></a>Tipos de autorização

ASP.NET Core autorização fornece uma [função](xref:security/authorization/roles) declarativa simples e um modelo avançado [baseado em políticas](xref:security/authorization/policies) . A autorização é expressa em requisitos, e os manipuladores avaliam as declarações de um usuário em relação aos requisitos. As verificações imperativas podem ser baseadas em políticas simples ou políticas que avaliam a identidade do usuário e as propriedades do recurso que o usuário está tentando acessar.

## <a name="namespaces"></a>Namespaces

Os componentes de autorização, incluindo os `AuthorizeAttribute` `AllowAnonymousAttribute` atributos e, são encontrados no `Microsoft.AspNetCore.Authorization` namespace.

Consulte a documentação sobre [autorização simples](xref:security/authorization/simple).
