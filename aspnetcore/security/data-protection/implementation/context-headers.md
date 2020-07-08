---
title: Cabeçalhos de contexto em ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação de cabeçalhos de contexto de proteção de dados ASP.NET Core.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 0995cd80c10f638c90a60630378518988ffb89ed
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060092"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="2fe1b-103">Cabeçalhos de contexto em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fe1b-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="2fe1b-104">Plano de fundo e teoria</span><span class="sxs-lookup"><span data-stu-id="2fe1b-104">Background and theory</span></span>

<span data-ttu-id="2fe1b-105">No sistema de proteção de dados, uma "chave" significa um objeto que pode fornecer serviços de criptografia autenticados.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="2fe1b-106">Cada chave é identificada por uma ID exclusiva (um GUID) e contém informações de algoritmos de ti e material de Entropic.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="2fe1b-107">É pretendido que cada chave tenha uma entropia exclusiva, mas o sistema não possa impor isso e também precisamos considerar os desenvolvedores que podem alterar o anel de chave manualmente modificando as informações de algoritmo de uma chave existente no anel de chave.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="2fe1b-108">Para atingir nossos requisitos de segurança, considerando esses casos, o sistema de proteção de dados tem um conceito de [agilidade de criptografia](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), que permite usar com segurança um único valor de Entropic em vários algoritmos criptográficos.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="2fe1b-109">A maioria dos sistemas que dão suporte à agilidade de criptografia faz isso incluindo algumas informações de identificação sobre o algoritmo dentro da carga.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="2fe1b-110">O OID do algoritmo geralmente é um bom candidato para isso.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="2fe1b-111">No entanto, um problema que encontramos é que há várias maneiras de especificar o mesmo algoritmo: "AES" (CNG) e as classes AES gerenciada, AesManaged, AesCryptoServiceProvider, AesCng e RijndaelManaged (dadas parâmetros específicos) são todas na verdade, e precisamos manter um mapeamento de todos eles para o OID correto.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="2fe1b-112">Se um desenvolvedor quisesse fornecer um algoritmo personalizado (ou até mesmo outra implementação do AES!), ele teria que nos dizer seu OID.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="2fe1b-113">Essa etapa de registro extra torna a configuração do sistema particularmente difícil.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="2fe1b-114">Voltando, decidimos que estamos se aproximando do problema da direção errada.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="2fe1b-115">Um OID informa o que é o algoritmo, mas não nos preocupamos com isso.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="2fe1b-116">Se precisarmos usar um único valor Entropic com segurança em dois algoritmos diferentes, não é necessário que possamos saber quais são os algoritmos realmente.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="2fe1b-117">O que realmente nos preocupamos é como eles se comportam.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="2fe1b-118">Qualquer algoritmo de codificação de bloco simétrico claro é também uma pseudoaleatória (PRP) de alta segurança: corrija as entradas (chave, modo de encadeamento, IV, texto não criptografado) e a saída de texto cifrado com probabilidade impressionante será distinta de qualquer outro algoritmo de codificação de bloco simétrico, dadas as mesmas entradas.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="2fe1b-119">Da mesma forma, qualquer função de hash com chave de certo é também uma função de pseudoaleatória forte (PRF) e, dada uma entrada fixa definida, sua saída será intensamente distinta de qualquer outra função de hash com chave.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="2fe1b-120">Usamos esse conceito de PRPs e PRFs fortes para criar um cabeçalho de contexto.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="2fe1b-121">Esse cabeçalho de contexto essencialmente atua como uma impressão digital estável nos algoritmos em uso para qualquer operação específica e fornece a agilidade criptográfica necessária para o sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="2fe1b-122">Esse cabeçalho é reproduzível e usado posteriormente como parte do processo de [derivação da subchave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="2fe1b-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="2fe1b-123">Há duas maneiras diferentes de criar o cabeçalho de contexto dependendo dos modos de operação dos algoritmos subjacentes.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="2fe1b-124">Criptografia de modo CBC + autenticação HMAC</span><span class="sxs-lookup"><span data-stu-id="2fe1b-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="2fe1b-125">O cabeçalho de contexto consiste nos seguintes componentes:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="2fe1b-126">[16 bits] O valor 00 00, que é um marcador que significa "criptografia de CBC + autenticação HMAC".</span><span class="sxs-lookup"><span data-stu-id="2fe1b-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="2fe1b-127">[32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de codificação de bloco simétrico.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="2fe1b-128">[32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de codificação de bloco simétrico.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="2fe1b-129">[32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="2fe1b-130">(Atualmente, o tamanho da chave sempre corresponde ao tamanho do resumo.)</span><span class="sxs-lookup"><span data-stu-id="2fe1b-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="2fe1b-131">[32 bits] O tamanho de resumo (em bytes, big-endian) do algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="2fe1b-132">`EncCBC(K_E, IV, "")`, que é a saída do algoritmo de codificação de bloco simétrico, dada uma entrada de cadeia de caracteres vazia e em que IV é um vetor de todos os zero.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="2fe1b-133">A construção do `K_E` é descrita abaixo.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="2fe1b-134">`MAC(K_H, "")`, que é a saída do algoritmo HMAC, considerando uma entrada de cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="2fe1b-135">A construção do `K_H` é descrita abaixo.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="2fe1b-136">O ideal é que possamos passar todos os vetores de zero para `K_E` e `K_H` .</span><span class="sxs-lookup"><span data-stu-id="2fe1b-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="2fe1b-137">No entanto, desejamos evitar a situação em que o algoritmo subjacente verifica a existência de chaves fracas antes de executar qualquer operação (notavelmente DES e 3DES), que impede o uso de um padrão simples ou repetível, como um vetor de todos os zeros.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="2fe1b-138">Em vez disso, usamos o NIST SP800-108 KDF no modo de contador (consulte [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) com uma chave de comprimento zero, rótulo e contexto e HMACSHA512 como o PRF subjacente.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="2fe1b-139">Podemos derivar `| K_E | + | K_H |` bytes de saída e, em seguida, decompor o resultado `K_E` entre `K_H` si.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="2fe1b-140">Matematicamente, isso é representado da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="2fe1b-141">Exemplo: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="2fe1b-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="2fe1b-142">Como exemplo, considere o caso em que o algoritmo de codificação de bloco simétrico é AES-192-CBC e o algoritmo de validação é HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="2fe1b-143">O sistema geraria o cabeçalho de contexto usando as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="2fe1b-144">Primeiro, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , em que `| K_E | = 192 bits` e `| K_H | = 256 bits` por os algoritmos especificados.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="2fe1b-145">Isso leva a `K_E = 5BB6..21DD` e, `K_H = A04A..00A9` no exemplo abaixo:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="2fe1b-146">Em seguida, COMPUTE `Enc_CBC (K_E, IV, "")` para AES-192-CBC fornecido `IV = 0*` e `K_E` como acima.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="2fe1b-147">Em seguida, COMPUTE `MAC(K_H, "")` para HMACSHA256 fornecido `K_H` como acima.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="2fe1b-148">Isso produz o cabeçalho de contexto completo abaixo:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="2fe1b-149">Esse cabeçalho de contexto é a impressão digital do par de algoritmos de criptografia autenticado (AES-192-CBC criptografia + validação de HMACSHA256).</span><span class="sxs-lookup"><span data-stu-id="2fe1b-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="2fe1b-150">Os componentes, conforme descrito [acima](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , são:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="2fe1b-151">o marcador`(00 00)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="2fe1b-152">o comprimento da chave de codificação do bloco`(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="2fe1b-153">o tamanho do bloco de codificação de bloco`(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="2fe1b-154">o comprimento da chave HMAC`(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="2fe1b-155">o tamanho do HMAC Digest`(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="2fe1b-156">a saída bloquear PRP de codificação `(F4 74 - DB 6F)` e</span><span class="sxs-lookup"><span data-stu-id="2fe1b-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="2fe1b-157">a saída de HMAC PRF `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="2fe1b-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="2fe1b-158">O cabeçalho de contexto de autenticação de criptografia do modo CBC + HMAC é criado da mesma maneira, independentemente de as implementações de algoritmos serem fornecidas pelo Windows CNG ou por tipos SymmetricAlgorithm e KeyedHashAlgorithm gerenciados.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="2fe1b-159">Isso permite que os aplicativos executados em diferentes sistemas operacionais produzam o mesmo cabeçalho de contexto de forma confiável, mesmo que as implementações dos algoritmos sejam diferentes entre os sistemas operacionais.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="2fe1b-160">(Na prática, o KeyedHashAlgorithm não precisa ser um HMAC adequado.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="2fe1b-161">Pode ser qualquer tipo de algoritmo de hash com chave.)</span><span class="sxs-lookup"><span data-stu-id="2fe1b-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="2fe1b-162">Exemplo: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="2fe1b-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="2fe1b-163">Primeiro, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , em que `| K_E | = 192 bits` e `| K_H | = 160 bits` por os algoritmos especificados.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="2fe1b-164">Isso leva a `K_E = A219..E2BB` e, `K_H = DC4A..B464` no exemplo abaixo:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="2fe1b-165">Em seguida, COMPUTE `Enc_CBC (K_E, IV, "")` para 3DES-192-CBC fornecido `IV = 0*` e `K_E` como acima.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="2fe1b-166">Em seguida, COMPUTE `MAC(K_H, "")` para HMACSHA1 fornecido `K_H` como acima.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="2fe1b-167">Isso produz o cabeçalho de contexto completo, que é uma impressão digital do par de algoritmos de criptografia autenticado (3DES-192-CBC criptografia + validação de HMACSHA1), mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="2fe1b-168">Os componentes são divididos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-168">The components break down as follows:</span></span>

* <span data-ttu-id="2fe1b-169">o marcador`(00 00)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="2fe1b-170">o comprimento da chave de codificação do bloco`(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="2fe1b-171">o tamanho do bloco de codificação de bloco`(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="2fe1b-172">o comprimento da chave HMAC`(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="2fe1b-173">o tamanho do HMAC Digest`(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="2fe1b-174">a saída bloquear PRP de codificação `(AB B1 - E1 0E)` e</span><span class="sxs-lookup"><span data-stu-id="2fe1b-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="2fe1b-175">a saída de HMAC PRF `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="2fe1b-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="2fe1b-176">Criptografia do modo de Galois/contador + autenticação</span><span class="sxs-lookup"><span data-stu-id="2fe1b-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="2fe1b-177">O cabeçalho de contexto consiste nos seguintes componentes:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="2fe1b-178">[16 bits] O valor 00 01, que é um marcador que significa "criptografia GCM + autenticação".</span><span class="sxs-lookup"><span data-stu-id="2fe1b-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="2fe1b-179">[32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de codificação de bloco simétrico.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="2fe1b-180">[32 bits] O tamanho de nonce (em bytes, big-endian) usado durante operações de criptografia autenticadas.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="2fe1b-181">(Para nosso sistema, isso é corrigido em um tamanho nonce = 96 bits.)</span><span class="sxs-lookup"><span data-stu-id="2fe1b-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="2fe1b-182">[32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de codificação de bloco simétrico.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="2fe1b-183">(Para GCM, isso é corrigido no tamanho do bloco = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="2fe1b-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="2fe1b-184">[32 bits] O tamanho da marca de autenticação (em bytes, big-endian) produzida pela função de criptografia autenticada.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="2fe1b-185">(Para nosso sistema, isso é fixo no tamanho da marca = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="2fe1b-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="2fe1b-186">[128 bits] A marca de `Enc_GCM (K_E, nonce, "")` , que é a saída do algoritmo de codificação de bloco simétrico, dada uma entrada de cadeia de caracteres vazia e em que o nonce é um vetor de todos-zero de 96 bits.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="2fe1b-187">`K_E`é derivado usando o mesmo mecanismo do cenário de autenticação CBC Encryption + HMAC.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="2fe1b-188">No entanto, como não há nada `K_H` em jogar aqui, essencialmente temos `| K_H | = 0` , e o algoritmo é recolhido para o formulário abaixo.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="2fe1b-189">Exemplo: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="2fe1b-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="2fe1b-190">Primeiro, deixe `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , onde `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="2fe1b-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="2fe1b-191">Em seguida, Compute a marca de autenticação do `Enc_GCM (K_E, nonce, "")` para AES-256-GCM fornecido `nonce = 096` e `K_E` como acima.</span><span class="sxs-lookup"><span data-stu-id="2fe1b-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="2fe1b-192">Isso produz o cabeçalho de contexto completo abaixo:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="2fe1b-193">Os componentes são divididos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="2fe1b-193">The components break down as follows:</span></span>

* <span data-ttu-id="2fe1b-194">o marcador`(00 01)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="2fe1b-195">o comprimento da chave de codificação do bloco`(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="2fe1b-196">o tamanho de nonce`(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="2fe1b-197">o tamanho do bloco de codificação de bloco`(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="2fe1b-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="2fe1b-198">o tamanho da marca de autenticação `(00 00 00 10)` e</span><span class="sxs-lookup"><span data-stu-id="2fe1b-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="2fe1b-199">a marca de autenticação da execução da codificação do bloco `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="2fe1b-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
