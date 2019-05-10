---
title: DevOps com ASP.NET Core e Azure
author: CamSoper
description: Um guia que fornece orientação de ponta a ponta sobre a criação de um pipeline de DevOps para um aplicativo ASP.NET Core hospedado no Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
uid: azure/devops/index
ms.openlocfilehash: f45bb2a5dd4b3d1a820085ede7ce3219045ed80b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64881951"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="930e6-103">DevOps com ASP.NET Core e Azure</span><span class="sxs-lookup"><span data-stu-id="930e6-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="930e6-104">[![Imagem da capa](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="930e6-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="930e6-105">Por [Cam Soper](https://twitter.com/camsoper) e [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="930e6-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="930e6-106">Este guia está disponível como um [e-book em PDF para download](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="930e6-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="930e6-107">Bem-vindo</span><span class="sxs-lookup"><span data-stu-id="930e6-107">Welcome</span></span> 

<span data-ttu-id="930e6-108">Bem-vindo ao guia de ciclo de vida de desenvolvimento do Azure para .NET.</span><span class="sxs-lookup"><span data-stu-id="930e6-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="930e6-109">Este guia apresenta os conceitos básicos da criação de um ciclo de vida de desenvolvimento para o Azure usando processos e ferramentas do .NET.</span><span class="sxs-lookup"><span data-stu-id="930e6-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="930e6-110">Depois de concluí-lo, você aproveitará os benefícios de uma cadeia de ferramentas madura do DevOps.</span><span class="sxs-lookup"><span data-stu-id="930e6-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="930e6-111">A quem esse guia se destina</span><span class="sxs-lookup"><span data-stu-id="930e6-111">Who this guide is for</span></span>

<span data-ttu-id="930e6-112">Você deve ser um desenvolvedor experiente do ASP.NET Core (nível 200 a 300).</span><span class="sxs-lookup"><span data-stu-id="930e6-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="930e6-113">Você não precisa saber nada sobre o Azure, uma vez que abordaremos isso nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="930e6-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="930e6-114">Esse guia também pode ser útil para engenheiros de DevOps que estão mais focados nas operações do que no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="930e6-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="930e6-115">Esse guia destina-se aos desenvolvedores do Windows.</span><span class="sxs-lookup"><span data-stu-id="930e6-115">This guide targets Windows developers.</span></span> <span data-ttu-id="930e6-116">No entanto, Linux e macOS são totalmente compatíveis com .NET Core.</span><span class="sxs-lookup"><span data-stu-id="930e6-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="930e6-117">Para adaptar esse guia para Linux/macOS, fique atento a textos explicativos para diferenças do Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="930e6-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="930e6-118">O que este guia não aborda</span><span class="sxs-lookup"><span data-stu-id="930e6-118">What this guide doesn't cover</span></span>

<span data-ttu-id="930e6-119">Esse guia se concentra em uma experiência de implantação contínua de ponta a ponta para desenvolvedores do .NET.</span><span class="sxs-lookup"><span data-stu-id="930e6-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="930e6-120">Ele não é um guia completo para tudo relacionado ao Azure e não se concentra extensivamente em APIs .NET para serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="930e6-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="930e6-121">A ênfase está totalmente concentrada na integração contínua, na implantação, no monitoramento e na depuração.</span><span class="sxs-lookup"><span data-stu-id="930e6-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="930e6-122">No final do guia, são oferecidas recomendações para as próximas etapas.</span><span class="sxs-lookup"><span data-stu-id="930e6-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="930e6-123">Serviços da plataforma do Azure que úteis para desenvolvedores do ASP.NET Core estão incluídos nas sugestões.</span><span class="sxs-lookup"><span data-stu-id="930e6-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="930e6-124">O que há neste guia</span><span class="sxs-lookup"><span data-stu-id="930e6-124">What's in this guide</span></span>

### <a name="tools-and-downloadsxrefazuredevopstools-and-downloads"></a>[<span data-ttu-id="930e6-125">Ferramentas e downloads</span><span class="sxs-lookup"><span data-stu-id="930e6-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="930e6-126">Saiba onde obter as ferramentas usadas neste guia.</span><span class="sxs-lookup"><span data-stu-id="930e6-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-servicexrefazuredevopsdeploy-to-app-service"></a>[<span data-ttu-id="930e6-127">Implantar no Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="930e6-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="930e6-128">Aprenda os vários métodos para implantar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="930e6-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deploymentxrefazuredevopscicd"></a>[<span data-ttu-id="930e6-129">Integração contínua e implantação</span><span class="sxs-lookup"><span data-stu-id="930e6-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="930e6-130">Crie uma solução de implantação e integração contínua de ponta a ponta para seu aplicativo ASP.NET Core com o GitHub, o Azure DevOps Services e o Azure.</span><span class="sxs-lookup"><span data-stu-id="930e6-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debugxrefazuredevopsmonitor"></a>[<span data-ttu-id="930e6-131">Monitorar e depurar</span><span class="sxs-lookup"><span data-stu-id="930e6-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="930e6-132">Use as ferramentas do Azure para monitorar, solucionar problemas e ajustar seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="930e6-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-stepsxrefazuredevopsnext-steps"></a>[<span data-ttu-id="930e6-133">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="930e6-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="930e6-134">Outros roteiros de aprendizado para desenvolvedores do ASP.NET Core que estão aprendendo sobre o Azure.</span><span class="sxs-lookup"><span data-stu-id="930e6-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="930e6-135">Leitura adicional de introdução</span><span class="sxs-lookup"><span data-stu-id="930e6-135">Additional introductory reading</span></span>

<span data-ttu-id="930e6-136">Se esta é sua primeira experiência com a computação em nuvem, estes artigos explicam os conceitos básicos.</span><span class="sxs-lookup"><span data-stu-id="930e6-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="930e6-137">O que é a computação em nuvem?</span><span class="sxs-lookup"><span data-stu-id="930e6-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="930e6-138">Exemplos de computação em nuvem</span><span class="sxs-lookup"><span data-stu-id="930e6-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="930e6-139">O que é IaaS?</span><span class="sxs-lookup"><span data-stu-id="930e6-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="930e6-140">O que é PaaS?</span><span class="sxs-lookup"><span data-stu-id="930e6-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
