---
title: Hospedar o ASP.NET Core em contêineres do Docker
author: rick-anderson
description: Descobrir links para recursos para saber mais sobre como hospedar aplicativos ASP.NET Core em contêineres do Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: cb5f774db5fab46a57f8ca4bbbca148f20f371ba
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308043"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Hospedar o ASP.NET Core em contêineres do Docker

Os artigos a seguir estão disponíveis para saber mais sobre como hospedar aplicativos ASP.NET Core no Docker:

[Introdução aos contêineres e ao Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Veja como o uso de contêineres é uma abordagem de desenvolvimento de software na qual um aplicativo ou serviço, suas dependências e sua configuração são empacotados juntos como uma imagem de contêiner. A imagem pode ser testada e, em seguida, implantada em um host.

[O que é o Docker?](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Descubra como o Docker é um projeto de software livre para automatizar a implantação de aplicativos como contêineres autossuficientes portáteis que podem ser executados na nuvem ou localmente.

[Terminologia do Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Aprenda termos e definições para a tecnologia do Docker.

[Registros, imagens e contêineres do Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Descubra como imagens de contêiner do Docker são armazenadas em um registro de imagem para implantação consistente entre ambientes.

<xref:host-and-deploy/docker/building-net-docker-images> Saiba como criar um aplicativo ASP.NET Core e convertê-lo para Docker. Explore imagens do Docker mantidas pela Microsoft e examine os casos de uso.

[Ferramentas de contêiner do Visual Studio](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Descubra como o Visual Studio dá suporte à criação, à depuração e à execução de aplicativos ASP.NET Core direcionados ao .NET Framework ou ao .NET Core no Docker for Windows. Contêineres do Windows e do Linux são compatíveis.

[Publicar no Registro de Contêiner do Azure](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Saiba como usar a extensão Ferramentas de Contêiner do Visual Studio para implantar um aplicativo do ASP.NET Core para um host Docker no Azure usando o PowerShell.

[Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer)  
Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy e balanceadores de carga. Passar solicitações por meio de um proxy geralmente oculta informações sobre a solicitação original, como o esquema e o IP de cliente. Talvez seja necessário encaminhar manualmente algumas informações sobre a solicitação para o aplicativo.
