---
title: Testes de carga/estresse do ASP.NET Core
author: Jeremy-Meng
description: Saiba mais sobre várias importantes ferramentas e abordagens para testes de carga e aplicativos ASP.NET Core de teste de carga.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 0a8449ea2c9df0f2ac93058f03af0a1a2aa66508
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068177"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="d6091-103">Testes de carga/estresse do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6091-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="d6091-104">Teste de carga e testes de estresse são importantes para garantir que um aplicativo web é eficaz e escalonável.</span><span class="sxs-lookup"><span data-stu-id="d6091-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="d6091-105">Suas metas são diferentes, mesmo que eles compartilham muitas vezes testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="d6091-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="d6091-106">**Testes de carga** &ndash; testar se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e ainda assim satisfazer a meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="d6091-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="d6091-107">O aplicativo é executado em condições normais.</span><span class="sxs-lookup"><span data-stu-id="d6091-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="d6091-108">**Testes de estresse** &ndash; testar a estabilidade do aplicativo ao ser executada sob condições extremas, geralmente por um longo período de tempo.</span><span class="sxs-lookup"><span data-stu-id="d6091-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="d6091-109">Os testes de colocar a carga de usuário com altos, picos ou gradualmente aumento da carga, no aplicativo ou eles limitam os recursos de computação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d6091-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="d6091-110">Testes de estresse determinar se um aplicativo sob carga excessiva pode se recuperar de falha e normalmente retornar ao comportamento esperado.</span><span class="sxs-lookup"><span data-stu-id="d6091-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="d6091-111">Sob carga excessiva, o aplicativo não é executado em condições normais.</span><span class="sxs-lookup"><span data-stu-id="d6091-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="d6091-112">2019 do Visual Studio é a última versão do Visual Studio com recursos de teste de carga.</span><span class="sxs-lookup"><span data-stu-id="d6091-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="d6091-113">Para clientes que exigem ferramentas de teste no futuro de carga, é recomendável ferramentas alternativas, como o Apache JMeter, Akamai CloudTest e BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="d6091-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="d6091-114">Para obter mais informações, consulte o [notas de versão do Visual Studio 2019](/visualstudio/releases/2019/release-notes#test-tools).</span><span class="sxs-lookup"><span data-stu-id="d6091-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes#test-tools).</span></span>

<span data-ttu-id="d6091-115">A testes de carga service no DevOps do Azure terminará em 2020.</span><span class="sxs-lookup"><span data-stu-id="d6091-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="d6091-116">Para obter mais informações, consulte [fim de serviço da vida útil de teste de carga baseado em nuvem](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="d6091-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="d6091-117">Ferramentas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6091-117">Visual Studio tools</span></span>

<span data-ttu-id="d6091-118">Visual Studio permite aos usuários criar, desenvolver e depurar testes de carga e desempenho na web.</span><span class="sxs-lookup"><span data-stu-id="d6091-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="d6091-119">Uma opção está disponível para criar testes gravando ações em um navegador da web.</span><span class="sxs-lookup"><span data-stu-id="d6091-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="d6091-120">Para obter informações sobre como criar, configurar e executar um teste de carga projetos usando o Visual Studio 2017, consulte [guia de início rápido: Criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="d6091-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span> <span data-ttu-id="d6091-121">Para obter mais informações, consulte o [recursos adicionais](#additional-resources) seção.</span><span class="sxs-lookup"><span data-stu-id="d6091-121">For more information, see the [Additional resources](#additional-resources) section.</span></span>

<span data-ttu-id="d6091-122">Testes de carga podem ser configurados para ser executado no local ou em execução na nuvem usando o DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="d6091-122">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="d6091-123">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="d6091-123">Azure DevOps</span></span>

<span data-ttu-id="d6091-124">Execuções de teste de carga podem ser iniciadas usando o [planos de teste do Azure DevOps](/azure/devops/test/load-test/index?view=vsts) service.</span><span class="sxs-lookup"><span data-stu-id="d6091-124">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Página de aterrissagem de teste de carga de DevOps do Azure](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="d6091-126">O serviço dá suporte aos seguintes formatos de teste:</span><span class="sxs-lookup"><span data-stu-id="d6091-126">The service supports the following test formats:</span></span>

* <span data-ttu-id="d6091-127">Visual Studio &ndash; teste da Web criado no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d6091-127">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="d6091-128">Arquivo HTTP &ndash; tráfego HTTP capturado dentro do arquivo morto é reproduzido durante o teste.</span><span class="sxs-lookup"><span data-stu-id="d6091-128">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="d6091-129">[Baseado em URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; permite especificar URLs para carregar testes, tipos de solicitação, cabeçalhos e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="d6091-129">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="d6091-130">Executar a configuração de parâmetros, como duração, padrão de carga e o número de usuários podem ser configurado.</span><span class="sxs-lookup"><span data-stu-id="d6091-130">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="d6091-131">[Apache JMeter](https://jmeter.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="d6091-131">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="d6091-132">Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="d6091-132">Azure portal</span></span>

<span data-ttu-id="d6091-133">[Portal do Azure permite configurar e executar testes de carga de aplicativos web](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) diretamente a partir de **desempenho** guia do serviço de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="d6091-133">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Serviço de aplicativo do Azure no portal do Azure](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="d6091-135">O teste pode ser um teste manual com uma URL especificada ou um arquivo de teste do Visual Studio Web, que pode testar várias URLs.</span><span class="sxs-lookup"><span data-stu-id="d6091-135">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Nova página de teste de desempenho no portal do Azure](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="d6091-137">No final do teste, gerar relatórios mostram as características de desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d6091-137">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="d6091-138">Estatísticas de exemplo incluem:</span><span class="sxs-lookup"><span data-stu-id="d6091-138">Example statistics include:</span></span>

* <span data-ttu-id="d6091-139">Tempo médio de resposta</span><span class="sxs-lookup"><span data-stu-id="d6091-139">Average response time</span></span>
* <span data-ttu-id="d6091-140">Taxa de transferência máxima: solicitações por segundo</span><span class="sxs-lookup"><span data-stu-id="d6091-140">Max throughput: requests per second</span></span>
* <span data-ttu-id="d6091-141">Percentual de falha</span><span class="sxs-lookup"><span data-stu-id="d6091-141">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="d6091-142">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="d6091-142">Third-party tools</span></span>

<span data-ttu-id="d6091-143">A lista a seguir contém as ferramentas de desempenho da web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="d6091-143">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="d6091-144">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="d6091-144">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="d6091-145">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="d6091-145">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="d6091-146">Gatling</span><span class="sxs-lookup"><span data-stu-id="d6091-146">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="d6091-147">Locust</span><span class="sxs-lookup"><span data-stu-id="d6091-147">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="d6091-148">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="d6091-148">West Wind WebSurge</span></span>](http://websurge.west-wind.com/)
* [<span data-ttu-id="d6091-149">Netling</span><span class="sxs-lookup"><span data-stu-id="d6091-149">Netling</span></span>](https://github.com/hallatore/Netling)

## <a name="additional-resources"></a><span data-ttu-id="d6091-150">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d6091-150">Additional resources</span></span>

* [<span data-ttu-id="d6091-151">Série de blogs de teste de carga</span><span class="sxs-lookup"><span data-stu-id="d6091-151">Load Test blog series</span></span>](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
