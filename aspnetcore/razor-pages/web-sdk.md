---
title: ASP.NET Núcleo Web SDK
author: Rick-Anderson
description: Visão geral do Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661052"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Núcleo Web SDK

### <a name="overview"></a>Visão geral

`Microsoft.NET.Sdk.Web`é um [SDK do projeto MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para a construção de aplicativos ASP.NET Core. É possível construir um aplicativo ASP.NET Core sem este SDK, no entanto, o Web SDK é:

* Adaptado para proporcionar uma experiência de primeira classe.
* O alvo recomendado para a maioria dos usuários.

Use o Web.SDK em um projeto:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Recursos habilitados usando o Web SDK:

* Projetos direcionados ao .NET Core 3.0 ou posteriormente implicitamente referenciam:

  * A [estrutura compartilhada do Núcleo ASP.NET](xref:fundamentals/metapackage-app).
  * [Analisadores projetados](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) para construir aplicativos ASP.NET Core.
* O Web SDK importa metas MSBuild que permitem o uso de perfis de publicação e publicação usando o WebDeploy.

### <a name="properties"></a>Propriedades

| Propriedade | Descrição |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Desativa a `Microsoft.AspNetCore.App` referência implícita à estrutura compartilhada. |
| `DisableImplicitAspNetCoreAnalyzers` | Desativa a referência implícita aos analisadores ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Desativa a referência implícita aos analisadores de componentes de barbear ao criar Blazor aplicativos (servidor). |
