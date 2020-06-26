---
title: Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais
author: rick-anderson
description: Descubra artigos com base em projetos ASP.NET Core criados com contas de usuário individuais.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6d3743fc31c29bed5075ac29381aea51a64a908c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406245"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais

ASP.NET Core Identity está incluído nos modelos de projeto no Visual Studio com a opção "contas de usuário individuais".

Os modelos de autenticação estão disponíveis no CLI do .NET Core com `-au Individual` :

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5833) para autenticação da API Web.

<a name="no"></a>

## <a name="no-authentication"></a>Sem Autenticação

A autenticação é especificada no CLI do .NET Core com a `-au` opção. No Visual Studio, a caixa de diálogo **alterar autenticação** está disponível para novos aplicativos Web. O padrão para novos aplicativos Web no Visual Studio **não é autenticação**.

Projetos criados sem autenticação:

* Não conte páginas da Web e interface do usuário para entrar e sair.
* Não contêm código de autenticação.

<a name="win"></a>

## <a name="windows-authentication"></a>Autenticação do Windows

A autenticação do Windows é especificada para novos aplicativos Web no CLI do .NET Core com a `-au Windows` opção. No Visual Studio, a caixa de diálogo **alterar autenticação** fornece as opções de **autenticação do Windows** .

Se a autenticação do Windows for selecionada, o aplicativo será configurado para usar o [módulo IIS de autenticação do Windows](xref:host-and-deploy/iis/modules). A autenticação do Windows destina-se a sites da intranet.

## <a name="dotnet-new-webapp-authentication-options"></a>novas opções de autenticação de webapp do dotnet

A tabela a seguir mostra as opções de autenticação disponíveis para novos aplicativos Web:

| Opção | Tipo de autenticação | Link para mais informações |
 | ----------------- | ------------ | ---------- |
| Nenhum            |  Sem autenticação | | 
| Individual      |  Autenticação individual | <xref:security/authentication/identity>
| IndividualB2C   |  Autenticação individual hospedada na nuvem com o Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  Autenticação organizacional para um único locatário | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  Autenticação organizacional para vários locatários | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Autenticação do Windows | [Autenticação do Windows](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Novas opções de autenticação webapp do Visual Studio

A tabela a seguir mostra as opções de autenticação disponíveis ao criar um novo aplicativo Web com o Visual Studio:

| Opção | Tipo de autenticação | Link para mais informações |
 | ----------------- | ------------ | ---------- |
| Nenhum            |  Sem autenticação | | 
| Contas de usuário individuais/armazenar contas de usuário no aplicativo |  Autenticação individual | <xref:security/authentication/identity> |
| Contas de usuário individuais/Conecte-se a um armazenamento de usuário existente na nuvem |  Autenticação individual hospedada na nuvem com o Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Nuvem corporativa ou de estudante/organização única  |  Autenticação organizacional para um único locatário | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Nuvem corporativa ou de estudante/várias org |  Autenticação organizacional para vários locatários | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Autenticação do Windows | [Autenticação do Windows](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Recursos adicionais

Os artigos a seguir mostram como usar o código gerado em ASP.NET Core modelos que usam contas de usuário individuais:

* [Confirmação de conta e de recuperação de senha no ASP.NET Core](xref:security/authentication/accconfirm)
* [Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização](xref:security/authorization/secure-data)
