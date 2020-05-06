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
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="2cf80-103">Limitar o tempo de vida de cargas protegidas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cf80-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="2cf80-104">Há cenários em que o desenvolvedor do aplicativo deseja criar uma carga protegida que expira após um determinado período de tempo.</span><span class="sxs-lookup"><span data-stu-id="2cf80-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="2cf80-105">Por exemplo, a carga protegida pode representar um token de redefinição de senha que só deve ser válido por uma hora.</span><span class="sxs-lookup"><span data-stu-id="2cf80-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="2cf80-106">Certamente, é possível que o desenvolvedor crie seu próprio formato de carga que contenha uma data de expiração incorporada, e desenvolvedores avançados talvez queiram fazer isso mesmo assim, mas para a maioria dos desenvolvedores que gerenciam essas expirações podem aumentar o tedioso.</span><span class="sxs-lookup"><span data-stu-id="2cf80-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="2cf80-107">Para tornar isso mais fácil para o público do desenvolvedor, o pacote [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contém APIs do utilitário para a criação de cargas que expiram automaticamente após um determinado período de tempo.</span><span class="sxs-lookup"><span data-stu-id="2cf80-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="2cf80-108">Essas APIs desligam o `ITimeLimitedDataProtector` tipo.</span><span class="sxs-lookup"><span data-stu-id="2cf80-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="2cf80-109">Uso da API</span><span class="sxs-lookup"><span data-stu-id="2cf80-109">API usage</span></span>

<span data-ttu-id="2cf80-110">A `ITimeLimitedDataProtector` interface é a interface principal para proteger e desproteger cargas de tempo limitado/com expiração demorada.</span><span class="sxs-lookup"><span data-stu-id="2cf80-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="2cf80-111">Para criar uma instância de um `ITimeLimitedDataProtector`, primeiro você precisará de uma instância de um [IDataProtector](xref:security/data-protection/consumer-apis/overview) regular construído com uma finalidade específica.</span><span class="sxs-lookup"><span data-stu-id="2cf80-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="2cf80-112">Depois que `IDataProtector` a instância estiver disponível, chame `IDataProtector.ToTimeLimitedDataProtector` o método de extensão para obter um protetor com recursos internos de expiração.</span><span class="sxs-lookup"><span data-stu-id="2cf80-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="2cf80-113">`ITimeLimitedDataProtector`expõe os seguintes métodos de extensão e superfície de API:</span><span class="sxs-lookup"><span data-stu-id="2cf80-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="2cf80-114">Createprotector (finalidade da cadeia de caracteres): ITimeLimitedDataProtector-essa API é semelhante à `IDataProtectionProvider.CreateProtector` existente no, que pode ser usada para criar [cadeias de finalidade](xref:security/data-protection/consumer-apis/purpose-strings) de um protetor de tempo limitado de raiz.</span><span class="sxs-lookup"><span data-stu-id="2cf80-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="2cf80-115">Proteger (Byte [] texto não criptografado, expiração de DateTimeOffset): byte []</span><span class="sxs-lookup"><span data-stu-id="2cf80-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="2cf80-116">Proteger (Byte [] texto não criptografado, tempo de vida de TimeSpan): byte []</span><span class="sxs-lookup"><span data-stu-id="2cf80-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="2cf80-117">Proteger (texto sem formatação de byte []): byte []</span><span class="sxs-lookup"><span data-stu-id="2cf80-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="2cf80-118">Protect (cadeia de caracteres de texto não criptografado, expiração de DateTimeOffset): cadeia</span><span class="sxs-lookup"><span data-stu-id="2cf80-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="2cf80-119">Protect (cadeia de caracteres de texto sem formatação, tempo de vida de TimeSpan): cadeia</span><span class="sxs-lookup"><span data-stu-id="2cf80-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="2cf80-120">Protect (texto sem formatação de cadeia de caracteres): cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="2cf80-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="2cf80-121">Além dos principais `Protect` métodos que usam apenas o texto sem formatação, há novas sobrecargas que permitem especificar a data de validade da carga.</span><span class="sxs-lookup"><span data-stu-id="2cf80-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="2cf80-122">A data de validade pode ser especificada como uma data absoluta (por `DateTimeOffset`meio de uma) ou como uma hora relativa (da hora atual do sistema `TimeSpan`, por meio de).</span><span class="sxs-lookup"><span data-stu-id="2cf80-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="2cf80-123">Se uma sobrecarga que não faz uma expiração for chamada, a carga será presumida para nunca expirar.</span><span class="sxs-lookup"><span data-stu-id="2cf80-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="2cf80-124">Desproteger (Byte [] protectedData, out DateTimeOffset Expiration): byte []</span><span class="sxs-lookup"><span data-stu-id="2cf80-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="2cf80-125">Desproteger (Byte [] protectedData): byte []</span><span class="sxs-lookup"><span data-stu-id="2cf80-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="2cf80-126">Desproteger (cadeia de caracteres protectedData, expiração de DateTimeOffset): cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="2cf80-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="2cf80-127">Desproteger (cadeia de caracteres protectedData): cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="2cf80-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="2cf80-128">Os `Unprotect` métodos retornam os dados desprotegidos originais.</span><span class="sxs-lookup"><span data-stu-id="2cf80-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="2cf80-129">Se a carga ainda não tiver expirado, a expiração absoluta será retornada como um parâmetro out opcional junto com os dados originais desprotegidos.</span><span class="sxs-lookup"><span data-stu-id="2cf80-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="2cf80-130">Se a carga tiver expirado, todas as sobrecargas do método desproteger gerarão CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="2cf80-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="2cf80-131">Não é recomendável usar essas APIs para proteger as cargas que exigem persistência de longo prazo ou indefinida.</span><span class="sxs-lookup"><span data-stu-id="2cf80-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="2cf80-132">"Posso pagar que as cargas protegidas sejam permanentemente irrecuperáveis após um mês?"</span><span class="sxs-lookup"><span data-stu-id="2cf80-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="2cf80-133">pode servir como uma boa regra geral; se a resposta for não, os desenvolvedores devem considerar APIs alternativas.</span><span class="sxs-lookup"><span data-stu-id="2cf80-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="2cf80-134">O exemplo a seguir usa os [caminhos de código não-di](xref:security/data-protection/configuration/non-di-scenarios) para instanciar o sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="2cf80-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="2cf80-135">Para executar este exemplo, verifique se você adicionou primeiro uma referência ao pacote Microsoft. AspNetCore. dataprotection. Extensions.</span><span class="sxs-lookup"><span data-stu-id="2cf80-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
