---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401929"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor plataformas com suporte

Por [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Requisitos de navegador

### Blazor WebAssembly

| Navegador                          | Versão               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, incluindo Android | Current               |
| Safari, incluindo iOS            | Current               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

&dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### Blazor Server

| Navegador                          | Versão    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, incluindo Android | Current    |
| Safari, incluindo iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Os suportes retroativos adicionais são necessários (por exemplo, as promessas podem ser adicionadas por meio de um [`Polyfill.io`](https://polyfill.io/v3/) pacote).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
