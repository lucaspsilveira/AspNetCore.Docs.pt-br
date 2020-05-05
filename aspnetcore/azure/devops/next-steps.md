---
title: Próximas etapas-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Outros caminhos de aprendizagem para DevOps com o ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/next-steps
ms.openlocfilehash: 92401d45d36dd3b93d175e08a8fa8697217ca7c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766518"
---
# <a name="next-steps"></a>Próximas etapas

Neste guia, você criou um pipeline do DevOps para um aplicativo de exemplo ASP.NET Core. Parabéns! Esperamos que você tenha gostado de aprender a publicar ASP.NET Core aplicativos Web para Azure App serviço e automatizar a integração contínua das alterações.

Além de hospedagem na Web e DevOps, o Azure tem uma ampla gama de serviços de PaaS (plataforma como serviço) úteis para ASP.NET Core desenvolvedores. Esta seção fornece uma breve visão geral de alguns dos serviços mais usados.

## <a name="storage-and-databases"></a>Armazenamento e bancos de dados

O [cache Redis](/azure/redis-cache/) é um cache de dados de baixa latência e alta taxa de transferência disponível como um serviço. Ele pode ser usado para cache de saída de página, reduzindo solicitações de banco de dados e fornecendo ASP.NET Core estado de sessão em várias instâncias de um aplicativo.

O [armazenamento do Azure](/azure/storage/) é um armazenamento em nuvem altamente escalonável do Azure. Os desenvolvedores podem aproveitar o [armazenamento de filas](/azure/storage/queues/storage-queues-introduction) para o serviço de enfileiramento de mensagens confiável, e o [armazenamento de tabelas](/azure/storage/tables/table-storage-overview) é um repositório de chave-valor NoSQL projetado para um rápido desenvolvimento usando conjuntos de dados maciços e semiestruturados.

O [banco de dados SQL do Azure](/azure/sql-database/) fornece funcionalidade familiar de banco de dados relacional como um serviço usando o mecanismo de Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) serviço de banco de dados NoSQL de vários modelos distribuído globalmente. Várias APIs estão disponíveis, incluindo a API do SQL (anteriormente chamada de DocumentDB), Cassandra e MongoDB.

## Identity

[Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) são serviços de identidade. A Azure Active Directory é projetada para cenários empresariais e habilita a colaboração B2B (entre empresas) do Azure AD, enquanto Azure Active Directory B2C se destina a cenários entre clientes, incluindo a entrada de rede social.

## <a name="mobile"></a>Celular

Os [hubs de notificação](/azure/notification-hubs/) são um mecanismo de notificação por push de várias plataformas e escalonáveis para enviar rapidamente milhões de mensagens para aplicativos em execução em vários tipos de dispositivos.

## <a name="web-infrastructure"></a>Infraestrutura da Web

O [serviço de contêiner do Azure](/azure/aks/) gerencia seu ambiente kubernetes hospedado, tornando rápido e fácil implantar e gerenciar aplicativos em contêineres sem conhecimento de orquestração de contêiner.

[Azure Search](/azure/search/) é usado para criar uma solução de pesquisa empresarial sobre conteúdo privado e heterogêneo.

O [Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos que torna mais fácil empacotar, implantar e gerenciar microserviços e contêineres escalonáveis e confiáveis.
