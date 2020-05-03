---
title: Proteger aplicativos Blazor do ASP.NET Core Server
author: guardrex
description: Saiba como proteger Blazor aplicativos de servidor como ASP.NET Core aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 324b3e8ed2d931c81bb41381caa469b0ec2f2fda
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727808"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="b37e0-103">Proteger os aplicativos de servidor do mais ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b37e0-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b37e0-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b37e0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="b37e0-105">Modelo de projeto de servidor mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="b37e0-105">Blazor Server project template</span></span>

<span data-ttu-id="b37e0-106">O modelo de projeto de servidor mais incrivelmente pode ser configurado para autenticação quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="b37e0-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b37e0-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b37e0-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b37e0-108">Siga as diretrizes do Visual Studio no <xref:blazor/get-started> artigo para criar um novo projeto de servidor com um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="b37e0-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b37e0-109">Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.</span><span class="sxs-lookup"><span data-stu-id="b37e0-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b37e0-110">Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b37e0-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b37e0-111">**Sem autenticação**</span><span class="sxs-lookup"><span data-stu-id="b37e0-111">**No Authentication**</span></span>
* <span data-ttu-id="b37e0-112">**Contas de usuário individuais** &ndash; as contas de usuário podem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="b37e0-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="b37e0-113">Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b37e0-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b37e0-114">Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="b37e0-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b37e0-115">**Contas corporativas ou de estudante**</span><span class="sxs-lookup"><span data-stu-id="b37e0-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="b37e0-116">**Autenticação do Windows**</span><span class="sxs-lookup"><span data-stu-id="b37e0-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b37e0-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b37e0-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b37e0-118">Siga as diretrizes de Visual Studio Code no <xref:blazor/get-started> artigo para criar um novo projeto de servidor com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="b37e0-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b37e0-119">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b37e0-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b37e0-120">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="b37e0-120">Authentication mechanism</span></span> | <span data-ttu-id="b37e0-121">Descrição</span><span class="sxs-lookup"><span data-stu-id="b37e0-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b37e0-122">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="b37e0-122">`None` (default)</span></span>         | <span data-ttu-id="b37e0-123">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="b37e0-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b37e0-124">Usuários armazenados no aplicativo com identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b37e0-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b37e0-125">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b37e0-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b37e0-126">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="b37e0-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b37e0-127">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="b37e0-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b37e0-128">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="b37e0-128">Windows Authentication</span></span> |

<span data-ttu-id="b37e0-129">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="b37e0-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b37e0-130">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="b37e0-130">Create a folder for the project.</span></span>
* <span data-ttu-id="b37e0-131">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="b37e0-131">Name the project.</span></span>

<span data-ttu-id="b37e0-132">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b37e0-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b37e0-133">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b37e0-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b37e0-134">Siga as diretrizes de Visual Studio para Mac no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="b37e0-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="b37e0-135">Na etapa **configurar seu novo aplicativo de servidor de Nova** velocidade, selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="b37e0-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b37e0-136">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="b37e0-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b37e0-137">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b37e0-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b37e0-138">Siga as diretrizes de CLI do .NET Core no <xref:blazor/get-started> artigo para criar um novo projeto de servidor com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="b37e0-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b37e0-139">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b37e0-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b37e0-140">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="b37e0-140">Authentication mechanism</span></span> | <span data-ttu-id="b37e0-141">Descrição</span><span class="sxs-lookup"><span data-stu-id="b37e0-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b37e0-142">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="b37e0-142">`None` (default)</span></span>         | <span data-ttu-id="b37e0-143">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="b37e0-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b37e0-144">Usuários armazenados no aplicativo com identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b37e0-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b37e0-145">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b37e0-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b37e0-146">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="b37e0-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b37e0-147">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="b37e0-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b37e0-148">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="b37e0-148">Windows Authentication</span></span> |

<span data-ttu-id="b37e0-149">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="b37e0-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b37e0-150">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="b37e0-150">Create a folder for the project.</span></span>
* <span data-ttu-id="b37e0-151">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="b37e0-151">Name the project.</span></span>

<span data-ttu-id="b37e0-152">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b37e0-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="b37e0-153">Proteger um aplicativo existente</span><span class="sxs-lookup"><span data-stu-id="b37e0-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="b37e0-154">Os aplicativos de servidor são configurados para segurança da mesma maneira que ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b37e0-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="b37e0-155">Para obter mais informações, consulte os artigos <xref:security/index>em.</span><span class="sxs-lookup"><span data-stu-id="b37e0-155">For more information, see the articles under <xref:security/index>.</span></span>
