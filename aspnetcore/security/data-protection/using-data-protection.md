---
title: Introdução às APIs de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba como usar o ASP.NET Core APIs de proteção de dados para proteger e desproteger dados em um aplicativo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1b0dc6756de55d9ce35eb08ca037e4d4b1fede75
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405608"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="c6ac5-103">Introdução às APIs de proteção de dados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6ac5-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="c6ac5-104">Em sua forma mais simples, proteger os dados consiste nas seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="c6ac5-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="c6ac5-105">Crie um protetor de dados de um provedor de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="c6ac5-106">Chame o `Protect` método com os dados que você deseja proteger.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="c6ac5-107">Chame o `Unprotect` método com os dados que você deseja voltar para o texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="c6ac5-108">A maioria das estruturas e modelos de aplicativos, como ASP.NET Core ou SignalR , já configura o sistema de proteção de dados e o adiciona a um contêiner de serviço que você acessa por meio de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="c6ac5-109">O exemplo a seguir demonstra a configuração de um contêiner de serviço para injeção de dependência e o registro da pilha de proteção de dados, o recebimento do provedor de proteção de dados por meio de DI, a criação de um protetor e a proteção e desproteção de dados.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="c6ac5-110">Ao criar um protetor, você deve fornecer uma ou mais [cadeias de caracteres de finalidade](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="c6ac5-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="c6ac5-111">Uma cadeia de caracteres de finalidade fornece isolamento entre os consumidores.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="c6ac5-112">Por exemplo, um protetor criado com uma cadeia de caracteres de finalidade "verde" não seria capaz de desproteger os dados fornecidos por um protetor com a finalidade de "roxo".</span><span class="sxs-lookup"><span data-stu-id="c6ac5-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="c6ac5-113">Instâncias do `IDataProtectionProvider` e `IDataProtector` são thread-safe para vários chamadores.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="c6ac5-114">É intencional que, uma vez que um componente obtenha uma referência a um `IDataProtector` por meio de uma chamada para `CreateProtector` , ele usará essa referência para várias chamadas para `Protect` e `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="c6ac5-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="c6ac5-115">Uma chamada para `Unprotect` emitirá CryptographicException se a carga protegida não puder ser verificada ou decifrada.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="c6ac5-116">Alguns componentes podem querer ignorar erros durante operações de desproteção; um componente que lê cookies de autenticação pode lidar com esse erro e tratar a solicitação como se não tivesse nenhum cookie em vez de falhar a solicitação imediatamente.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="c6ac5-117">Os componentes que desejam esse comportamento devem capturar especificamente CryptographicException em vez de assimilar todas as exceções.</span><span class="sxs-lookup"><span data-stu-id="c6ac5-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
