---
title: Próximas etapas - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Adicionais de caminhos de aprendizado de DevOps com o ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659470"
---
# <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste guia, você criou um pipeline de DevOps para um aplicativo de exemplo do ASP.NET Core. Parabéns! Esperamos que você tenha gostado de aprendizado publicar aplicativos web ASP.NET Core no serviço de aplicativo do Azure e automatizar a integração contínua das alterações.

Além de hospedagem na web e DevOps, o Azure tem uma ampla gama de serviços de plataforma-como um serviço (PaaS) útil para desenvolvedores do ASP.NET Core. Esta seção fornece uma visão geral de alguns dos serviços mais comumente usados.

## <a name="storage-and-databases"></a>Armazenamento e bancos de dados

O [cache Redis](/azure/redis-cache/) é um cache de dados de baixa latência e alta taxa de transferência disponível como um serviço. Ele pode ser usado para armazenamento em cache de saída de página, reduzindo as solicitações de banco de dados e fornecendo o estado de sessão do ASP.NET Core em várias instâncias de um aplicativo.

O [armazenamento do Azure](/azure/storage/) é um armazenamento em nuvem altamente escalonável do Azure. Os desenvolvedores podem aproveitar o [armazenamento de filas](/azure/storage/queues/storage-queues-introduction) para o serviço de enfileiramento de mensagens confiável, e o [armazenamento de tabelas](/azure/storage/tables/table-storage-overview) é um repositório de chave-valor NoSQL projetado para um rápido desenvolvimento usando conjuntos de dados maciços e semiestruturados.

O [banco de dados SQL do Azure](/azure/sql-database/) fornece funcionalidade familiar de banco de dados relacional como um serviço usando o mecanismo de Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) serviço de banco de dados NoSQL de vários modelos distribuído globalmente. Várias APIs estão disponíveis, incluindo MongoDB, Cassandra e API do SQL (anteriormente chamado de DocumentDB).

## <a name="identity"></a>Identity

[Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) são serviços de identidade. O Azure Active Directory foi projetado para cenários empresariais e permite a colaboração do Azure AD B2B (business-to-business), enquanto o Azure Active Directory B2C é pretendidos cenários de negócios para o cliente, incluindo rede social entrar.

## <a name="mobile"></a>Mobilidade

Os [hubs de notificação](/azure/notification-hubs/) são um mecanismo de notificação por push de várias plataformas e escalonáveis para enviar rapidamente milhões de mensagens para aplicativos em execução em vários tipos de dispositivos.

## <a name="web-infrastructure"></a>Infraestrutura da Web

O [serviço de contêiner do Azure](/azure/aks/) gerencia seu ambiente kubernetes hospedado, tornando rápido e fácil implantar e gerenciar aplicativos em contêineres sem conhecimento de orquestração de contêiner.

[Azure Search](/azure/search/) é usado para criar uma solução de pesquisa empresarial sobre conteúdo privado e heterogêneo.

O [Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos que torna mais fácil empacotar, implantar e gerenciar microserviços e contêineres escalonáveis e confiáveis.
