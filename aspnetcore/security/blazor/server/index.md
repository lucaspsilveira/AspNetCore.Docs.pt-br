---
title: Proteger aplicativos Blazor do ASP.NET Core Server
author: guardrex
description: Saiba como proteger Blazor aplicativos de servidor como ASP.NET Core aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206364"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="7f2f8-103">Proteger os aplicativos de servidor do mais ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f2f8-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="7f2f8-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7f2f8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="7f2f8-105">Modelo de projeto de servidor mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="7f2f8-105">Blazor Server project template</span></span>

<span data-ttu-id="7f2f8-106">O modelo de projeto de servidor mais incrivelmente pode ser configurado para autenticação quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f2f8-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f2f8-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f2f8-108">Siga as diretrizes do Visual Studio no <xref:blazor/get-started> artigo para criar um novo projeto de servidor com um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="7f2f8-109">Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="7f2f8-110">Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="7f2f8-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="7f2f8-111">**Sem autenticação**</span><span class="sxs-lookup"><span data-stu-id="7f2f8-111">**No Authentication**</span></span>
* <span data-ttu-id="7f2f8-112">**Contas de usuário individuais** &ndash; as contas de usuário podem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="7f2f8-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="7f2f8-113">Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="7f2f8-114">Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="7f2f8-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="7f2f8-115">**Contas corporativas ou de estudante**</span><span class="sxs-lookup"><span data-stu-id="7f2f8-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="7f2f8-116">**Autenticação do Windows**</span><span class="sxs-lookup"><span data-stu-id="7f2f8-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7f2f8-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f2f8-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f2f8-118">Siga as diretrizes de Visual Studio Code no <xref:blazor/get-started> artigo para criar um novo projeto de servidor com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="7f2f8-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="7f2f8-119">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="7f2f8-120">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="7f2f8-120">Authentication mechanism</span></span> | <span data-ttu-id="7f2f8-121">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f2f8-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="7f2f8-122">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="7f2f8-122">`None` (default)</span></span>         | <span data-ttu-id="7f2f8-123">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="7f2f8-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="7f2f8-124">Usuários armazenados no aplicativo com identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f2f8-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="7f2f8-125">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="7f2f8-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="7f2f8-126">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="7f2f8-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="7f2f8-127">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="7f2f8-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="7f2f8-128">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="7f2f8-128">Windows Authentication</span></span> |

<span data-ttu-id="7f2f8-129">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="7f2f8-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="7f2f8-130">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-130">Create a folder for the project.</span></span>
* <span data-ttu-id="7f2f8-131">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-131">Name the project.</span></span>

<span data-ttu-id="7f2f8-132">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f2f8-133">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7f2f8-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7f2f8-134">Siga as diretrizes de Visual Studio para Mac no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="7f2f8-135">Na etapa **configurar seu novo aplicativo de servidor de Nova** velocidade, selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="7f2f8-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="7f2f8-136">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7f2f8-137">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="7f2f8-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="7f2f8-138">Siga as diretrizes de CLI do .NET Core no <xref:blazor/get-started> artigo para criar um novo projeto de servidor com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="7f2f8-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="7f2f8-139">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="7f2f8-140">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="7f2f8-140">Authentication mechanism</span></span> | <span data-ttu-id="7f2f8-141">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f2f8-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="7f2f8-142">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="7f2f8-142">`None` (default)</span></span>         | <span data-ttu-id="7f2f8-143">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="7f2f8-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="7f2f8-144">Usuários armazenados no aplicativo com identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f2f8-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="7f2f8-145">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="7f2f8-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="7f2f8-146">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="7f2f8-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="7f2f8-147">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="7f2f8-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="7f2f8-148">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="7f2f8-148">Windows Authentication</span></span> |

<span data-ttu-id="7f2f8-149">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="7f2f8-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="7f2f8-150">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-150">Create a folder for the project.</span></span>
* <span data-ttu-id="7f2f8-151">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-151">Name the project.</span></span>

<span data-ttu-id="7f2f8-152">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f2f8-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---
