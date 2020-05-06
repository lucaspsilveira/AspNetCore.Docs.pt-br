---
title: Introdução às APIs de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba como usar o ASP.NET Core APIs de proteção de dados para proteger e desproteger dados em um aplicativo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: d5e9e61db39a67e8ccb7b345dfa4c97353312857
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774256"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Introdução às APIs de proteção de dados no ASP.NET Core

<a name="security-data-protection-getting-started"></a>

Em sua forma mais simples, proteger os dados consiste nas seguintes etapas:

1. Crie um protetor de dados de um provedor de proteção de dados.

2. Chame o `Protect` método com os dados que você deseja proteger.

3. Chame o `Unprotect` método com os dados que você deseja voltar para o texto sem formatação.

A maioria das estruturas e modelos de aplicativos, como ASP.NET Core SignalRou, já configura o sistema de proteção de dados e o adiciona a um contêiner de serviço que você acessa por meio de injeção de dependência. O exemplo a seguir demonstra a configuração de um contêiner de serviço para injeção de dependência e o registro da pilha de proteção de dados, o recebimento do provedor de proteção de dados por meio de DI, a criação de um protetor e a proteção e desproteção de dados.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Ao criar um protetor, você deve fornecer uma ou mais [cadeias de caracteres de finalidade](xref:security/data-protection/consumer-apis/purpose-strings). Uma cadeia de caracteres de finalidade fornece isolamento entre os consumidores. Por exemplo, um protetor criado com uma cadeia de caracteres de finalidade "verde" não seria capaz de desproteger os dados fornecidos por um protetor com a finalidade de "roxo".

>[!TIP]
> Instâncias do `IDataProtectionProvider` e `IDataProtector` são thread-safe para vários chamadores. É intencional que, uma vez que um componente obtenha uma referência `IDataProtector` a um por meio `CreateProtector`de uma chamada para, ele usará essa referência `Protect` para `Unprotect`várias chamadas para e.
>
>Uma chamada para `Unprotect` emitirá CryptographicException se a carga protegida não puder ser verificada ou decifrada. Alguns componentes podem querer ignorar erros durante operações de desproteção; um componente que lê cookies de autenticação pode lidar com esse erro e tratar a solicitação como se não tivesse nenhum cookie em vez de falhar a solicitação imediatamente. Os componentes que desejam esse comportamento devem capturar especificamente CryptographicException em vez de assimilar todas as exceções.
