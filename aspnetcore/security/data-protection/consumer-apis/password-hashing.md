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
# <a name="hash-passwords-in-aspnet-core"></a>Senhas de hash no ASP.NET Core

A base de código de proteção de dados inclui um pacote *Microsoft. AspNetCore. Cryptography. Keyderivation* que contém funções de derivação de chave de criptografia. Este pacote é um componente autônomo e não tem dependências no restante do sistema de proteção de dados. Ele pode ser usado completamente de forma independente. A origem existe junto com a base de código de proteção de dados como uma conveniência.

Atualmente, o pacote oferece um `KeyDerivation.Pbkdf2` método que permite o hash de uma senha usando o [algoritmo PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Essa API é muito semelhante ao [tipo de Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)existente da .NET Framework, mas há três distinções importantes:

1. O `KeyDerivation.Pbkdf2` método dá suporte ao consumo de vários `HMACSHA1`PRFs `HMACSHA256`(atualmente `HMACSHA512`, e), `Rfc2898DeriveBytes` enquanto o tipo `HMACSHA1`só dá suporte a.

2. O `KeyDerivation.Pbkdf2` método detecta o sistema operacional atual e tenta escolher a implementação mais otimizada da rotina, fornecendo um desempenho muito melhor em determinados casos. (No Windows 8, ele oferece cerca de 10 vezes a `Rfc2898DeriveBytes`taxa de transferência de.)

3. O `KeyDerivation.Pbkdf2` método requer que o chamador especifique todos os parâmetros (Salt, PRF e contagem de iteração). O `Rfc2898DeriveBytes` tipo fornece valores padrão para eles.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Consulte o [código-fonte](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) para Identityo `PasswordHasher` tipo de ASP.NET Core para um caso de uso do mundo real.
