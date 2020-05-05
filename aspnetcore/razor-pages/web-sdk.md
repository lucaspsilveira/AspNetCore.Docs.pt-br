---
title: SDK do ASP.NET Core Web
author: Rick-Anderson
description: Visão geral de Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/web-sdk
ms.openlocfilehash: 2797f0b3003b8ad89093fe1115dee2acc8650c73
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777157"
---
# <a name="aspnet-core-web-sdk"></a>SDK do ASP.NET Core Web

### <a name="overview"></a>Visão geral

`Microsoft.NET.Sdk.Web`é um [SDK de projeto do MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para compilar ASP.NET Core aplicativos. É possível criar um aplicativo ASP.NET Core sem esse SDK, no entanto, o SDK da Web é:

* Adaptado em direção à oferta de uma experiência de primeira classe.
* O destino recomendado para a maioria dos usuários.

Use o Web. SDK em um projeto do:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Recursos habilitados usando o SDK da Web:

* Projetos voltados para o .NET Core 3,0 ou posterior referência implícita:

  * A [estrutura compartilhada ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analisadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) projetados para a criação de aplicativos ASP.NET Core.
* O SDK da Web importa destinos do MSBuild que habilitam o uso de perfis de publicação e publicação usando o WebDeploy.

### <a name="properties"></a>Propriedades

| Propriedade | Descrição |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Desabilita a referência implícita à estrutura `Microsoft.AspNetCore.App` compartilhada. |
| `DisableImplicitAspNetCoreAnalyzers` | Desabilita a referência implícita a ASP.NET Core analisadores. |
| `DisableImplicitComponentsAnalyzers` | Desabilita a referência implícita aos analisadores de Razor componentes durante a Blazor compilação de aplicativos (servidor). |
