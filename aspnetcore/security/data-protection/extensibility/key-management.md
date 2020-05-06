---
title: Extensibilidade de gerenciamento de chaves no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a extensibilidade de gerenciamento de chaves de proteção de dados ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: f8af699344473510c5579c2f0e4d2920ada013f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775720"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="f3be4-103">Extensibilidade de gerenciamento de chaves no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3be4-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="f3be4-104">Leia a seção [Gerenciamento de chaves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) antes de ler esta seção, pois ela explica alguns dos conceitos fundamentais por trás dessas APIs.</span><span class="sxs-lookup"><span data-stu-id="f3be4-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="f3be4-105">Os tipos que implementam qualquer uma das interfaces a seguir devem ser thread-safe para vários chamadores.</span><span class="sxs-lookup"><span data-stu-id="f3be4-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="f3be4-106">Chave</span><span class="sxs-lookup"><span data-stu-id="f3be4-106">Key</span></span>

<span data-ttu-id="f3be4-107">A `IKey` interface é a representação básica de uma chave em cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="f3be4-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="f3be4-108">A chave de termo é usada aqui no sentido abstrato, não no sentido literal de "material de chave de criptografia".</span><span class="sxs-lookup"><span data-stu-id="f3be4-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="f3be4-109">Uma chave tem as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="f3be4-109">A key has the following properties:</span></span>

* <span data-ttu-id="f3be4-110">Datas de ativação, criação e validade</span><span class="sxs-lookup"><span data-stu-id="f3be4-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="f3be4-111">Status de revogação</span><span class="sxs-lookup"><span data-stu-id="f3be4-111">Revocation status</span></span>

* <span data-ttu-id="f3be4-112">Identificador de chave (um GUID)</span><span class="sxs-lookup"><span data-stu-id="f3be4-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f3be4-113">Além disso `IKey` , o `CreateEncryptor` expõe um método que pode ser usado para criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) vinculada a essa chave.</span><span class="sxs-lookup"><span data-stu-id="f3be4-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f3be4-114">Além disso `IKey` , o `CreateEncryptorInstance` expõe um método que pode ser usado para criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) vinculada a essa chave.</span><span class="sxs-lookup"><span data-stu-id="f3be4-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f3be4-115">Não há API para recuperar o material de criptografia bruto de uma `IKey` instância.</span><span class="sxs-lookup"><span data-stu-id="f3be4-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="f3be4-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="f3be4-116">IKeyManager</span></span>

<span data-ttu-id="f3be4-117">A `IKeyManager` interface representa um objeto responsável por armazenamento de chave geral, recuperação e manipulação.</span><span class="sxs-lookup"><span data-stu-id="f3be4-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="f3be4-118">Ele expõe três operações de alto nível:</span><span class="sxs-lookup"><span data-stu-id="f3be4-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="f3be4-119">Crie uma nova chave e persista-a no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="f3be4-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="f3be4-120">Obter todas as chaves do armazenamento.</span><span class="sxs-lookup"><span data-stu-id="f3be4-120">Get all keys from storage.</span></span>

* <span data-ttu-id="f3be4-121">Revogar uma ou mais chaves e persistir as informações de revogação para o armazenamento.</span><span class="sxs-lookup"><span data-stu-id="f3be4-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="f3be4-122">Escrever uma `IKeyManager` é uma tarefa muito avançada e a maioria dos desenvolvedores não deve tentar.</span><span class="sxs-lookup"><span data-stu-id="f3be4-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="f3be4-123">Em vez disso, a maioria dos desenvolvedores deve aproveitar as facilidades oferecidas pela classe [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="f3be4-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="f3be4-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="f3be4-124">XmlKeyManager</span></span>

<span data-ttu-id="f3be4-125">O `XmlKeyManager` tipo é a implementação concreta da caixa de `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="f3be4-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="f3be4-126">Ele fornece várias instalações úteis, incluindo a caução de chave e a criptografia de chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="f3be4-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="f3be4-127">As chaves neste sistema são representadas como elementos XML (especificamente, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="f3be4-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="f3be4-128">`XmlKeyManager`depende de vários outros componentes no decorrer do cumprimento de suas tarefas:</span><span class="sxs-lookup"><span data-stu-id="f3be4-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="f3be4-129">`AlgorithmConfiguration`, que determina os algoritmos usados por novas chaves.</span><span class="sxs-lookup"><span data-stu-id="f3be4-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="f3be4-130">`IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="f3be4-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="f3be4-131">`IXmlEncryptor`[opcional], que permite criptografar chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="f3be4-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="f3be4-132">`IKeyEscrowSink`[opcional], que fornece serviços de caução de chave.</span><span class="sxs-lookup"><span data-stu-id="f3be4-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="f3be4-133">`IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="f3be4-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="f3be4-134">`IXmlEncryptor`[opcional], que permite criptografar chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="f3be4-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="f3be4-135">`IKeyEscrowSink`[opcional], que fornece serviços de caução de chave.</span><span class="sxs-lookup"><span data-stu-id="f3be4-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="f3be4-136">Abaixo estão os diagramas de alto nível que indicam como esses componentes são conectados `XmlKeyManager`em conjunto.</span><span class="sxs-lookup"><span data-stu-id="f3be4-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Criação de chave](key-management/_static/keycreation2.png)

<span data-ttu-id="f3be4-138">*Criação de chave/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="f3be4-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="f3be4-139">Na implementação do `CreateNewKey`, o `AlgorithmConfiguration` componente é usado para criar um exclusivo `IAuthenticatedEncryptorDescriptor`, que é serializado como XML.</span><span class="sxs-lookup"><span data-stu-id="f3be4-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="f3be4-140">Se um coletor de caução de chave estiver presente, o XML bruto (não criptografado) será fornecido ao coletor para armazenamento de longo prazo.</span><span class="sxs-lookup"><span data-stu-id="f3be4-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="f3be4-141">Em seguida, o XML não criptografado é `IXmlEncryptor` executado por meio de um (se necessário) para gerar o documento XML criptografado.</span><span class="sxs-lookup"><span data-stu-id="f3be4-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="f3be4-142">Esse documento criptografado é mantido para armazenamento de longo prazo por meio `IXmlRepository`do.</span><span class="sxs-lookup"><span data-stu-id="f3be4-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="f3be4-143">(Se não `IXmlEncryptor` estiver configurado, o documento não criptografado será mantido no `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="f3be4-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recuperação de chave](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Criação de chave](key-management/_static/keycreation1.png)

<span data-ttu-id="f3be4-146">*Criação de chave/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="f3be4-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="f3be4-147">Na implementação do `CreateNewKey`, o `IAuthenticatedEncryptorConfiguration` componente é usado para criar um exclusivo `IAuthenticatedEncryptorDescriptor`, que é serializado como XML.</span><span class="sxs-lookup"><span data-stu-id="f3be4-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="f3be4-148">Se um coletor de caução de chave estiver presente, o XML bruto (não criptografado) será fornecido ao coletor para armazenamento de longo prazo.</span><span class="sxs-lookup"><span data-stu-id="f3be4-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="f3be4-149">Em seguida, o XML não criptografado é `IXmlEncryptor` executado por meio de um (se necessário) para gerar o documento XML criptografado.</span><span class="sxs-lookup"><span data-stu-id="f3be4-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="f3be4-150">Esse documento criptografado é mantido para armazenamento de longo prazo por meio `IXmlRepository`do.</span><span class="sxs-lookup"><span data-stu-id="f3be4-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="f3be4-151">(Se não `IXmlEncryptor` estiver configurado, o documento não criptografado será mantido no `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="f3be4-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recuperação de chave](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="f3be4-153">*Recuperação de chave/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="f3be4-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="f3be4-154">Na implementação de `GetAllKeys`, os documentos XML que representam chaves e revogações são lidos do subjacente `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="f3be4-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="f3be4-155">Se esses documentos forem criptografados, o sistema irá descriptografá-los automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f3be4-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="f3be4-156">`XmlKeyManager`cria as instâncias `IAuthenticatedEncryptorDescriptorDeserializer` apropriadas para desserializar os documentos de volta `IAuthenticatedEncryptorDescriptor` em instâncias, que são encapsuladas em `IKey` instâncias individuais.</span><span class="sxs-lookup"><span data-stu-id="f3be4-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="f3be4-157">Essa coleção de `IKey` instâncias é retornada ao chamador.</span><span class="sxs-lookup"><span data-stu-id="f3be4-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="f3be4-158">Mais informações sobre os elementos XML específicos podem ser encontradas no [documento de formato de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="f3be4-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="f3be4-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-159">IXmlRepository</span></span>

<span data-ttu-id="f3be4-160">A `IXmlRepository` interface representa um tipo que pode persistir XML e recuperar XML de um repositório de backup.</span><span class="sxs-lookup"><span data-stu-id="f3be4-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="f3be4-161">Ele expõe duas APIs:</span><span class="sxs-lookup"><span data-stu-id="f3be4-161">It exposes two APIs:</span></span>

* <span data-ttu-id="f3be4-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="f3be4-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="f3be4-163">As implementações do `IXmlRepository` não precisam analisar o XML transmitindo-as.</span><span class="sxs-lookup"><span data-stu-id="f3be4-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="f3be4-164">Eles devem tratar os documentos XML como opacos e permitir que camadas mais altas se preocupem com a geração e análise dos documentos.</span><span class="sxs-lookup"><span data-stu-id="f3be4-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="f3be4-165">Há quatro tipos concretos internos que implementam `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="f3be4-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="f3be4-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="f3be4-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="f3be4-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="f3be4-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="f3be4-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="f3be4-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="f3be4-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="f3be4-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="f3be4-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="f3be4-174">Consulte o [documento provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f3be4-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="f3be4-175">O registro de um `IXmlRepository` personalizado é apropriado ao usar um repositório de backup diferente (por exemplo, armazenamento de tabelas do Azure).</span><span class="sxs-lookup"><span data-stu-id="f3be4-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="f3be4-176">Para alterar o aplicativo do repositório padrão, registre uma instância personalizada `IXmlRepository` :</span><span class="sxs-lookup"><span data-stu-id="f3be4-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="f3be4-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="f3be4-177">IXmlEncryptor</span></span>

<span data-ttu-id="f3be4-178">A `IXmlEncryptor` interface representa um tipo que pode criptografar um elemento XML de texto não criptografado.</span><span class="sxs-lookup"><span data-stu-id="f3be4-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="f3be4-179">Ele expõe uma única API:</span><span class="sxs-lookup"><span data-stu-id="f3be4-179">It exposes a single API:</span></span>

* <span data-ttu-id="f3be4-180">Encrypt (XElement texto não criptografado): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="f3be4-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="f3be4-181">Se uma serializada `IAuthenticatedEncryptorDescriptor` contiver qualquer elemento marcado como "requer criptografia" `XmlKeyManager` , executará esses elementos por meio `IXmlEncryptor`do `Encrypt` método configurado, e ele persistirá o elemento encriptografado em vez do elemento de texto `IXmlRepository`sem formatação para o.</span><span class="sxs-lookup"><span data-stu-id="f3be4-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="f3be4-182">A saída do `Encrypt` método é um `EncryptedXmlInfo` objeto.</span><span class="sxs-lookup"><span data-stu-id="f3be4-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="f3be4-183">Esse objeto é um wrapper que contém tanto a codificação resultante `XElement` quanto o tipo que representa um `IXmlDecryptor` que pode ser usado para decifrar o elemento correspondente.</span><span class="sxs-lookup"><span data-stu-id="f3be4-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="f3be4-184">Há quatro tipos concretos internos que implementam `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="f3be4-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="f3be4-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="f3be4-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="f3be4-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="f3be4-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="f3be4-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="f3be4-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="f3be4-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="f3be4-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="f3be4-189">Consulte o [documento criptografia de chave em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f3be4-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="f3be4-190">Para alterar o mecanismo padrão de criptografia de chave-codificação em repouso em todo o aplicativo, registre `IXmlEncryptor` uma instância personalizada:</span><span class="sxs-lookup"><span data-stu-id="f3be4-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="f3be4-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="f3be4-191">IXmlDecryptor</span></span>

<span data-ttu-id="f3be4-192">A `IXmlDecryptor` interface representa um tipo que sabe como descriptografar `XElement` um que foi codificado por meio de `IXmlEncryptor`um.</span><span class="sxs-lookup"><span data-stu-id="f3be4-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="f3be4-193">Ele expõe uma única API:</span><span class="sxs-lookup"><span data-stu-id="f3be4-193">It exposes a single API:</span></span>

* <span data-ttu-id="f3be4-194">Descriptografar (XElement encryptelement): XElement</span><span class="sxs-lookup"><span data-stu-id="f3be4-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="f3be4-195">O `Decrypt` método desfaz a criptografia executada pelo `IXmlEncryptor.Encrypt`.</span><span class="sxs-lookup"><span data-stu-id="f3be4-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="f3be4-196">Em geral, cada `IXmlEncryptor` implementação concreta terá uma implementação concreta `IXmlDecryptor` correspondente.</span><span class="sxs-lookup"><span data-stu-id="f3be4-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="f3be4-197">Os tipos que `IXmlDecryptor` implementam devem ter um dos dois construtores públicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="f3be4-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="f3be4-198">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="f3be4-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="f3be4-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="f3be4-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="f3be4-200">O `IServiceProvider` passado para o construtor pode ser nulo.</span><span class="sxs-lookup"><span data-stu-id="f3be4-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="f3be4-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="f3be4-201">IKeyEscrowSink</span></span>

<span data-ttu-id="f3be4-202">A `IKeyEscrowSink` interface representa um tipo que pode executar a caução de informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="f3be4-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="f3be4-203">Lembre-se de que os descritores serializados podem conter informações confidenciais (como material criptográfico), e isso é o que levou à introdução do tipo [IXmlEncryptor](#ixmlencryptor) em primeiro lugar.</span><span class="sxs-lookup"><span data-stu-id="f3be4-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="f3be4-204">No entanto, acidentes acontecem e anéis-chave podem ser excluídos ou corrompidos.</span><span class="sxs-lookup"><span data-stu-id="f3be4-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="f3be4-205">A interface de caução fornece um hachura de escape de emergência, permitindo o acesso ao XML serializado bruto antes que ele seja transformado por qualquer [IXmlEncryptor](#ixmlencryptor)configurada.</span><span class="sxs-lookup"><span data-stu-id="f3be4-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="f3be4-206">A interface expõe uma única API:</span><span class="sxs-lookup"><span data-stu-id="f3be4-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="f3be4-207">Store (keyId GUID, elemento XElement)</span><span class="sxs-lookup"><span data-stu-id="f3be4-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="f3be4-208">Cabe à `IKeyEscrowSink` implementação lidar com o elemento fornecido de maneira segura consistente com a política de negócios.</span><span class="sxs-lookup"><span data-stu-id="f3be4-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="f3be4-209">Uma implementação possível poderia ser para o coletor de caução criptografar o elemento XML usando um certificado X. 509 corporativo conhecido em que a chave privada do certificado tenha sido caução; o `CertificateXmlEncryptor` tipo pode ajudar com isso.</span><span class="sxs-lookup"><span data-stu-id="f3be4-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="f3be4-210">A `IKeyEscrowSink` implementação também é responsável por persistir o elemento fornecido adequadamente.</span><span class="sxs-lookup"><span data-stu-id="f3be4-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="f3be4-211">Por padrão, nenhum mecanismo de caução está habilitado, embora os administradores de servidor possam [configurar isso globalmente](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="f3be4-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="f3be4-212">Ele também pode ser configurado programaticamente `IDataProtectionBuilder.AddKeyEscrowSink` por meio do método, conforme mostrado no exemplo abaixo.</span><span class="sxs-lookup"><span data-stu-id="f3be4-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="f3be4-213">O `AddKeyEscrowSink` método sobrecarrega o espelhamento e `IServiceCollection.AddSingleton` `IServiceCollection.AddInstance` as sobrecargas, pois `IKeyEscrowSink` as instâncias devem ser singletons.</span><span class="sxs-lookup"><span data-stu-id="f3be4-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="f3be4-214">Se várias `IKeyEscrowSink` instâncias forem registradas, cada uma será chamada durante a geração de chave, de modo que as chaves podem ter a garantia de vários mecanismos simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="f3be4-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="f3be4-215">Não há API para ler o material de uma `IKeyEscrowSink` instância.</span><span class="sxs-lookup"><span data-stu-id="f3be4-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="f3be4-216">Isso é consistente com a teoria de design do mecanismo de caução: ela se destina a tornar o material da chave acessível a uma autoridade confiável e, como o aplicativo não é uma autoridade confiável, ele não deve ter acesso a seu próprio material com garantia.</span><span class="sxs-lookup"><span data-stu-id="f3be4-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="f3be4-217">O código de exemplo a seguir demonstra como criar e `IKeyEscrowSink` registrar uma chave where, de forma que somente os membros de "CONTOSODomain admins" possam recuperá-las.</span><span class="sxs-lookup"><span data-stu-id="f3be4-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="f3be4-218">Para executar este exemplo, você deve estar em um computador Windows 8/Windows Server 2012 ingressado no domínio e o controlador de domínio deve ser o Windows Server 2012 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="f3be4-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
