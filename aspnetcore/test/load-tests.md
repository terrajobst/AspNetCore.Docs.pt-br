---
title: Testes de carga/estresse do ASP.NET Core
author: Jeremy-Meng
description: Descreve várias ferramentas importantes e abordagens para testes de carga e aplicativos ASP.NET Core de teste de carga.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: d989bc841a372bed7ebf2c84c6abe1a57762ad04
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207350"
---
# <a name="load-and-stress-testing-aspnet-core"></a><span data-ttu-id="9e192-103">ASP.NET Core teste de estresse e carregar</span><span class="sxs-lookup"><span data-stu-id="9e192-103">Load and stress testing ASP.NET Core</span></span>

<span data-ttu-id="9e192-104">Teste de carga e testes de estresse são importantes para garantir que um aplicativo web é eficaz e escalonável.</span><span class="sxs-lookup"><span data-stu-id="9e192-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9e192-105">Suas metas são diferentes, mesmo que eles compartilham muitas vezes testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="9e192-105">Their goals are different even they often share similar tests.</span></span>

<span data-ttu-id="9e192-106">**Testes de carga**: Testa se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e ainda assim satisfazer a meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="9e192-106">**Load tests**: Tests whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9e192-107">O aplicativo é executado em condições normais.</span><span class="sxs-lookup"><span data-stu-id="9e192-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="9e192-108">**Testes de estresse**: Estabilidade do aplicativo de testes ao executar sob condições extremas e muitas vezes um longo período de tempo:</span><span class="sxs-lookup"><span data-stu-id="9e192-108">**Stress tests**: Tests app stability when running under extreme conditions and often a long period of time:</span></span>

* <span data-ttu-id="9e192-109">Carga de usuário com altos – picos ou aumentando gradualmente.</span><span class="sxs-lookup"><span data-stu-id="9e192-109">High user load – either spikes or gradually increasing.</span></span>
* <span data-ttu-id="9e192-110">Recursos de computação limitados.</span><span class="sxs-lookup"><span data-stu-id="9e192-110">Limited computing resources.</span></span>  

<span data-ttu-id="9e192-111">Sob carga excessiva, pode o aplicativo se recuperar de falha e normalmente retornar ao comportamento esperado?</span><span class="sxs-lookup"><span data-stu-id="9e192-111">Under stress, can the app recover from failure and gracefully return to expected behavior?</span></span> <span data-ttu-id="9e192-112">Sob carga excessiva, o aplicativo está *não* executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="9e192-112">Under stress, the app is *not* run under normal conditions.</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9e192-113">Ferramentas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e192-113">Visual Studio Tools</span></span>

<span data-ttu-id="9e192-114">Visual Studio permite aos usuários criar, desenvolver e depurar testes de carga e desempenho na web.</span><span class="sxs-lookup"><span data-stu-id="9e192-114">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9e192-115">Uma opção está disponível para criar testes gravando ações em um navegador da web.</span><span class="sxs-lookup"><span data-stu-id="9e192-115">An option is available to create tests by recording actions in web browser.</span></span>

<span data-ttu-id="9e192-116">[Início Rápido: Criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) mostra como criar, configurar e executar um teste de carga projetos usando o Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="9e192-116">[Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) shows how to create, configure, and run a load test projects using Visual Studio 2017.</span></span>

<span data-ttu-id="9e192-117">Consulte [Recursos adicionais](#add) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9e192-117">See [Additional Resources](#add) for more information.</span></span>

<span data-ttu-id="9e192-118">Testes de carga podem ser configurados para executar no local ou executados na nuvem usando o DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="9e192-118">Load tests can be configured to run in on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="9e192-119">DevOps do Azure</span><span class="sxs-lookup"><span data-stu-id="9e192-119">Azure DevOps</span></span>

<span data-ttu-id="9e192-120">Execuções de teste de carga podem ser iniciadas usando o [planos de teste do Azure DevOps](/azure/devops/test/load-test/index?view=vsts) service.</span><span class="sxs-lookup"><span data-stu-id="9e192-120">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="9e192-121">O serviço suporta os seguintes tipos de formato de teste:</span><span class="sxs-lookup"><span data-stu-id="9e192-121">The service supports the following types of test format:</span></span>

- <span data-ttu-id="9e192-122">Teste do Visual Studio – teste da web criado no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e192-122">Visual Studio test – web test created in Visual Studio.</span></span>
- <span data-ttu-id="9e192-123">Teste com base em arquivo HTTP – tráfego HTTP capturado dentro do arquivo morto é reproduzida durante o teste.</span><span class="sxs-lookup"><span data-stu-id="9e192-123">HTTP Archive-based test – captured HTTP traffic inside archive is replayed during testing.</span></span>
- <span data-ttu-id="9e192-124">[Teste com base na URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – permite especificar URLs para carregar testes, tipos de solicitação, cabeçalhos e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e192-124">[URL-based test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="9e192-125">Executar a configuração de parâmetros, como duração, padrão de carga, o número de usuários, etc., pode ser configurado.</span><span class="sxs-lookup"><span data-stu-id="9e192-125">Run setting parameters such as duration, load pattern, number of users, etc., can be configured.</span></span>
- <span data-ttu-id="9e192-126">[Apache JMeter](https://jmeter.apache.org/) de teste.</span><span class="sxs-lookup"><span data-stu-id="9e192-126">[Apache JMeter](https://jmeter.apache.org/) test.</span></span>

## <a name="azure-portal"></a><span data-ttu-id="9e192-127">Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="9e192-127">Azure portal</span></span>

<span data-ttu-id="9e192-128">[Portal do Azure permite configurar e executar testes de carga de aplicativos Web,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) diretamente a partir da guia de desempenho do serviço de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="9e192-128">[Azure portal allows setting up and running load testing of Web Apps,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the Performance tab of the App Service in Azure portal.</span></span>

![](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="9e192-129">O teste pode ser um teste manual com uma URL especificada, ou um arquivo de teste do Visual Studio Web, que pode testar várias URLs.</span><span class="sxs-lookup"><span data-stu-id="9e192-129">The test can be a manual test with a specified URL, or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="9e192-130">No final do teste, os relatórios são gerados para mostrar as características de desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e192-130">At end of the test, reports are generated to show the performance characteristics of the app.</span></span> <span data-ttu-id="9e192-131">Estatísticas de exemplo incluem:</span><span class="sxs-lookup"><span data-stu-id="9e192-131">Example statistics include:</span></span>

- <span data-ttu-id="9e192-132">Tempo médio de resposta</span><span class="sxs-lookup"><span data-stu-id="9e192-132">Average response time</span></span>
- <span data-ttu-id="9e192-133">Taxa de transferência máxima: solicitações por segundo</span><span class="sxs-lookup"><span data-stu-id="9e192-133">Max throughput: requests per second</span></span>
- <span data-ttu-id="9e192-134">Percentual de falha</span><span class="sxs-lookup"><span data-stu-id="9e192-134">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9e192-135">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="9e192-135">Third-party Tools</span></span>

<span data-ttu-id="9e192-136">A lista a seguir contém as ferramentas de desempenho da web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="9e192-136">The following list contains third-party web performance tools with various feature sets:</span></span>

- <span data-ttu-id="9e192-137">[Apache JMeter](https://jmeter.apache.org/) : Pacote de destaque completo de ferramentas de teste de carga.</span><span class="sxs-lookup"><span data-stu-id="9e192-137">[Apache JMeter](https://jmeter.apache.org/) : Full featured suite of load testing tools.</span></span> <span data-ttu-id="9e192-138">Limite de thread: precisa de um thread por usuário.</span><span class="sxs-lookup"><span data-stu-id="9e192-138">Thread-bound: need one thread per user.</span></span>
- [<span data-ttu-id="9e192-139">AB - servidor HTTP Apache ferramenta de benchmark</span><span class="sxs-lookup"><span data-stu-id="9e192-139">ab - Apache HTTP server benchmarking tool</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
- <span data-ttu-id="9e192-140">[Gatling](https://gatling.io/) : Ferramenta da área de trabalho com gravadores de GUI e de teste.</span><span class="sxs-lookup"><span data-stu-id="9e192-140">[Gatling](https://gatling.io/) : Desktop tool with a GUI and test recorders.</span></span> <span data-ttu-id="9e192-141">Mais eficaz do que o JMeter.</span><span class="sxs-lookup"><span data-stu-id="9e192-141">More performant than JMeter.</span></span>
- <span data-ttu-id="9e192-142">[Locust.IO](https://locust.io/) : Não é limitado por threads.</span><span class="sxs-lookup"><span data-stu-id="9e192-142">[Locust.io](https://locust.io/) : Not bounded by threads.</span></span>

<a name="add"></a>
## <a name="additional-resources"></a><span data-ttu-id="9e192-143">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9e192-143">Additional Resources</span></span>

<span data-ttu-id="9e192-144">[Série de blogs de teste de carga](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) por Charles Sterling.</span><span class="sxs-lookup"><span data-stu-id="9e192-144">[Load Test blog series](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) by Charles Sterling.</span></span> <span data-ttu-id="9e192-145">Com data, mas a maioria dos tópicos ainda é relevante.</span><span class="sxs-lookup"><span data-stu-id="9e192-145">Dated but most of the topics are still relevant.</span></span>
