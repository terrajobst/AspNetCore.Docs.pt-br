---
title: Testes de carga/estresse do ASP.NET Core
author: Jeremy-Meng
description: Descreve várias ferramentas importantes e abordagens para testes de carga e aplicativos ASP.NET Core de teste de carga.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: 587df6e216943d3eeec779df4d0554dd0fc2fda0
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345422"
---
# <a name="load-and-stress-testing-aspnet-core"></a><span data-ttu-id="7424c-103">ASP.NET Core teste de estresse e carregar</span><span class="sxs-lookup"><span data-stu-id="7424c-103">Load and stress testing ASP.NET Core</span></span>

<span data-ttu-id="7424c-104">Teste de carga e testes de estresse são importantes para garantir que um aplicativo web é eficaz e escalonável.</span><span class="sxs-lookup"><span data-stu-id="7424c-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="7424c-105">Suas metas são diferentes, mesmo que eles compartilham muitas vezes testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="7424c-105">Their goals are different even they often share similar tests.</span></span>

<span data-ttu-id="7424c-106">**Testes de carga**: Testa se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e ainda assim satisfazer a meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="7424c-106">**Load tests**: Tests whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="7424c-107">O aplicativo é executado em condições normais.</span><span class="sxs-lookup"><span data-stu-id="7424c-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="7424c-108">**Testes de estresse**: Estabilidade do aplicativo de testes ao executar sob condições extremas e muitas vezes um longo período de tempo:</span><span class="sxs-lookup"><span data-stu-id="7424c-108">**Stress tests**: Tests app stability when running under extreme conditions and often a long period of time:</span></span>

* <span data-ttu-id="7424c-109">Carga de usuário com altos – picos ou aumentando gradualmente.</span><span class="sxs-lookup"><span data-stu-id="7424c-109">High user load – either spikes or gradually increasing.</span></span>
* <span data-ttu-id="7424c-110">Recursos de computação limitados.</span><span class="sxs-lookup"><span data-stu-id="7424c-110">Limited computing resources.</span></span>  

<span data-ttu-id="7424c-111">Sob carga excessiva, pode o aplicativo se recuperar de falha e normalmente retornar ao comportamento esperado?</span><span class="sxs-lookup"><span data-stu-id="7424c-111">Under stress, can the app recover from failure and gracefully return to expected behavior?</span></span> <span data-ttu-id="7424c-112">Sob carga excessiva, o aplicativo está *não* executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="7424c-112">Under stress, the app is *not* run under normal conditions.</span></span>

<span data-ttu-id="7424c-113">O Visual Studio 2019 será a última versão do Visual Studio com funcionalidades de teste de carga.</span><span class="sxs-lookup"><span data-stu-id="7424c-113">Visual Studio 2019 will be the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="7424c-114">Para clientes que exigem ferramentas de teste de carga, é recomendável usar ferramentas de teste de carga alternativas, assim como Apache JMeter, Akamai CloudTest e Blazemeter.</span><span class="sxs-lookup"><span data-stu-id="7424c-114">For customers requiring load testing tools, we recommend using alternate load testing tools such as Apache JMeter, Akamai CloudTest, Blazemeter.</span></span> <span data-ttu-id="7424c-115">Para obter mais informações, consulte o [notas de versão visualização do Visual Studio 2019](/visualstudio/releases/2019/release-notes-preview#test-tools).</span><span class="sxs-lookup"><span data-stu-id="7424c-115">For more information, see the [Visual Studio 2019 Preview Release Notes](/visualstudio/releases/2019/release-notes-preview#test-tools).</span></span>

<span data-ttu-id="7424c-116">A testes de carga service no DevOps do Azure terminará em 2020.</span><span class="sxs-lookup"><span data-stu-id="7424c-116">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="7424c-117">Para obter mais informações, consulte [fim de serviço da vida útil de teste de carga baseado em nuvem](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="7424c-117">For more information see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="7424c-118">Ferramentas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7424c-118">Visual Studio Tools</span></span>

<span data-ttu-id="7424c-119">Visual Studio permite aos usuários criar, desenvolver e depurar testes de carga e desempenho na web.</span><span class="sxs-lookup"><span data-stu-id="7424c-119">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="7424c-120">Uma opção está disponível para criar testes gravando ações em um navegador da web.</span><span class="sxs-lookup"><span data-stu-id="7424c-120">An option is available to create tests by recording actions in web browser.</span></span>

<span data-ttu-id="7424c-121">[Início Rápido: Criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) mostra como criar, configurar e executar um teste de carga projetos usando o Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="7424c-121">[Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) shows how to create, configure, and run a load test projects using Visual Studio 2017.</span></span>

<span data-ttu-id="7424c-122">Consulte [Recursos adicionais](#add) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="7424c-122">See [Additional Resources](#add) for more information.</span></span>

<span data-ttu-id="7424c-123">Testes de carga podem ser configurados para executar no local ou executados na nuvem usando o DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="7424c-123">Load tests can be configured to run in on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="7424c-124">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="7424c-124">Azure DevOps</span></span>

<span data-ttu-id="7424c-125">Execuções de teste de carga podem ser iniciadas usando o [planos de teste do Azure DevOps](/azure/devops/test/load-test/index?view=vsts) service.</span><span class="sxs-lookup"><span data-stu-id="7424c-125">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="7424c-126">O serviço suporta os seguintes tipos de formato de teste:</span><span class="sxs-lookup"><span data-stu-id="7424c-126">The service supports the following types of test format:</span></span>

- <span data-ttu-id="7424c-127">Teste do Visual Studio – teste da web criado no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7424c-127">Visual Studio test – web test created in Visual Studio.</span></span>
- <span data-ttu-id="7424c-128">Teste com base em arquivo HTTP – tráfego HTTP capturado dentro do arquivo morto é reproduzida durante o teste.</span><span class="sxs-lookup"><span data-stu-id="7424c-128">HTTP Archive-based test – captured HTTP traffic inside archive is replayed during testing.</span></span>
- <span data-ttu-id="7424c-129">[Teste com base na URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – permite especificar URLs para carregar testes, tipos de solicitação, cabeçalhos e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="7424c-129">[URL-based test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="7424c-130">Executar a configuração de parâmetros, como duração, padrão de carga, o número de usuários, etc., pode ser configurado.</span><span class="sxs-lookup"><span data-stu-id="7424c-130">Run setting parameters such as duration, load pattern, number of users, etc., can be configured.</span></span>
- <span data-ttu-id="7424c-131">[Apache JMeter](https://jmeter.apache.org/) de teste.</span><span class="sxs-lookup"><span data-stu-id="7424c-131">[Apache JMeter](https://jmeter.apache.org/) test.</span></span>

## <a name="azure-portal"></a><span data-ttu-id="7424c-132">Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="7424c-132">Azure portal</span></span>

<span data-ttu-id="7424c-133">[Portal do Azure permite configurar e executar testes de carga de aplicativos Web,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) diretamente a partir da guia de desempenho do serviço de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="7424c-133">[Azure portal allows setting up and running load testing of Web Apps,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the Performance tab of the App Service in Azure portal.</span></span>

![](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="7424c-134">O teste pode ser um teste manual com uma URL especificada, ou um arquivo de teste do Visual Studio Web, que pode testar várias URLs.</span><span class="sxs-lookup"><span data-stu-id="7424c-134">The test can be a manual test with a specified URL, or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="7424c-135">No final do teste, os relatórios são gerados para mostrar as características de desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7424c-135">At end of the test, reports are generated to show the performance characteristics of the app.</span></span> <span data-ttu-id="7424c-136">Estatísticas de exemplo incluem:</span><span class="sxs-lookup"><span data-stu-id="7424c-136">Example statistics include:</span></span>

- <span data-ttu-id="7424c-137">Tempo médio de resposta</span><span class="sxs-lookup"><span data-stu-id="7424c-137">Average response time</span></span>
- <span data-ttu-id="7424c-138">Taxa de transferência máxima: solicitações por segundo</span><span class="sxs-lookup"><span data-stu-id="7424c-138">Max throughput: requests per second</span></span>
- <span data-ttu-id="7424c-139">Percentual de falha</span><span class="sxs-lookup"><span data-stu-id="7424c-139">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="7424c-140">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="7424c-140">Third-party Tools</span></span>

<span data-ttu-id="7424c-141">A lista a seguir contém as ferramentas de desempenho da web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="7424c-141">The following list contains third-party web performance tools with various feature sets:</span></span>

- <span data-ttu-id="7424c-142">[Apache JMeter](https://jmeter.apache.org/) : Pacote de destaque completo de ferramentas de teste de carga.</span><span class="sxs-lookup"><span data-stu-id="7424c-142">[Apache JMeter](https://jmeter.apache.org/) : Full featured suite of load testing tools.</span></span> <span data-ttu-id="7424c-143">Limite de thread: precisa de um thread por usuário.</span><span class="sxs-lookup"><span data-stu-id="7424c-143">Thread-bound: need one thread per user.</span></span>
- [<span data-ttu-id="7424c-144">AB - servidor HTTP Apache ferramenta de benchmark</span><span class="sxs-lookup"><span data-stu-id="7424c-144">ab - Apache HTTP server benchmarking tool</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
- <span data-ttu-id="7424c-145">[Gatling](https://gatling.io/) : Ferramenta da área de trabalho com gravadores de GUI e de teste.</span><span class="sxs-lookup"><span data-stu-id="7424c-145">[Gatling](https://gatling.io/) : Desktop tool with a GUI and test recorders.</span></span> <span data-ttu-id="7424c-146">Mais eficaz do que o JMeter.</span><span class="sxs-lookup"><span data-stu-id="7424c-146">More performant than JMeter.</span></span>
- <span data-ttu-id="7424c-147">[Locust.IO](https://locust.io/) : Não é limitado por threads.</span><span class="sxs-lookup"><span data-stu-id="7424c-147">[Locust.io](https://locust.io/) : Not bounded by threads.</span></span>

<a name="add"></a>
## <a name="additional-resources"></a><span data-ttu-id="7424c-148">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7424c-148">Additional Resources</span></span>

<span data-ttu-id="7424c-149">[Série de blogs de teste de carga](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) por Charles Sterling.</span><span class="sxs-lookup"><span data-stu-id="7424c-149">[Load Test blog series](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) by Charles Sterling.</span></span> <span data-ttu-id="7424c-150">Com data, mas a maioria dos tópicos ainda é relevante.</span><span class="sxs-lookup"><span data-stu-id="7424c-150">Dated but most of the topics are still relevant.</span></span>
