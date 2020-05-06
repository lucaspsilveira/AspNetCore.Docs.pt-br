---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte Blazorpara ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1c78803e6468f924bf8c8e9403a34565b114006f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771110"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core plataformas suportadas com suporte

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Requisitos de navegador

### <a name="blazor-webassembly"></a>WebAssembly Blazor

| Navegador                          | Versão               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, incluindo Android | Current               |
| Safari, incluindo iOS            | Current               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

&dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### <a name="blazor-server"></a>Servidor Blazor

| Navegador                          | Versão    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, incluindo Android | Current    |
| Safari, incluindo iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Os suportes retroativos adicionais são necessários (por exemplo, as promessas podem ser adicionadas por meio de um pacote [polyfill.Io](https://polyfill.io/v3/) ).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
