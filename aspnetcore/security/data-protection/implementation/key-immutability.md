---
title: Principais configurações de imutabilidade e chave em ASP.NET Core
author: rick-anderson
description: Conheça os detalhes de implementação das APIs de imutabilidade da chave de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 5bbd1fb28fc0330ccfe4e829ab0b2f86226c7166
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776910"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Principais configurações de imutabilidade e chave em ASP.NET Core

Depois que um objeto é persistido no armazenamento de backup, sua representação é sempre fixa. Novos dados podem ser adicionados ao repositório de backup, mas os dados existentes nunca podem ser modificados. A principal finalidade desse comportamento é evitar a corrupção de dados.

Uma consequência desse comportamento é que, uma vez que uma chave é gravada no armazenamento de backup, ela é imutável. Suas datas de criação, ativação e expiração nunca podem ser alteradas, embora possam ser revogadas `IKeyManager`usando o. Além disso, suas informações de algoritmos subjacentes, material de chaveamento mestre e propriedades de criptografia em repouso também são imutáveis.

Se o desenvolvedor alterar qualquer configuração que afete a persistência de chave, essas alterações não entrarão em vigor até a próxima vez que uma chave for gerada, seja `IKeyManager.CreateNewKey` por meio de uma chamada explícita para ou por meio do próprio comportamento de [geração de chave automática](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) do sistema de proteção de dados. As configurações que afetam a persistência de chave são as seguintes:

* [O tempo de vida da chave padrão](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [O mecanismo de criptografia de chave em repouso](xref:security/data-protection/implementation/key-encryption-at-rest)

* [As informações de algoritmos contidas na chave](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Se você precisar que essas configurações sejam ativadas antes do próximo tempo de reversão automático da chave, considere fazer uma `IKeyManager.CreateNewKey` chamada explícita para para forçar a criação de uma nova chave. Lembre-se de fornecer uma data de ativação explícita ({Now + 2 dias} é uma boa regra geral para permitir o tempo para a alteração ser propagada) e a data de expiração na chamada.

>[!TIP]
> Todos os aplicativos que tocam no repositório devem especificar as mesmas `IDataProtectionBuilder` configurações com os métodos de extensão. Caso contrário, as propriedades da chave persistente serão dependentes do aplicativo específico que invocou as rotinas de geração de chave.
