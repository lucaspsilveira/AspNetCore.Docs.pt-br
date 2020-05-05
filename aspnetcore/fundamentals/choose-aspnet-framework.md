---
title: Escolher entre o ASP.NET 4.x e o ASP.NET Core
author: rick-anderson
description: Explica ASP.NET Core vs. ASP.NET 4. x e como escolher entre eles.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 1fb81d5a54cf332ca473af8fbe1841813a127be7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775876"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Escolher entre o ASP.NET 4.x e o ASP.NET Core

O ASP.NET Core é uma reformulação do ASP.NET 4. x. Este artigo lista as diferenças entre eles.

## <a name="aspnet-core"></a>ASP.NET Core

O ASP.NET Core é uma estrutura de software livre, multiplataforma, para a criação de aplicativos Web modernos e baseados em nuvem, no Windows, no macOS ou no Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

O ASP.NET 4.x é uma estrutura consolidada que fornece os serviços necessários para criar aplicativos Web baseados em servidor, de nível empresarial, no Windows.

## <a name="framework-selection"></a>Seleção de estrutura

A tabela a seguir compara o ASP.NET Core com o ASP.NET 4. x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Build para Windows, macOS ou Linux|Build para Windows|
|Páginas é a abordagem recomendada para criar uma interface do usuário da Web a partir do ASP.NET Core 2. x. [ Razor ](xref:razor-pages/index) Consulte também [MVC](xref:mvc/overview), [API da Web](xref:tutorials/first-web-api)e [SignalR](xref:signalr/introduction).|Usar [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [API da Web](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/)ou [páginas da Web](/aspnet/web-pages)|
|Várias versões por computador|Uma versão por computador|
|Desenvolva com o [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) ou [Visual Studio Code](https://code.visualstudio.com/) usando C# ou F#|Desenvolva com o [Visual Studio](https://visualstudio.microsoft.com/vs/) usando C#, vb ou F #|
|Desempenho superior ao do ASP.NET 4.x|Bom desempenho|
|[Usar o runtime do .NET Core](/dotnet/standard/choosing-core-framework-server)|Use o runtime do .NET Framework|

Confira [ASP.NET Core targeting .NET Framework](xref:index#target-framework) (ASP.NET Core direcionado para o .NET Framework) para obter informações sobre o suporte do ASP.NET Core 2.x no .NET Framework.

## <a name="aspnet-core-scenarios"></a>Cenários do ASP.NET Core

* [Sites](xref:tutorials/first-mvc-app/index)
* [APIs](xref:tutorials/first-web-api)
* [Tempo real](xref:signalr/introduction)
* [Implantar um aplicativo do ASP.NET Core no Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>Cenários do ASP.NET 4.x

* [Sites](/aspnet/mvc)
* [APIs](/aspnet/web-api)
* [Tempo real](/aspnet/signalr)
* [Criar um aplicativo Web ASP.NET 4.x no Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao ASP.NET](/aspnet/overview)
* [Introdução ao ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
