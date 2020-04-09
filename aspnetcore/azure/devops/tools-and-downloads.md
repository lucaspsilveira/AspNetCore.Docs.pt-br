---
title: Ferramentas e downloads - DevOps com ASP.NET Core e Azure
author: CamSoper
description: Ferramentas e downloads necessários para DevOps com ASP.NET Core e Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511139"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="54da2-103">Ferramentas e downloads</span><span class="sxs-lookup"><span data-stu-id="54da2-103">Tools and downloads</span></span>

<span data-ttu-id="54da2-104">O Azure possui diversas interfaces para provisionamento e gerenciamento de recursos, como o [portal Azure](https://portal.azure.com), [Azure CLI,](/cli/azure/) [Azure PowerShell,](/powershell/azure/overview) [Azure Cloud Shell](https://shell.azure.com/bash)e Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="54da2-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="54da2-105">Este guia adota uma abordagem minimalista e usa o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias.</span><span class="sxs-lookup"><span data-stu-id="54da2-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="54da2-106">No entanto, o portal Azure deve ser usado para algumas porções.</span><span class="sxs-lookup"><span data-stu-id="54da2-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54da2-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54da2-107">Prerequisites</span></span>

<span data-ttu-id="54da2-108">As seguintes assinaturas são necessárias:</span><span class="sxs-lookup"><span data-stu-id="54da2-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="54da2-109">Azure &mdash; Se você não tem uma conta, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="54da2-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="54da2-110">Azure DevOps &mdash; Services sua assinatura e organização Do Azure DevOps é criada no Capítulo 4.</span><span class="sxs-lookup"><span data-stu-id="54da2-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="54da2-111">GitHub &mdash; Se você não tiver uma conta, [inscreva-se gratuitamente](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="54da2-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="54da2-112">As seguintes ferramentas são necessárias:</span><span class="sxs-lookup"><span data-stu-id="54da2-112">The following tools are required:</span></span>

* <span data-ttu-id="54da2-113">[Git](https://git-scm.com/downloads) &mdash; Uma compreensão fundamental de Git é recomendada para este guia.</span><span class="sxs-lookup"><span data-stu-id="54da2-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="54da2-114">Revise a documentação do [Git,](https://git-scm.com/doc)especificamente [git remote](https://git-scm.com/docs/git-remote) e [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="54da2-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="54da2-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Versão 2.1.300 ou posterior é necessária para construir e executar o aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="54da2-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="54da2-116">Se o Visual Studio estiver instalado com a carga **de trabalho de desenvolvimento multiplataforma .NET Core,** o .NET Core SDK já está instalado.</span><span class="sxs-lookup"><span data-stu-id="54da2-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="54da2-117">Verifique a instalação do .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="54da2-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="54da2-118">Abra uma camada de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="54da2-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="54da2-119">Ferramentas recomendadas (somente Windows)</span><span class="sxs-lookup"><span data-stu-id="54da2-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="54da2-120">[As](https://visualstudio.microsoft.com)ferramentas robustas do Azure do Visual Studio fornecem uma GUI para a maior parte das funcionalidades descritas neste guia.</span><span class="sxs-lookup"><span data-stu-id="54da2-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="54da2-121">Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito.</span><span class="sxs-lookup"><span data-stu-id="54da2-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="54da2-122">Os tutoriais são escritos para demonstrar desenvolvimento, implantação e DevOps com e sem visual studio.</span><span class="sxs-lookup"><span data-stu-id="54da2-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="54da2-123">Confirme se o Visual Studio tem as [seguintes cargas de trabalho instaladas:](/visualstudio/install/modify-visual-studio)</span><span class="sxs-lookup"><span data-stu-id="54da2-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="54da2-124">Desenvolvimento Web e ASP.NET</span><span class="sxs-lookup"><span data-stu-id="54da2-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="54da2-125">Desenvolvimento do Azure</span><span class="sxs-lookup"><span data-stu-id="54da2-125">Azure development</span></span>
  * <span data-ttu-id="54da2-126">Desenvolvimento multiplataforma com o .NET Core</span><span class="sxs-lookup"><span data-stu-id="54da2-126">.NET Core cross-platform development</span></span>
