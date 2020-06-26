---
title: Ferramentas e downloads-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Ferramentas e downloads necessários para o DevOps com o ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ed8aee214ff9b9e941aeea01887882c3bdfc56a7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400291"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="ff648-103">Ferramentas e downloads</span><span class="sxs-lookup"><span data-stu-id="ff648-103">Tools and downloads</span></span>

<span data-ttu-id="ff648-104">O Azure tem várias interfaces para provisionar e gerenciar recursos, como o [portal do Azure](https://portal.azure.com), [CLI do Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure cloud Shell](https://shell.azure.com/bash)e Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ff648-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="ff648-105">Este guia usa uma abordagem de solução de dutos e utiliza o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias.</span><span class="sxs-lookup"><span data-stu-id="ff648-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="ff648-106">No entanto, o portal do Azure deve ser usado para algumas partes.</span><span class="sxs-lookup"><span data-stu-id="ff648-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff648-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ff648-107">Prerequisites</span></span>

<span data-ttu-id="ff648-108">As seguintes assinaturas são necessárias:</span><span class="sxs-lookup"><span data-stu-id="ff648-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="ff648-109">Azure &mdash; se você não tiver uma conta, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="ff648-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="ff648-110">Azure DevOps Services &mdash; sua assinatura e organização do DevOps do Azure é criada no capítulo 4.</span><span class="sxs-lookup"><span data-stu-id="ff648-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="ff648-111">GitHub &mdash; se você não tiver uma conta, [Inscreva-se gratuitamente](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="ff648-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="ff648-112">As seguintes ferramentas são necessárias:</span><span class="sxs-lookup"><span data-stu-id="ff648-112">The following tools are required:</span></span>

* <span data-ttu-id="ff648-113">[Git](https://git-scm.com/downloads) &mdash; Uma compreensão fundamental do git é recomendada para este guia.</span><span class="sxs-lookup"><span data-stu-id="ff648-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="ff648-114">Examine a [documentação do git](https://git-scm.com/doc), especificamente [git remoto](https://git-scm.com/docs/git-remote) e [git Push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="ff648-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="ff648-115">[SDK do .NET Core](https://dotnet.microsoft.com/download/) &mdash; A versão 2.1.300 ou posterior é necessária para compilar e executar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ff648-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="ff648-116">Se o Visual Studio estiver instalado com a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** , o SDK do .NET Core já estará instalado.</span><span class="sxs-lookup"><span data-stu-id="ff648-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="ff648-117">Verifique a instalação do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff648-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="ff648-118">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff648-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="ff648-119">Ferramentas recomendadas (somente Windows)</span><span class="sxs-lookup"><span data-stu-id="ff648-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="ff648-120">As ferramentas robustas do Azure do [Visual Studio](https://visualstudio.microsoft.com)fornecem uma GUI para a maioria das funcionalidades descritas neste guia.</span><span class="sxs-lookup"><span data-stu-id="ff648-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="ff648-121">Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito.</span><span class="sxs-lookup"><span data-stu-id="ff648-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="ff648-122">Os tutoriais são escritos para demonstrar desenvolvimento, implantação e DevOps com e sem o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ff648-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="ff648-123">Confirme se o Visual Studio tem as seguintes [cargas de trabalho](/visualstudio/install/modify-visual-studio) instaladas:</span><span class="sxs-lookup"><span data-stu-id="ff648-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="ff648-124">Desenvolvimento Web e ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ff648-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="ff648-125">Desenvolvimento do Azure</span><span class="sxs-lookup"><span data-stu-id="ff648-125">Azure development</span></span>
  * <span data-ttu-id="ff648-126">Desenvolvimento multiplataforma com o .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff648-126">.NET Core cross-platform development</span></span>
