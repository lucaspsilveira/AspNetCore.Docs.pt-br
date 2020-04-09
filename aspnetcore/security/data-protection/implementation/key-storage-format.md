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
# <a name="key-storage-format-in-aspnet-core"></a>Formato de armazenamento chave em ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Os objetos são armazenados em repouso na representação XML. O diretório padrão para armazenamento de chaves é:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*

## <a name="the-key-element"></a>O \<elemento-chave>

As teclas existem como objetos de alto nível no repositório de chaves. Por chaves de convenção tem a chave de nome **de arquivo-{guid}.xml**, onde {guid} é o id da chave. Cada arquivo contém uma única chave. O formato do arquivo é o seguinte.

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

O \<elemento>-chave contém os seguintes atributos e elementos da criança:

* A chave de id. Esse valor é tratado como autoritário; o nome do arquivo é simplesmente uma gentileza para a legibilidade humana.

* A versão \<da chave> elemento, atualmente fixada em 1.

* As datas de criação, ativação e expiração da chave.

* Um \<descritor> elemento, que contém informações sobre a implementação de criptografia autenticada contida nesta chave.

No exemplo acima, o id da chave é {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, foi criado e ativado em 19 de março de 2015, e tem uma vida útil de 90 dias. (Ocasionalmente, a data de ativação pode ser ligeiramente antes da data de criação, como neste exemplo. Isso se deve a um nit em como as APIs funcionam e é inofensivo na prática.)

## <a name="the-descriptor-element"></a>O \<elemento> descritor

O \<descritor externo> elemento contém um deserializador de atributoSType, que é o nome qualificado para montagem de um tipo que implementa IAuthenticatedEncryptorDescriptorDeserializer. Este tipo é responsável \<pela leitura do descritor interno> elemento e pela análise das informações contidas.

O formato particular \<do> elemento depende da implementação de criptografador autenticada encapsulada pela chave, e cada tipo dedeserializador espera um formato ligeiramente diferente para isso. Em geral, porém, este elemento conterá informações algorítmicas (nomes, tipos, OIDs ou similares) e material chave secreto. No exemplo acima, o descritor especifica que esta chave envolve criptografia AES-256-CBC + validação HMACSHA256.

## <a name="the-encryptedsecret-element"></a>O \<elemento de> secreto criptografado

Um ** &lt;elemento&gt; secreto criptografado** que contém a forma criptografada do material chave secreto pode estar presente se a [criptografia de segredos em repouso estiver ativada](xref:security/data-protection/implementation/key-encryption-at-rest). O `decryptorType` atributo é o nome qualificado para montagem de um tipo que implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Esse tipo é responsável por ler o elemento ** &lt;Key&gt; criptografado** interno e descriptografá-lo para recuperar o texto simples original.

Como `<descriptor>`acontece, o formato `<encryptedSecret>` específico do elemento depende do mecanismo de criptografia em repouso em uso. No exemplo acima, a chave mestra é criptografada usando o Windows DPAPI de acordo com o comentário.

## <a name="the-revocation-element"></a>O \<elemento de revogação>

As revogações existem como objetos de alto nível no repositório de chaves. Por revogações de convenção tem a revogação do nome **do arquivo-{timestamp}.xml** (para revogar todas as chaves antes de uma data específica) ou **revogação-{guid}.xml** (para revogar uma chave específica). Cada arquivo contém \<uma única revogação> elemento.

Para revogações de chaves individuais, o conteúdo do arquivo será o seguinte.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

Neste caso, apenas a chave especificada é revogada. Se o id da chave for "*", no entanto, como no exemplo abaixo, todas as chaves cuja data de criação é anterior à data de revogação especificada são revogadas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

A \<razão pela qual> elemento nunca é lido pelo sistema. É simplesmente um lugar conveniente para armazenar uma razão legível por humanos para a revogação.
