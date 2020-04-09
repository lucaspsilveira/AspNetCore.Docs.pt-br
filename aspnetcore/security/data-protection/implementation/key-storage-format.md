---
title: Formato de armazenamento chave em ASP.NET Core
author: rick-anderson
description: Saiba detalhes de implementação do ASP.NET formato de armazenamento chave da Proteção de Dados do Núcleo.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976931"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="f9270-103">Formato de armazenamento chave em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9270-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="f9270-104">Os objetos são armazenados em repouso na representação XML.</span><span class="sxs-lookup"><span data-stu-id="f9270-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="f9270-105">O diretório padrão para armazenamento de chaves é:</span><span class="sxs-lookup"><span data-stu-id="f9270-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="f9270-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="f9270-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="f9270-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="f9270-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="f9270-108">O \<elemento-chave></span><span class="sxs-lookup"><span data-stu-id="f9270-108">The \<key> element</span></span>

<span data-ttu-id="f9270-109">As teclas existem como objetos de alto nível no repositório de chaves.</span><span class="sxs-lookup"><span data-stu-id="f9270-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="f9270-110">Por chaves de convenção tem a chave de nome **de arquivo-{guid}.xml**, onde {guid} é o id da chave.</span><span class="sxs-lookup"><span data-stu-id="f9270-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="f9270-111">Cada arquivo contém uma única chave.</span><span class="sxs-lookup"><span data-stu-id="f9270-111">Each such file contains a single key.</span></span> <span data-ttu-id="f9270-112">O formato do arquivo é o seguinte.</span><span class="sxs-lookup"><span data-stu-id="f9270-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="f9270-113">O \<elemento>-chave contém os seguintes atributos e elementos da criança:</span><span class="sxs-lookup"><span data-stu-id="f9270-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="f9270-114">A chave de id. Esse valor é tratado como autoritário; o nome do arquivo é simplesmente uma gentileza para a legibilidade humana.</span><span class="sxs-lookup"><span data-stu-id="f9270-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="f9270-115">A versão \<da chave> elemento, atualmente fixada em 1.</span><span class="sxs-lookup"><span data-stu-id="f9270-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="f9270-116">As datas de criação, ativação e expiração da chave.</span><span class="sxs-lookup"><span data-stu-id="f9270-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="f9270-117">Um \<descritor> elemento, que contém informações sobre a implementação de criptografia autenticada contida nesta chave.</span><span class="sxs-lookup"><span data-stu-id="f9270-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="f9270-118">No exemplo acima, o id da chave é {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, foi criado e ativado em 19 de março de 2015, e tem uma vida útil de 90 dias.</span><span class="sxs-lookup"><span data-stu-id="f9270-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="f9270-119">(Ocasionalmente, a data de ativação pode ser ligeiramente antes da data de criação, como neste exemplo.</span><span class="sxs-lookup"><span data-stu-id="f9270-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="f9270-120">Isso se deve a um nit em como as APIs funcionam e é inofensivo na prática.)</span><span class="sxs-lookup"><span data-stu-id="f9270-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="f9270-121">O \<elemento> descritor</span><span class="sxs-lookup"><span data-stu-id="f9270-121">The \<descriptor> element</span></span>

<span data-ttu-id="f9270-122">O \<descritor externo> elemento contém um deserializador de atributoSType, que é o nome qualificado para montagem de um tipo que implementa IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="f9270-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="f9270-123">Este tipo é responsável \<pela leitura do descritor interno> elemento e pela análise das informações contidas.</span><span class="sxs-lookup"><span data-stu-id="f9270-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="f9270-124">O formato particular \<do> elemento depende da implementação de criptografador autenticada encapsulada pela chave, e cada tipo dedeserializador espera um formato ligeiramente diferente para isso.</span><span class="sxs-lookup"><span data-stu-id="f9270-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="f9270-125">Em geral, porém, este elemento conterá informações algorítmicas (nomes, tipos, OIDs ou similares) e material chave secreto.</span><span class="sxs-lookup"><span data-stu-id="f9270-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="f9270-126">No exemplo acima, o descritor especifica que esta chave envolve criptografia AES-256-CBC + validação HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="f9270-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="f9270-127">O \<elemento de> secreto criptografado</span><span class="sxs-lookup"><span data-stu-id="f9270-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="f9270-128">Um \*\* &lt;elemento&gt; secreto criptografado\*\* que contém a forma criptografada do material chave secreto pode estar presente se a [criptografia de segredos em repouso estiver ativada](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="f9270-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="f9270-129">O `decryptorType` atributo é o nome qualificado para montagem de um tipo que implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="f9270-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="f9270-130">Esse tipo é responsável por ler o elemento \*\* &lt;Key&gt; criptografado\*\* interno e descriptografá-lo para recuperar o texto simples original.</span><span class="sxs-lookup"><span data-stu-id="f9270-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="f9270-131">Como `<descriptor>`acontece, o formato `<encryptedSecret>` específico do elemento depende do mecanismo de criptografia em repouso em uso.</span><span class="sxs-lookup"><span data-stu-id="f9270-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="f9270-132">No exemplo acima, a chave mestra é criptografada usando o Windows DPAPI de acordo com o comentário.</span><span class="sxs-lookup"><span data-stu-id="f9270-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="f9270-133">O \<elemento de revogação></span><span class="sxs-lookup"><span data-stu-id="f9270-133">The \<revocation> element</span></span>

<span data-ttu-id="f9270-134">As revogações existem como objetos de alto nível no repositório de chaves.</span><span class="sxs-lookup"><span data-stu-id="f9270-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="f9270-135">Por revogações de convenção tem a revogação do nome **do arquivo-{timestamp}.xml** (para revogar todas as chaves antes de uma data específica) ou **revogação-{guid}.xml** (para revogar uma chave específica).</span><span class="sxs-lookup"><span data-stu-id="f9270-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="f9270-136">Cada arquivo contém \<uma única revogação> elemento.</span><span class="sxs-lookup"><span data-stu-id="f9270-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="f9270-137">Para revogações de chaves individuais, o conteúdo do arquivo será o seguinte.</span><span class="sxs-lookup"><span data-stu-id="f9270-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="f9270-138">Neste caso, apenas a chave especificada é revogada.</span><span class="sxs-lookup"><span data-stu-id="f9270-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="f9270-139">Se o id da chave for "\*", no entanto, como no exemplo abaixo, todas as chaves cuja data de criação é anterior à data de revogação especificada são revogadas.</span><span class="sxs-lookup"><span data-stu-id="f9270-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="f9270-140">A \<razão pela qual> elemento nunca é lido pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="f9270-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="f9270-141">É simplesmente um lugar conveniente para armazenar uma razão legível por humanos para a revogação.</span><span class="sxs-lookup"><span data-stu-id="f9270-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
