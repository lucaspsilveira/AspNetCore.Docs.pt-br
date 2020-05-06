---
title: Provedores de proteção de dados efêmeras no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação do ASP.NET Core provedores de proteção de dados efêmeras.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: 22a332230e15256dc33fd1d06f2da3ea8d34d3bc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776884"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Provedores de proteção de dados efêmeras no ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Há cenários em que um aplicativo precisa de um `IDataProtectionProvider`descartável. Por exemplo, o desenvolvedor pode simplesmente estar experimentando um aplicativo de console único ou o próprio aplicativo é transitório (ele é inserido no script ou em um projeto de teste de unidade). Para dar suporte a esses cenários, o pacote [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) inclui `EphemeralDataProtectionProvider`um tipo. Esse tipo fornece uma implementação básica de `IDataProtectionProvider` cujo repositório de chaves é mantido apenas na memória e não é gravado em nenhum repositório de backup.

Cada instância do `EphemeralDataProtectionProvider` usa sua própria chave mestra exclusiva. Portanto, se uma `IDataProtector` `EphemeralDataProtectionProvider` raiz em um gerar uma carga protegida, essa carga só poderá ser desprotegida por um `IDataProtector` equivalente (dado a mesma cadeia de [finalidade](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) com raiz na mesma `EphemeralDataProtectionProvider` instância.

O exemplo a seguir demonstra como instanciar um `EphemeralDataProtectionProvider` e usá-lo para proteger e desproteger dados.

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
