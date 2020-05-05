---
title: Senhas de hash no ASP.NET Core
author: rick-anderson
description: Saiba como usar hash de senhas usando o ASP.NET Core APIs de proteção de dados.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 6a5e0e4378241671905f2a759aad88372901e7d2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769774"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="a068f-103">Senhas de hash no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a068f-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="a068f-104">A base de código de proteção de dados inclui um pacote *Microsoft. AspNetCore. Cryptography. Keyderivation* que contém funções de derivação de chave de criptografia.</span><span class="sxs-lookup"><span data-stu-id="a068f-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="a068f-105">Este pacote é um componente autônomo e não tem dependências no restante do sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="a068f-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="a068f-106">Ele pode ser usado completamente de forma independente.</span><span class="sxs-lookup"><span data-stu-id="a068f-106">It can be used completely independently.</span></span> <span data-ttu-id="a068f-107">A origem existe junto com a base de código de proteção de dados como uma conveniência.</span><span class="sxs-lookup"><span data-stu-id="a068f-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="a068f-108">Atualmente, o pacote oferece um `KeyDerivation.Pbkdf2` método que permite o hash de uma senha usando o [algoritmo PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="a068f-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="a068f-109">Essa API é muito semelhante ao [tipo de Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)existente da .NET Framework, mas há três distinções importantes:</span><span class="sxs-lookup"><span data-stu-id="a068f-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="a068f-110">O `KeyDerivation.Pbkdf2` método dá suporte ao consumo de vários `HMACSHA1`PRFs `HMACSHA256`(atualmente `HMACSHA512`, e), `Rfc2898DeriveBytes` enquanto o tipo `HMACSHA1`só dá suporte a.</span><span class="sxs-lookup"><span data-stu-id="a068f-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="a068f-111">O `KeyDerivation.Pbkdf2` método detecta o sistema operacional atual e tenta escolher a implementação mais otimizada da rotina, fornecendo um desempenho muito melhor em determinados casos.</span><span class="sxs-lookup"><span data-stu-id="a068f-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="a068f-112">(No Windows 8, ele oferece cerca de 10 vezes a `Rfc2898DeriveBytes`taxa de transferência de.)</span><span class="sxs-lookup"><span data-stu-id="a068f-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="a068f-113">O `KeyDerivation.Pbkdf2` método requer que o chamador especifique todos os parâmetros (Salt, PRF e contagem de iteração).</span><span class="sxs-lookup"><span data-stu-id="a068f-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="a068f-114">O `Rfc2898DeriveBytes` tipo fornece valores padrão para eles.</span><span class="sxs-lookup"><span data-stu-id="a068f-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="a068f-115">Consulte o [código-fonte](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) para Identityo `PasswordHasher` tipo de ASP.NET Core para um caso de uso do mundo real.</span><span class="sxs-lookup"><span data-stu-id="a068f-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
