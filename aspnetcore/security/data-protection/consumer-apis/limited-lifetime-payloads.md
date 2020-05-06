---
title: Limitar o tempo de vida de cargas protegidas no ASP.NET Core
author: rick-anderson
description: Saiba como limitar o tempo de vida de uma carga protegida usando as APIs de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: bc1597f75d8c5f786d46e59ac027d01ffca077c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768605"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Limitar o tempo de vida de cargas protegidas no ASP.NET Core

Há cenários em que o desenvolvedor do aplicativo deseja criar uma carga protegida que expira após um determinado período de tempo. Por exemplo, a carga protegida pode representar um token de redefinição de senha que só deve ser válido por uma hora. Certamente, é possível que o desenvolvedor crie seu próprio formato de carga que contenha uma data de expiração incorporada, e desenvolvedores avançados talvez queiram fazer isso mesmo assim, mas para a maioria dos desenvolvedores que gerenciam essas expirações podem aumentar o tedioso.

Para tornar isso mais fácil para o público do desenvolvedor, o pacote [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contém APIs do utilitário para a criação de cargas que expiram automaticamente após um determinado período de tempo. Essas APIs desligam o `ITimeLimitedDataProtector` tipo.

## <a name="api-usage"></a>Uso da API

A `ITimeLimitedDataProtector` interface é a interface principal para proteger e desproteger cargas de tempo limitado/com expiração demorada. Para criar uma instância de um `ITimeLimitedDataProtector`, primeiro você precisará de uma instância de um [IDataProtector](xref:security/data-protection/consumer-apis/overview) regular construído com uma finalidade específica. Depois que `IDataProtector` a instância estiver disponível, chame `IDataProtector.ToTimeLimitedDataProtector` o método de extensão para obter um protetor com recursos internos de expiração.

`ITimeLimitedDataProtector`expõe os seguintes métodos de extensão e superfície de API:

* Createprotector (finalidade da cadeia de caracteres): ITimeLimitedDataProtector-essa API é semelhante à `IDataProtectionProvider.CreateProtector` existente no, que pode ser usada para criar [cadeias de finalidade](xref:security/data-protection/consumer-apis/purpose-strings) de um protetor de tempo limitado de raiz.

* Proteger (Byte [] texto não criptografado, expiração de DateTimeOffset): byte []

* Proteger (Byte [] texto não criptografado, tempo de vida de TimeSpan): byte []

* Proteger (texto sem formatação de byte []): byte []

* Protect (cadeia de caracteres de texto não criptografado, expiração de DateTimeOffset): cadeia

* Protect (cadeia de caracteres de texto sem formatação, tempo de vida de TimeSpan): cadeia

* Protect (texto sem formatação de cadeia de caracteres): cadeia de caracteres

Além dos principais `Protect` métodos que usam apenas o texto sem formatação, há novas sobrecargas que permitem especificar a data de validade da carga. A data de validade pode ser especificada como uma data absoluta (por `DateTimeOffset`meio de uma) ou como uma hora relativa (da hora atual do sistema `TimeSpan`, por meio de). Se uma sobrecarga que não faz uma expiração for chamada, a carga será presumida para nunca expirar.

* Desproteger (Byte [] protectedData, out DateTimeOffset Expiration): byte []

* Desproteger (Byte [] protectedData): byte []

* Desproteger (cadeia de caracteres protectedData, expiração de DateTimeOffset): cadeia de caracteres

* Desproteger (cadeia de caracteres protectedData): cadeia de caracteres

Os `Unprotect` métodos retornam os dados desprotegidos originais. Se a carga ainda não tiver expirado, a expiração absoluta será retornada como um parâmetro out opcional junto com os dados originais desprotegidos. Se a carga tiver expirado, todas as sobrecargas do método desproteger gerarão CryptographicException.

>[!WARNING]
> Não é recomendável usar essas APIs para proteger as cargas que exigem persistência de longo prazo ou indefinida. "Posso pagar que as cargas protegidas sejam permanentemente irrecuperáveis após um mês?" pode servir como uma boa regra geral; se a resposta for não, os desenvolvedores devem considerar APIs alternativas.

O exemplo a seguir usa os [caminhos de código não-di](xref:security/data-protection/configuration/non-di-scenarios) para instanciar o sistema de proteção de dados. Para executar este exemplo, verifique se você adicionou primeiro uma referência ao pacote Microsoft. AspNetCore. dataprotection. Extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
