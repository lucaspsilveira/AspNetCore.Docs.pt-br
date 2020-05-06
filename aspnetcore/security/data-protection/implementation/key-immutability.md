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
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="904cf-103">Principais configurações de imutabilidade e chave em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="904cf-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="904cf-104">Depois que um objeto é persistido no armazenamento de backup, sua representação é sempre fixa.</span><span class="sxs-lookup"><span data-stu-id="904cf-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="904cf-105">Novos dados podem ser adicionados ao repositório de backup, mas os dados existentes nunca podem ser modificados.</span><span class="sxs-lookup"><span data-stu-id="904cf-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="904cf-106">A principal finalidade desse comportamento é evitar a corrupção de dados.</span><span class="sxs-lookup"><span data-stu-id="904cf-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="904cf-107">Uma consequência desse comportamento é que, uma vez que uma chave é gravada no armazenamento de backup, ela é imutável.</span><span class="sxs-lookup"><span data-stu-id="904cf-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="904cf-108">Suas datas de criação, ativação e expiração nunca podem ser alteradas, embora possam ser revogadas `IKeyManager`usando o.</span><span class="sxs-lookup"><span data-stu-id="904cf-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="904cf-109">Além disso, suas informações de algoritmos subjacentes, material de chaveamento mestre e propriedades de criptografia em repouso também são imutáveis.</span><span class="sxs-lookup"><span data-stu-id="904cf-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="904cf-110">Se o desenvolvedor alterar qualquer configuração que afete a persistência de chave, essas alterações não entrarão em vigor até a próxima vez que uma chave for gerada, seja `IKeyManager.CreateNewKey` por meio de uma chamada explícita para ou por meio do próprio comportamento de [geração de chave automática](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) do sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="904cf-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="904cf-111">As configurações que afetam a persistência de chave são as seguintes:</span><span class="sxs-lookup"><span data-stu-id="904cf-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="904cf-112">O tempo de vida da chave padrão</span><span class="sxs-lookup"><span data-stu-id="904cf-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="904cf-113">O mecanismo de criptografia de chave em repouso</span><span class="sxs-lookup"><span data-stu-id="904cf-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="904cf-114">As informações de algoritmos contidas na chave</span><span class="sxs-lookup"><span data-stu-id="904cf-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="904cf-115">Se você precisar que essas configurações sejam ativadas antes do próximo tempo de reversão automático da chave, considere fazer uma `IKeyManager.CreateNewKey` chamada explícita para para forçar a criação de uma nova chave.</span><span class="sxs-lookup"><span data-stu-id="904cf-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="904cf-116">Lembre-se de fornecer uma data de ativação explícita ({Now + 2 dias} é uma boa regra geral para permitir o tempo para a alteração ser propagada) e a data de expiração na chamada.</span><span class="sxs-lookup"><span data-stu-id="904cf-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="904cf-117">Todos os aplicativos que tocam no repositório devem especificar as mesmas `IDataProtectionBuilder` configurações com os métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="904cf-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="904cf-118">Caso contrário, as propriedades da chave persistente serão dependentes do aplicativo específico que invocou as rotinas de geração de chave.</span><span class="sxs-lookup"><span data-stu-id="904cf-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
