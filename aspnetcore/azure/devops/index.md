---
title: DevOps com ASP.NET Core e Azure
author: CamSoper
description: Um guia que fornece orientação de ponta a ponta sobre a criação de um pipeline de DevOps para um aplicativo ASP.NET Core hospedado no Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/index
ms.openlocfilehash: da9d5365b6e3920dbf672f65d5fab4ea158932c2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766895"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="c5b77-103">DevOps com ASP.NET Core e Azure</span><span class="sxs-lookup"><span data-stu-id="c5b77-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="c5b77-104">[![Imagem de capa](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="c5b77-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="c5b77-105">Por [Cam Soper](https://twitter.com/camsoper) e [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c5b77-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="c5b77-106">Este guia está disponível como um [e-book em PDF para download](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="c5b77-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="c5b77-107">Bem-Vindo</span><span class="sxs-lookup"><span data-stu-id="c5b77-107">Welcome</span></span> 

<span data-ttu-id="c5b77-108">Bem-vindo ao guia de ciclo de vida de desenvolvimento do Azure para .NET.</span><span class="sxs-lookup"><span data-stu-id="c5b77-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="c5b77-109">Este guia apresenta os conceitos básicos da criação de um ciclo de vida de desenvolvimento para o Azure usando processos e ferramentas do .NET.</span><span class="sxs-lookup"><span data-stu-id="c5b77-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="c5b77-110">Depois de concluí-lo, você aproveitará os benefícios de uma cadeia de ferramentas madura do DevOps.</span><span class="sxs-lookup"><span data-stu-id="c5b77-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="c5b77-111">A quem esse guia se destina</span><span class="sxs-lookup"><span data-stu-id="c5b77-111">Who this guide is for</span></span>

<span data-ttu-id="c5b77-112">Você deve ser um desenvolvedor experiente do ASP.NET Core (nível 200 a 300).</span><span class="sxs-lookup"><span data-stu-id="c5b77-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="c5b77-113">Você não precisa saber nada sobre o Azure, uma vez que abordaremos isso nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="c5b77-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="c5b77-114">Esse guia também pode ser útil para engenheiros de DevOps que estão mais focados nas operações do que no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c5b77-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="c5b77-115">Esse guia destina-se aos desenvolvedores do Windows.</span><span class="sxs-lookup"><span data-stu-id="c5b77-115">This guide targets Windows developers.</span></span> <span data-ttu-id="c5b77-116">No entanto, Linux e macOS são totalmente compatíveis com .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5b77-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="c5b77-117">Para adaptar esse guia para Linux/macOS, fique atento a textos explicativos para diferenças do Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="c5b77-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="c5b77-118">O que este guia não aborda</span><span class="sxs-lookup"><span data-stu-id="c5b77-118">What this guide doesn't cover</span></span>

<span data-ttu-id="c5b77-119">Esse guia se concentra em uma experiência de implantação contínua de ponta a ponta para desenvolvedores do .NET.</span><span class="sxs-lookup"><span data-stu-id="c5b77-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="c5b77-120">Ele não é um guia completo para tudo relacionado ao Azure e não se concentra extensivamente em APIs .NET para serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="c5b77-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="c5b77-121">A ênfase está totalmente concentrada na integração contínua, na implantação, no monitoramento e na depuração.</span><span class="sxs-lookup"><span data-stu-id="c5b77-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="c5b77-122">No final do guia, são oferecidas recomendações para as próximas etapas.</span><span class="sxs-lookup"><span data-stu-id="c5b77-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="c5b77-123">Serviços da plataforma do Azure que úteis para desenvolvedores do ASP.NET Core estão incluídos nas sugestões.</span><span class="sxs-lookup"><span data-stu-id="c5b77-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="c5b77-124">O que há neste guia</span><span class="sxs-lookup"><span data-stu-id="c5b77-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="c5b77-125">Ferramentas e downloads</span><span class="sxs-lookup"><span data-stu-id="c5b77-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="c5b77-126">Saiba onde obter as ferramentas usadas neste guia.</span><span class="sxs-lookup"><span data-stu-id="c5b77-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="c5b77-127">Implantar no Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="c5b77-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="c5b77-128">Aprenda os vários métodos para implantar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="c5b77-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="c5b77-129">Integração e implantação contínuas</span><span class="sxs-lookup"><span data-stu-id="c5b77-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="c5b77-130">Crie uma solução de implantação e integração contínua de ponta a ponta para seu aplicativo ASP.NET Core com o GitHub, o Azure DevOps Services e o Azure.</span><span class="sxs-lookup"><span data-stu-id="c5b77-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="c5b77-131">Monitorar e depurar</span><span class="sxs-lookup"><span data-stu-id="c5b77-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="c5b77-132">Use as ferramentas do Azure para monitorar, solucionar problemas e ajustar seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5b77-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="c5b77-133">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c5b77-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="c5b77-134">Outros roteiros de aprendizado para desenvolvedores do ASP.NET Core que estão aprendendo sobre o Azure.</span><span class="sxs-lookup"><span data-stu-id="c5b77-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="c5b77-135">Leitura adicional de introdução</span><span class="sxs-lookup"><span data-stu-id="c5b77-135">Additional introductory reading</span></span>

<span data-ttu-id="c5b77-136">Se esta é sua primeira experiência com a computação em nuvem, estes artigos explicam os conceitos básicos.</span><span class="sxs-lookup"><span data-stu-id="c5b77-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="c5b77-137">O que é a computação em nuvem?</span><span class="sxs-lookup"><span data-stu-id="c5b77-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="c5b77-138">Exemplos de computação em nuvem</span><span class="sxs-lookup"><span data-stu-id="c5b77-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="c5b77-139">O que é IaaS?</span><span class="sxs-lookup"><span data-stu-id="c5b77-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="c5b77-140">O que é PaaS?</span><span class="sxs-lookup"><span data-stu-id="c5b77-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
