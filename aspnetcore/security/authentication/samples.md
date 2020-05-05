---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776546"
---
# <a name="authentication-samples-for-aspnet-core"></a>Exemplos de autenticação para ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :

* [Transformação de declarações](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookie](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Provedor de política personalizada-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Opções e esquemas de autenticação dinâmica](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre o cookie e outro esquema de autenticação com base na solicitação](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar as amostras

* Selecione uma [ramificação](https://github.com/dotnet/AspNetCore). Por exemplo, `release/3.1`
* Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.
* Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :

* [Transformação de declarações](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookie](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Provedor de política personalizada-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Opções e esquemas de autenticação dinâmica](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Declarações externas](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre o cookie e outro esquema de autenticação com base na solicitação](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar as amostras

* Selecione uma [ramificação](https://github.com/dotnet/AspNetCore). Por exemplo, `release/2.2`
* Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.
* Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.

::: moniker-end
