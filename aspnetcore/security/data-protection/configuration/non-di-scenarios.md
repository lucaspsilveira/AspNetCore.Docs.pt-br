---
title: Cenários sem reconhecimento de DI para a Proteção de Dados no ASP.NET Core
author: rick-anderson
description: Saiba como dar suporte a cenários de proteção de dados em que você não pode ou não deseja usar um serviço fornecido pela injeção de dependência.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 31013e97038338d72c98151e23a5caa68008ce4f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776819"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Cenários sem reconhecimento de DI para a Proteção de Dados no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O sistema de proteção de dados de ASP.NET Core normalmente é [adicionado a um contêiner de serviço](xref:security/data-protection/consumer-apis/overview) e consumido por componentes dependentes por meio de injeção de dependência (di). No entanto, há casos em que isso não é viável ou desejado, especialmente ao importar o sistema para um aplicativo existente.

Para dar suporte a esses cenários, o pacote [Microsoft. AspNetCore. Dataprotegetion. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) fornece um tipo concreto, [dataprotectionprovider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), que oferece uma maneira simples de usar a proteção de dados sem depender de di. O `DataProtectionProvider` tipo implementa [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). A construção `DataProtectionProvider` requer apenas fornecer uma instância de [DirectoryInfo](/dotnet/api/system.io.directoryinfo) para indicar onde as chaves criptográficas do provedor devem ser armazenadas, como visto no exemplo de código a seguir:

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

Por padrão, o `DataProtectionProvider` tipo concreto não criptografa o material de chave bruta antes de mantê-lo no sistema de arquivos. Isso é para dar suporte a cenários em que o desenvolvedor aponta para um compartilhamento de rede e o sistema de proteção de dados não pode deduzir automaticamente um mecanismo de criptografia de chave em repouso apropriado.

Além disso, `DataProtectionProvider` o tipo concreto não [isola aplicativos](xref:security/data-protection/configuration/overview#per-application-isolation) por padrão. Todos os aplicativos que usam o mesmo diretório de chave podem compartilhar cargas, desde que seus [parâmetros de finalidade](xref:security/data-protection/consumer-apis/purpose-strings) correspondam.

O construtor [Dataprotectionprovider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) aceita um retorno de chamada de configuração opcional que pode ser usado para ajustar os comportamentos do sistema. O exemplo a seguir demonstra a restauração de isolamento com uma chamada explícita para [Setapplicationname](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). O exemplo também demonstra como configurar o sistema para criptografar automaticamente chaves persistentes usando o Windows DPAPI. Se o diretório apontar para um compartilhamento UNC, talvez você queira distribuir um certificado compartilhado em todos os computadores relevantes e configurar o sistema para usar a criptografia baseada em certificado com uma chamada para [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> As `DataProtectionProvider` instâncias do tipo concreto são caras para criar. Se um aplicativo mantiver várias instâncias desse tipo e se todas estiverem usando o mesmo diretório de armazenamento de chaves, o desempenho do aplicativo poderá ser prejudicado. Se você usar o `DataProtectionProvider` tipo, recomendamos que você crie esse tipo uma vez e reutilize-o o máximo possível. O `DataProtectionProvider` tipo e todas as instâncias de [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) criadas a partir dele são thread-safe para vários chamadores.
