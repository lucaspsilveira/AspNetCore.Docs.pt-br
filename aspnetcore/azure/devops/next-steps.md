---
title: Próximos passos - DevOps com ASP.NET Core e Azure
author: CamSoper
description: Caminhos adicionais de aprendizado para DevOps com ASP.NET Core e Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659470"
---
# <a name="next-steps"></a><span data-ttu-id="8d7af-103">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="8d7af-103">Next steps</span></span>

<span data-ttu-id="8d7af-104">Neste guia, você criou um pipeline DevOps para um aplicativo de amostra ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8d7af-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="8d7af-105">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="8d7af-105">Congratulations!</span></span> <span data-ttu-id="8d7af-106">Esperamos que você tenha gostado de aprender a publicar ASP.NET aplicativos web Core no Azure App Service e automatizar a integração contínua das mudanças.</span><span class="sxs-lookup"><span data-stu-id="8d7af-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="8d7af-107">Além de hospedagem web e DevOps, o Azure tem uma ampla gama de serviços Desmembrantes (PaaS) de plataforma como serviço útil para ASP.NET desenvolvedores Core.</span><span class="sxs-lookup"><span data-stu-id="8d7af-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="8d7af-108">Esta seção fornece uma breve visão geral de alguns dos serviços mais usados.</span><span class="sxs-lookup"><span data-stu-id="8d7af-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="8d7af-109">Armazenamento e bancos de dados</span><span class="sxs-lookup"><span data-stu-id="8d7af-109">Storage and databases</span></span>

<span data-ttu-id="8d7af-110">[O Redis Cache](/azure/redis-cache/) é um cache de dados de baixa latência disponível como serviço.</span><span class="sxs-lookup"><span data-stu-id="8d7af-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="8d7af-111">Ele pode ser usado para cacher a saída da página, reduzir as solicitações de banco de dados e fornecer ASP.NET estado de sessão Core em várias instâncias de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8d7af-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="8d7af-112">[O Azure Storage](/azure/storage/) é o armazenamento em nuvem maciçamente escalável do Azure.</span><span class="sxs-lookup"><span data-stu-id="8d7af-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="8d7af-113">Os desenvolvedores podem aproveitar o [armazenamento na fila](/azure/storage/queues/storage-queues-introduction) para uma fila de mensagens confiável, e o Table [Storage](/azure/storage/tables/table-storage-overview) é uma loja de valor-chave NoSQL projetada para desenvolvimento rápido usando conjuntos de dados maciços e semiestruturados.</span><span class="sxs-lookup"><span data-stu-id="8d7af-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="8d7af-114">[O Azure SQL Database](/azure/sql-database/) fornece funcionalidade de banco de dados relacional familiar como um serviço usando o Microsoft SQL Server Engine.</span><span class="sxs-lookup"><span data-stu-id="8d7af-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="8d7af-115">[Cosmos DB](/azure/cosmos-db/) serviço de banco de dados NoSQL distribuído globalmente e multimodelo.</span><span class="sxs-lookup"><span data-stu-id="8d7af-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="8d7af-116">Várias APIs estão disponíveis, incluindo API SQL (anteriormente chamada DocumentDB), Cassandra e MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8d7af-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="8d7af-117">Identidade</span><span class="sxs-lookup"><span data-stu-id="8d7af-117">Identity</span></span>

<span data-ttu-id="8d7af-118">[O Azure Active Directory](/azure/active-directory/) e [o Azure Active Directory B2C](/azure/active-directory-b2c/) são serviços de identidade.</span><span class="sxs-lookup"><span data-stu-id="8d7af-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="8d7af-119">O Azure Active Directory foi projetado para cenários corporativos e permite a colaboração do Azure AD B2B (business-to-business), enquanto o Azure Active Directory B2C destina-se a cenários de negócios para clientes, incluindo o login em rede social.</span><span class="sxs-lookup"><span data-stu-id="8d7af-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="8d7af-120">Celular</span><span class="sxs-lookup"><span data-stu-id="8d7af-120">Mobile</span></span>

<span data-ttu-id="8d7af-121">[O Notification Hubs](/azure/notification-hubs/) é um mecanismo de notificação de push escalável e multiplataforma para enviar rapidamente milhões de mensagens para aplicativos em execução em vários tipos de dispositivos.</span><span class="sxs-lookup"><span data-stu-id="8d7af-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="8d7af-122">Infraestrutura web</span><span class="sxs-lookup"><span data-stu-id="8d7af-122">Web infrastructure</span></span>

<span data-ttu-id="8d7af-123">[O Azure Container Service](/azure/aks/) gerencia seu ambiente Kubernetes hospedado, tornando-o rápido e fácil de implantar e gerenciar aplicativos contêiners sem experiência em orquestração de contêineres.</span><span class="sxs-lookup"><span data-stu-id="8d7af-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="8d7af-124">[O Azure Search](/azure/search/) é usado para criar uma solução de pesquisa corporativa sobre conteúdo privado e heterogêneo.</span><span class="sxs-lookup"><span data-stu-id="8d7af-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="8d7af-125">[Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microserviços e contêineres escaláveis e confiáveis.</span><span class="sxs-lookup"><span data-stu-id="8d7af-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
