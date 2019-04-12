---
title: Hospedar o ASP.NET Core em contêineres do Docker
author: rick-anderson
description: Descobrir links para recursos para saber mais sobre como hospedar aplicativos ASP.NET Core em contêineres do Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: e56f90ec7272ce0411651ee6f8e7c754ae44b78d
ms.sourcegitcommit: 9b7fcb4ce00a3a32e153a080ebfaae4ef417aafa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59516255"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="57c29-103">Hospedar o ASP.NET Core em contêineres do Docker</span><span class="sxs-lookup"><span data-stu-id="57c29-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="57c29-104">Os artigos a seguir estão disponíveis para saber mais sobre como hospedar aplicativos ASP.NET Core no Docker:</span><span class="sxs-lookup"><span data-stu-id="57c29-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="57c29-105">Introdução aos contêineres e ao Docker</span><span class="sxs-lookup"><span data-stu-id="57c29-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="57c29-106">Veja como o uso de contêineres é uma abordagem de desenvolvimento de software na qual um aplicativo ou serviço, suas dependências e sua configuração são empacotados juntos como uma imagem de contêiner.</span><span class="sxs-lookup"><span data-stu-id="57c29-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="57c29-107">A imagem pode ser testada e, em seguida, implantada em um host.</span><span class="sxs-lookup"><span data-stu-id="57c29-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="57c29-108">O que é o Docker?</span><span class="sxs-lookup"><span data-stu-id="57c29-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="57c29-109">Descubra como o Docker é um projeto de software livre para automatizar a implantação de aplicativos como contêineres autossuficientes portáteis que podem ser executados na nuvem ou localmente.</span><span class="sxs-lookup"><span data-stu-id="57c29-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="57c29-110">Terminologia do Docker</span><span class="sxs-lookup"><span data-stu-id="57c29-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="57c29-111">Aprenda termos e definições para a tecnologia do Docker.</span><span class="sxs-lookup"><span data-stu-id="57c29-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="57c29-112">Registros, imagens e contêineres do Docker</span><span class="sxs-lookup"><span data-stu-id="57c29-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="57c29-113">Descubra como imagens de contêiner do Docker são armazenadas em um registro de imagem para implantação consistente entre ambientes.</span><span class="sxs-lookup"><span data-stu-id="57c29-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

[<span data-ttu-id="57c29-114">Ferramentas do Visual Studio para Docker</span><span class="sxs-lookup"><span data-stu-id="57c29-114">Visual Studio Tools for Docker</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="57c29-115">Descubra como o Visual Studio 2017 permite a criação, depuração e execução de aplicativos ASP.NET Core direcionados ao .NET Framework ou ao .NET Core no Docker para Windows.</span><span class="sxs-lookup"><span data-stu-id="57c29-115">Discover how Visual Studio 2017 supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="57c29-116">Contêineres do Windows e do Linux são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="57c29-116">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="57c29-117">Publicar em uma imagem do Docker</span><span class="sxs-lookup"><span data-stu-id="57c29-117">Publish to a Docker Image</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="57c29-118">Saiba como usar a extensão Ferramentas do Visual Studio para Docker para implantar um aplicativo do ASP.NET Core para um host Docker no Azure usando o PowerShell.</span><span class="sxs-lookup"><span data-stu-id="57c29-118">Find out how to use the Visual Studio Tools for Docker extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="57c29-119">Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga</span><span class="sxs-lookup"><span data-stu-id="57c29-119">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="57c29-120">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="57c29-120">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="57c29-121">Passar solicitações por meio de um proxy geralmente oculta informações sobre a solicitação original, como o esquema e o IP de cliente.</span><span class="sxs-lookup"><span data-stu-id="57c29-121">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="57c29-122">Talvez seja necessário encaminhar manualmente algumas informações sobre a solicitação para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="57c29-122">It might be necessary to forwarded some information about the request manually to the app.</span></span>
