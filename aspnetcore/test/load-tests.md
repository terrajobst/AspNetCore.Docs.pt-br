---
title: ASP.NET Core teste de carga/estresse
author: Jeremy-Meng
description: Saiba mais sobre várias ferramentas e abordagens notáveis para teste de carga e teste de estresse ASP.NET Core aplicativos.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 7a9dfc1fedf747ab26daa573b61ed01c31709058
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975241"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="9bac9-103">ASP.NET Core teste de carga/estresse</span><span class="sxs-lookup"><span data-stu-id="9bac9-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="9bac9-104">Testes de carga e testes de estresse são importantes para garantir que um aplicativo Web seja eficaz e escalonável.</span><span class="sxs-lookup"><span data-stu-id="9bac9-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9bac9-105">Suas metas são diferentes, embora elas geralmente compartilhem testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="9bac9-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="9bac9-106">**Testes de carga** &ndash; Teste se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e, ao mesmo tempo, atender à meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="9bac9-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9bac9-107">O aplicativo é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="9bac9-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="9bac9-108">**Testes de estresse** &ndash; Teste a estabilidade do aplicativo quando executada sob condições extremas, muitas vezes por um longo período de tempo.</span><span class="sxs-lookup"><span data-stu-id="9bac9-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="9bac9-109">Os testes colocam alta carga de usuário, picos ou aumentam a carga gradualmente, no aplicativo, ou limitam os recursos de computação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bac9-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="9bac9-110">Testes de estresse determinam se um aplicativo sob estresse pode se recuperar de uma falha e retornar normalmente para o comportamento esperado.</span><span class="sxs-lookup"><span data-stu-id="9bac9-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="9bac9-111">Sob estresse, o aplicativo não é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="9bac9-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="9bac9-112">O Visual Studio 2019 é a última versão do Visual Studio com recursos de teste de carga.</span><span class="sxs-lookup"><span data-stu-id="9bac9-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="9bac9-113">Para clientes que precisam de ferramentas de teste de carga no futuro, recomendamos ferramentas alternativas, como Apache JMeter, Akamai CloudTest e BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="9bac9-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="9bac9-114">Para obter mais informações, consulte as [notas de versão do Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="9bac9-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="9bac9-115">O serviço de teste de carga no Azure DevOps está terminando em 2020.</span><span class="sxs-lookup"><span data-stu-id="9bac9-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="9bac9-116">Para obter mais informações, consulte [fim da vida útil do serviço de teste de carga baseado em nuvem](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="9bac9-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9bac9-117">Ferramentas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9bac9-117">Visual Studio tools</span></span>

<span data-ttu-id="9bac9-118">O Visual Studio permite que os usuários criem, desenvolvam e depurem testes de carga e desempenho da Web.</span><span class="sxs-lookup"><span data-stu-id="9bac9-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9bac9-119">Uma opção está disponível para criar testes por meio da gravação de ações em um navegador da Web.</span><span class="sxs-lookup"><span data-stu-id="9bac9-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="9bac9-120">Para obter informações sobre como criar, configurar e executar um projeto de teste de carga usando o Visual Studio 2017 [, consulte início rápido: Criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="9bac9-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="9bac9-121">Os testes de carga podem ser configurados para serem executados no local ou executados na nuvem usando o Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="9bac9-121">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="9bac9-122">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="9bac9-122">Azure DevOps</span></span>

<span data-ttu-id="9bac9-123">As execuções de teste de carga podem ser iniciadas usando o serviço de [Test Plans DevOps do Azure](/azure/devops/test/load-test/index?view=vsts) .</span><span class="sxs-lookup"><span data-stu-id="9bac9-123">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Página de aterrissagem de teste de carga do Azure DevOps](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="9bac9-125">O serviço oferece suporte aos seguintes formatos de teste:</span><span class="sxs-lookup"><span data-stu-id="9bac9-125">The service supports the following test formats:</span></span>

* <span data-ttu-id="9bac9-126">Teste na &ndash; Web do Visual Studio criado no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9bac9-126">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="9bac9-127">O arquivo &ndash; http capturado para o tráfego http dentro do arquivo é reproduzido durante o teste.</span><span class="sxs-lookup"><span data-stu-id="9bac9-127">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="9bac9-128">[Baseado em URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Permite especificar URLs para teste de carga, tipos de solicitação, cabeçalhos e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9bac9-128">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="9bac9-129">Executar parâmetros de configuração como duração, padrão de carga e número de usuários podem ser configurados.</span><span class="sxs-lookup"><span data-stu-id="9bac9-129">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="9bac9-130">[Apache JMeter](https://jmeter.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="9bac9-130">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="9bac9-131">Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="9bac9-131">Azure portal</span></span>

<span data-ttu-id="9bac9-132">[Portal do Azure permite configurar e executar testes de carga de aplicativos Web](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) diretamente da guia **desempenho** do serviço de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="9bac9-132">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Serviço de Azure App no portal do Azure](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="9bac9-134">O teste pode ser um teste manual com uma URL especificada ou um arquivo de teste da Web do Visual Studio, que pode testar várias URLs.</span><span class="sxs-lookup"><span data-stu-id="9bac9-134">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Nova página de teste de desempenho no portal do Azure](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="9bac9-136">No final do teste, os relatórios gerados mostram as características de desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bac9-136">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="9bac9-137">As estatísticas de exemplo incluem:</span><span class="sxs-lookup"><span data-stu-id="9bac9-137">Example statistics include:</span></span>

* <span data-ttu-id="9bac9-138">Tempo médio de resposta</span><span class="sxs-lookup"><span data-stu-id="9bac9-138">Average response time</span></span>
* <span data-ttu-id="9bac9-139">Taxa de transferência máxima: solicitações por segundo</span><span class="sxs-lookup"><span data-stu-id="9bac9-139">Max throughput: requests per second</span></span>
* <span data-ttu-id="9bac9-140">Percentual de falha</span><span class="sxs-lookup"><span data-stu-id="9bac9-140">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9bac9-141">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="9bac9-141">Third-party tools</span></span>

<span data-ttu-id="9bac9-142">A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="9bac9-142">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="9bac9-143">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="9bac9-143">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="9bac9-144">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="9bac9-144">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="9bac9-145">Gatling</span><span class="sxs-lookup"><span data-stu-id="9bac9-145">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="9bac9-146">Locust</span><span class="sxs-lookup"><span data-stu-id="9bac9-146">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="9bac9-147">Websurto de vento oeste</span><span class="sxs-lookup"><span data-stu-id="9bac9-147">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="9bac9-148">Netling</span><span class="sxs-lookup"><span data-stu-id="9bac9-148">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="9bac9-149">Vegeta</span><span class="sxs-lookup"><span data-stu-id="9bac9-149">Vegeta</span></span>](https://github.com/tsenart/vegeta)
