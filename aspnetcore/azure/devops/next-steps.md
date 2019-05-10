---
title: Próximas etapas - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Adicionais de caminhos de aprendizado de DevOps com o ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892003"
---
# <a name="next-steps"></a>Próximas etapas

Neste guia, você criou um pipeline de DevOps para um aplicativo de exemplo do ASP.NET Core. Parabéns! Esperamos que você tenha gostado de aprendizado publicar aplicativos web ASP.NET Core no serviço de aplicativo do Azure e automatizar a integração contínua das alterações.

Além de hospedagem na web e DevOps, o Azure tem uma ampla gama de serviços de plataforma-como um serviço (PaaS) útil para desenvolvedores do ASP.NET Core. Esta seção fornece uma visão geral de alguns dos serviços mais comumente usados.

## <a name="storage-and-databases"></a>Armazenamento e bancos de dados

[Cache redis](/azure/redis-cache/) está disponível como um serviço de cache de dados de alta taxa de transferência e baixa latência. Ele pode ser usado para armazenamento em cache de saída de página, reduzindo as solicitações de banco de dados e fornecendo o estado de sessão do ASP.NET Core em várias instâncias de um aplicativo.

[O armazenamento do Azure](/azure/storage/) é armazenamento em nuvem altamente escalonável do Azure. Os desenvolvedores podem aproveitar [armazenamento de filas](/azure/storage/queues/storage-queues-introduction) para enfileiramento de mensagens confiável, e [armazenamento de tabelas](/azure/storage/tables/table-storage-overview) é um repositório de chave-valor NoSQL projetado para rápido desenvolvimento usando conjuntos de dados grandes e semi-estruturados.

[Banco de dados SQL do Azure](/azure/sql-database/) fornece a funcionalidade de banco de dados relacional conhecida como um serviço usando o mecanismo do Microsoft SQL Server.

[O cosmos DB](/azure/cosmos-db/) serviço de banco de dados NoSQL multimodelo, distribuído globalmente. Várias APIs estão disponíveis, incluindo MongoDB, Cassandra e API do SQL (anteriormente chamado de DocumentDB).

## <a name="identity"></a>Identidade

[O Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) são ambos os serviços de identidade. O Azure Active Directory foi projetado para cenários empresariais e permite a colaboração do Azure AD B2B (business-to-business), enquanto o Azure Active Directory B2C é pretendidos cenários de negócios para o cliente, incluindo rede social entrar.

## <a name="mobile"></a>Celular

[Os Hubs de notificação](/azure/notification-hubs/) é um mecanismo de notificação por push multiplataforma e dimensionável para enviar rapidamente milhões de mensagens para aplicativos em execução em vários tipos de dispositivos.

## <a name="web-infrastructure"></a>Infraestrutura da Web

[O serviço de contêiner do Azure](/azure/aks/) gerencia seu ambiente Kubernetes hospedado, tornando rápido e fácil de implantar e gerenciar aplicativos em contêineres sem conhecimento de orquestração de contêiner.

[O Azure Search](/azure/search/) é usado para criar uma solução de pesquisa empresarial sobre conteúdo privada e heterogênea.

[O Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos que torna mais fácil empacotar, implantar e gerenciar escalonável e confiáveis microsserviços e contêineres.
