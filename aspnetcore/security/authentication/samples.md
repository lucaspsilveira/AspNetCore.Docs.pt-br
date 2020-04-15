---
title: Amostras de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para as amostras de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308209"
---
# <a name="authentication-samples-for-aspnet-core"></a>Amostras de autenticação para ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

O [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore) contém as seguintes amostras de autenticação na pasta *AspNetCore/src/Security/samples:*

* [Transformação de declarações](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookies](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Provedor de apólices personalizadas - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Esquemas e opções de autenticação dinâmica](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Reivindicações externas](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre cookie e outro esquema de autenticação com base na solicitação](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar as amostras

* Selecione um [ramo](https://github.com/dotnet/AspNetCore). Por exemplo, `release/3.1`
* Clonar ou baixar o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verifique se você instalou a versão [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) que corresponde ao clone do repositório ASP.NET Core.
* Navegue até uma amostra em *AspNetCore/src/Security/samples* e execute a amostra com `dotnet run`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore) contém as seguintes amostras de autenticação na pasta *AspNetCore/src/Security/samples:*

* [Transformação de declarações](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Autenticação de cookies](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Provedor de apólices personalizadas - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Esquemas e opções de autenticação dinâmica](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Reivindicações externas](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Seleção entre cookie e outro esquema de autenticação com base na solicitação](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restringe o acesso a arquivos estáticos](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Executar as amostras

* Selecione um [ramo](https://github.com/dotnet/AspNetCore). Por exemplo, `release/2.2`
* Clonar ou baixar o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Verifique se você instalou a versão [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) que corresponde ao clone do repositório ASP.NET Core.
* Navegue até uma amostra em *AspNetCore/src/Security/samples* e execute a amostra com `dotnet run`.

::: moniker-end
