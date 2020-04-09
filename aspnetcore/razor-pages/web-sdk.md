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
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="5b227-103">ASP.NET Núcleo Web SDK</span><span class="sxs-lookup"><span data-stu-id="5b227-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="5b227-104">Visão geral</span><span class="sxs-lookup"><span data-stu-id="5b227-104">Overview</span></span>

<span data-ttu-id="5b227-105">`Microsoft.NET.Sdk.Web`é um [SDK do projeto MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para a construção de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b227-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="5b227-106">É possível construir um aplicativo ASP.NET Core sem este SDK, no entanto, o Web SDK é:</span><span class="sxs-lookup"><span data-stu-id="5b227-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="5b227-107">Adaptado para proporcionar uma experiência de primeira classe.</span><span class="sxs-lookup"><span data-stu-id="5b227-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="5b227-108">O alvo recomendado para a maioria dos usuários.</span><span class="sxs-lookup"><span data-stu-id="5b227-108">The recommended target for most users.</span></span>

<span data-ttu-id="5b227-109">Use o Web.SDK em um projeto:</span><span class="sxs-lookup"><span data-stu-id="5b227-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="5b227-110">Recursos habilitados usando o Web SDK:</span><span class="sxs-lookup"><span data-stu-id="5b227-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="5b227-111">Projetos direcionados ao .NET Core 3.0 ou posteriormente implicitamente referenciam:</span><span class="sxs-lookup"><span data-stu-id="5b227-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="5b227-112">A [estrutura compartilhada do Núcleo ASP.NET](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5b227-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="5b227-113">[Analisadores projetados](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) para construir aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b227-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="5b227-114">O Web SDK importa metas MSBuild que permitem o uso de perfis de publicação e publicação usando o WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="5b227-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="5b227-115">Propriedades</span><span class="sxs-lookup"><span data-stu-id="5b227-115">Properties</span></span>

| <span data-ttu-id="5b227-116">Propriedade</span><span class="sxs-lookup"><span data-stu-id="5b227-116">Property</span></span> | <span data-ttu-id="5b227-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="5b227-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="5b227-118">Desativa a `Microsoft.AspNetCore.App` referência implícita à estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="5b227-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="5b227-119">Desativa a referência implícita aos analisadores ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b227-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="5b227-120">Desativa a referência implícita aos analisadores de componentes de barbear ao criar Blazor aplicativos (servidor).</span><span class="sxs-lookup"><span data-stu-id="5b227-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
