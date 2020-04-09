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
# <a name="next-steps"></a>Próximas etapas

Neste guia, você criou um pipeline DevOps para um aplicativo de amostra ASP.NET Core. Parabéns! Esperamos que você tenha gostado de aprender a publicar ASP.NET aplicativos web Core no Azure App Service e automatizar a integração contínua das mudanças.

Além de hospedagem web e DevOps, o Azure tem uma ampla gama de serviços Desmembrantes (PaaS) de plataforma como serviço útil para ASP.NET desenvolvedores Core. Esta seção fornece uma breve visão geral de alguns dos serviços mais usados.

## <a name="storage-and-databases"></a>Armazenamento e bancos de dados

[O Redis Cache](/azure/redis-cache/) é um cache de dados de baixa latência disponível como serviço. Ele pode ser usado para cacher a saída da página, reduzir as solicitações de banco de dados e fornecer ASP.NET estado de sessão Core em várias instâncias de um aplicativo.

[O Azure Storage](/azure/storage/) é o armazenamento em nuvem maciçamente escalável do Azure. Os desenvolvedores podem aproveitar o [armazenamento na fila](/azure/storage/queues/storage-queues-introduction) para uma fila de mensagens confiável, e o Table [Storage](/azure/storage/tables/table-storage-overview) é uma loja de valor-chave NoSQL projetada para desenvolvimento rápido usando conjuntos de dados maciços e semiestruturados.

[O Azure SQL Database](/azure/sql-database/) fornece funcionalidade de banco de dados relacional familiar como um serviço usando o Microsoft SQL Server Engine.

[Cosmos DB](/azure/cosmos-db/) serviço de banco de dados NoSQL distribuído globalmente e multimodelo. Várias APIs estão disponíveis, incluindo API SQL (anteriormente chamada DocumentDB), Cassandra e MongoDB.

## <a name="identity"></a>Identidade

[O Azure Active Directory](/azure/active-directory/) e [o Azure Active Directory B2C](/azure/active-directory-b2c/) são serviços de identidade. O Azure Active Directory foi projetado para cenários corporativos e permite a colaboração do Azure AD B2B (business-to-business), enquanto o Azure Active Directory B2C destina-se a cenários de negócios para clientes, incluindo o login em rede social.

## <a name="mobile"></a>Celular

[O Notification Hubs](/azure/notification-hubs/) é um mecanismo de notificação de push escalável e multiplataforma para enviar rapidamente milhões de mensagens para aplicativos em execução em vários tipos de dispositivos.

## <a name="web-infrastructure"></a>Infraestrutura web

[O Azure Container Service](/azure/aks/) gerencia seu ambiente Kubernetes hospedado, tornando-o rápido e fácil de implantar e gerenciar aplicativos contêiners sem experiência em orquestração de contêineres.

[O Azure Search](/azure/search/) é usado para criar uma solução de pesquisa corporativa sobre conteúdo privado e heterogêneo.

[Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microserviços e contêineres escaláveis e confiáveis.
