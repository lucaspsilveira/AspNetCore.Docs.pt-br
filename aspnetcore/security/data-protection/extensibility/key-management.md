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
# <a name="key-management-extensibility-in-aspnet-core"></a>Extensibilidade de gerenciamento de chaves no ASP.NET Core

> [!TIP]
> Leia a seção [Gerenciamento de chaves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) antes de ler esta seção, pois ela explica alguns dos conceitos fundamentais por trás dessas APIs.

> [!WARNING]
> Os tipos que implementam qualquer uma das interfaces a seguir devem ser thread-safe para vários chamadores.

## <a name="key"></a>Chave

A `IKey` interface é a representação básica de uma chave em cryptosystem. A chave de termo é usada aqui no sentido abstrato, não no sentido literal de "material de chave de criptografia". Uma chave tem as seguintes propriedades:

* Datas de ativação, criação e validade

* Status de revogação

* Identificador de chave (um GUID)

::: moniker range=">= aspnetcore-2.0"

Além disso `IKey` , o `CreateEncryptor` expõe um método que pode ser usado para criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) vinculada a essa chave.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Além disso `IKey` , o `CreateEncryptorInstance` expõe um método que pode ser usado para criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) vinculada a essa chave.

::: moniker-end

> [!NOTE]
> Não há API para recuperar o material de criptografia bruto de uma `IKey` instância.

## <a name="ikeymanager"></a>IKeyManager

A `IKeyManager` interface representa um objeto responsável por armazenamento de chave geral, recuperação e manipulação. Ele expõe três operações de alto nível:

* Crie uma nova chave e persista-a no armazenamento.

* Obter todas as chaves do armazenamento.

* Revogar uma ou mais chaves e persistir as informações de revogação para o armazenamento.

>[!WARNING]
> Escrever uma `IKeyManager` é uma tarefa muito avançada e a maioria dos desenvolvedores não deve tentar. Em vez disso, a maioria dos desenvolvedores deve aproveitar as facilidades oferecidas pela classe [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

O `XmlKeyManager` tipo é a implementação concreta da caixa de `IKeyManager`. Ele fornece várias instalações úteis, incluindo a caução de chave e a criptografia de chaves em repouso. As chaves neste sistema são representadas como elementos XML (especificamente, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager`depende de vários outros componentes no decorrer do cumprimento de suas tarefas:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, que determina os algoritmos usados por novas chaves.

* `IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.

* `IXmlEncryptor`[opcional], que permite criptografar chaves em repouso.

* `IKeyEscrowSink`[opcional], que fornece serviços de caução de chave.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.

* `IXmlEncryptor`[opcional], que permite criptografar chaves em repouso.

* `IKeyEscrowSink`[opcional], que fornece serviços de caução de chave.

::: moniker-end

Abaixo estão os diagramas de alto nível que indicam como esses componentes são conectados `XmlKeyManager`em conjunto.

::: moniker range=">= aspnetcore-2.0"

![Criação de chave](key-management/_static/keycreation2.png)

*Criação de chave/CreateNewKey*

Na implementação do `CreateNewKey`, o `AlgorithmConfiguration` componente é usado para criar um exclusivo `IAuthenticatedEncryptorDescriptor`, que é serializado como XML. Se um coletor de caução de chave estiver presente, o XML bruto (não criptografado) será fornecido ao coletor para armazenamento de longo prazo. Em seguida, o XML não criptografado é `IXmlEncryptor` executado por meio de um (se necessário) para gerar o documento XML criptografado. Esse documento criptografado é mantido para armazenamento de longo prazo por meio `IXmlRepository`do. (Se não `IXmlEncryptor` estiver configurado, o documento não criptografado será mantido no `IXmlRepository`.)

![Recuperação de chave](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Criação de chave](key-management/_static/keycreation1.png)

*Criação de chave/CreateNewKey*

Na implementação do `CreateNewKey`, o `IAuthenticatedEncryptorConfiguration` componente é usado para criar um exclusivo `IAuthenticatedEncryptorDescriptor`, que é serializado como XML. Se um coletor de caução de chave estiver presente, o XML bruto (não criptografado) será fornecido ao coletor para armazenamento de longo prazo. Em seguida, o XML não criptografado é `IXmlEncryptor` executado por meio de um (se necessário) para gerar o documento XML criptografado. Esse documento criptografado é mantido para armazenamento de longo prazo por meio `IXmlRepository`do. (Se não `IXmlEncryptor` estiver configurado, o documento não criptografado será mantido no `IXmlRepository`.)

![Recuperação de chave](key-management/_static/keyretrieval1.png)

::: moniker-end

*Recuperação de chave/GetAllKeys*

Na implementação de `GetAllKeys`, os documentos XML que representam chaves e revogações são lidos do subjacente `IXmlRepository`. Se esses documentos forem criptografados, o sistema irá descriptografá-los automaticamente. `XmlKeyManager`cria as instâncias `IAuthenticatedEncryptorDescriptorDeserializer` apropriadas para desserializar os documentos de volta `IAuthenticatedEncryptorDescriptor` em instâncias, que são encapsuladas em `IKey` instâncias individuais. Essa coleção de `IKey` instâncias é retornada ao chamador.

Mais informações sobre os elementos XML específicos podem ser encontradas no [documento de formato de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

A `IXmlRepository` interface representa um tipo que pode persistir XML e recuperar XML de um repositório de backup. Ele expõe duas APIs:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

As implementações do `IXmlRepository` não precisam analisar o XML transmitindo-as. Eles devem tratar os documentos XML como opacos e permitir que camadas mais altas se preocupem com a geração e análise dos documentos.

Há quatro tipos concretos internos que implementam `IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Consulte o [documento provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers) para obter mais informações.

O registro de um `IXmlRepository` personalizado é apropriado ao usar um repositório de backup diferente (por exemplo, armazenamento de tabelas do Azure).

Para alterar o aplicativo do repositório padrão, registre uma instância personalizada `IXmlRepository` :

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

A `IXmlEncryptor` interface representa um tipo que pode criptografar um elemento XML de texto não criptografado. Ele expõe uma única API:

* Encrypt (XElement texto não criptografado): EncryptedXmlInfo

Se uma serializada `IAuthenticatedEncryptorDescriptor` contiver qualquer elemento marcado como "requer criptografia" `XmlKeyManager` , executará esses elementos por meio `IXmlEncryptor`do `Encrypt` método configurado, e ele persistirá o elemento encriptografado em vez do elemento de texto `IXmlRepository`sem formatação para o. A saída do `Encrypt` método é um `EncryptedXmlInfo` objeto. Esse objeto é um wrapper que contém tanto a codificação resultante `XElement` quanto o tipo que representa um `IXmlDecryptor` que pode ser usado para decifrar o elemento correspondente.

Há quatro tipos concretos internos que implementam `IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Consulte o [documento criptografia de chave em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais informações.

Para alterar o mecanismo padrão de criptografia de chave-codificação em repouso em todo o aplicativo, registre `IXmlEncryptor` uma instância personalizada:

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

A `IXmlDecryptor` interface representa um tipo que sabe como descriptografar `XElement` um que foi codificado por meio de `IXmlEncryptor`um. Ele expõe uma única API:

* Descriptografar (XElement encryptelement): XElement

O `Decrypt` método desfaz a criptografia executada pelo `IXmlEncryptor.Encrypt`. Em geral, cada `IXmlEncryptor` implementação concreta terá uma implementação concreta `IXmlDecryptor` correspondente.

Os tipos que `IXmlDecryptor` implementam devem ter um dos dois construtores públicos a seguir:

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> O `IServiceProvider` passado para o construtor pode ser nulo.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

A `IKeyEscrowSink` interface representa um tipo que pode executar a caução de informações confidenciais. Lembre-se de que os descritores serializados podem conter informações confidenciais (como material criptográfico), e isso é o que levou à introdução do tipo [IXmlEncryptor](#ixmlencryptor) em primeiro lugar. No entanto, acidentes acontecem e anéis-chave podem ser excluídos ou corrompidos.

A interface de caução fornece um hachura de escape de emergência, permitindo o acesso ao XML serializado bruto antes que ele seja transformado por qualquer [IXmlEncryptor](#ixmlencryptor)configurada. A interface expõe uma única API:

* Store (keyId GUID, elemento XElement)

Cabe à `IKeyEscrowSink` implementação lidar com o elemento fornecido de maneira segura consistente com a política de negócios. Uma implementação possível poderia ser para o coletor de caução criptografar o elemento XML usando um certificado X. 509 corporativo conhecido em que a chave privada do certificado tenha sido caução; o `CertificateXmlEncryptor` tipo pode ajudar com isso. A `IKeyEscrowSink` implementação também é responsável por persistir o elemento fornecido adequadamente.

Por padrão, nenhum mecanismo de caução está habilitado, embora os administradores de servidor possam [configurar isso globalmente](xref:security/data-protection/configuration/machine-wide-policy). Ele também pode ser configurado programaticamente `IDataProtectionBuilder.AddKeyEscrowSink` por meio do método, conforme mostrado no exemplo abaixo. O `AddKeyEscrowSink` método sobrecarrega o espelhamento e `IServiceCollection.AddSingleton` `IServiceCollection.AddInstance` as sobrecargas, pois `IKeyEscrowSink` as instâncias devem ser singletons. Se várias `IKeyEscrowSink` instâncias forem registradas, cada uma será chamada durante a geração de chave, de modo que as chaves podem ter a garantia de vários mecanismos simultaneamente.

Não há API para ler o material de uma `IKeyEscrowSink` instância. Isso é consistente com a teoria de design do mecanismo de caução: ela se destina a tornar o material da chave acessível a uma autoridade confiável e, como o aplicativo não é uma autoridade confiável, ele não deve ter acesso a seu próprio material com garantia.

O código de exemplo a seguir demonstra como criar e `IKeyEscrowSink` registrar uma chave where, de forma que somente os membros de "CONTOSODomain admins" possam recuperá-las.

> [!NOTE]
> Para executar este exemplo, você deve estar em um computador Windows 8/Windows Server 2012 ingressado no domínio e o controlador de domínio deve ser o Windows Server 2012 ou posterior.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
