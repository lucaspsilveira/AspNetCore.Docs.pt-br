---
title: plataformas Blazor suportadas pelo ASP.NET Core
author: guardrex
description: Conheça as plataformas suportadas Blazorpara ASP.NET Core .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658854"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Plataformas suportadas pelo Core Blazor

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
| Microsoft Internet Explorer      | Não suportado&dagger; |

&dagger;O Microsoft Internet Explorer não suporta [WebAssembly](https://webassembly.org).

### <a name="blazor-server"></a>Servidor Blazor

| Navegador                          | Versão    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, incluindo Android | Current    |
| Safari, incluindo iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;São necessários polipreenchimentos adicionais (por exemplo, promessas podem ser adicionadas através de um pacote [de Polyfill.io).](https://polyfill.io/v3/)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
