---
title: Testes de carga/estresse do ASP.NET Core
author: Jeremy-Meng
description: Saiba mais sobre várias importantes ferramentas e abordagens para testes de carga e aplicativos ASP.NET Core de teste de carga.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 0a8449ea2c9df0f2ac93058f03af0a1a2aa66508
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897433"
---
# <a name="aspnet-core-loadstress-testing"></a>Testes de carga/estresse do ASP.NET Core

Teste de carga e testes de estresse são importantes para garantir que um aplicativo web é eficaz e escalonável. Suas metas são diferentes, mesmo que eles compartilham muitas vezes testes semelhantes.

**Testes de carga** &ndash; testar se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e ainda assim satisfazer a meta de resposta. O aplicativo é executado em condições normais.

**Testes de estresse** &ndash; testar a estabilidade do aplicativo ao ser executada sob condições extremas, geralmente por um longo período de tempo. Os testes de colocar a carga de usuário com altos, picos ou gradualmente aumento da carga, no aplicativo ou eles limitam os recursos de computação do aplicativo.

Testes de estresse determinar se um aplicativo sob carga excessiva pode se recuperar de falha e normalmente retornar ao comportamento esperado. Sob carga excessiva, o aplicativo não é executado em condições normais.

2019 do Visual Studio é a última versão do Visual Studio com recursos de teste de carga. Para clientes que exigem ferramentas de teste no futuro de carga, é recomendável ferramentas alternativas, como o Apache JMeter, Akamai CloudTest e BlazeMeter. Para obter mais informações, consulte o [notas de versão do Visual Studio 2019](/visualstudio/releases/2019/release-notes#test-tools).

A testes de carga service no DevOps do Azure terminará em 2020. Para obter mais informações, consulte [fim de serviço da vida útil de teste de carga baseado em nuvem](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Ferramentas do Visual Studio

Visual Studio permite aos usuários criar, desenvolver e depurar testes de carga e desempenho na web. Uma opção está disponível para criar testes gravando ações em um navegador da web.

Para obter informações sobre como criar, configurar e executar um teste de carga projetos usando o Visual Studio 2017, consulte [guia de início rápido: Criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017). Para obter mais informações, consulte o [recursos adicionais](#additional-resources) seção.

Testes de carga podem ser configurados para ser executado no local ou em execução na nuvem usando o DevOps do Azure.

## <a name="azure-devops"></a>Azure DevOps

Execuções de teste de carga podem ser iniciadas usando o [planos de teste do Azure DevOps](/azure/devops/test/load-test/index?view=vsts) service.

![Página de aterrissagem de teste de carga de DevOps do Azure](./load-tests/_static/azure-devops-load-test.png)

O serviço dá suporte aos seguintes formatos de teste:

* Visual Studio &ndash; teste da Web criado no Visual Studio.
* Arquivo HTTP &ndash; tráfego HTTP capturado dentro do arquivo morto é reproduzido durante o teste.
* [Baseado em URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; permite especificar URLs para carregar testes, tipos de solicitação, cabeçalhos e cadeias de caracteres de consulta. Executar a configuração de parâmetros, como duração, padrão de carga e o número de usuários podem ser configurado.
* [Apache JMeter](https://jmeter.apache.org/).

## <a name="azure-portal"></a>Portal do Azure

[Portal do Azure permite configurar e executar testes de carga de aplicativos web](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) diretamente a partir de **desempenho** guia do serviço de aplicativo no portal do Azure.

![Serviço de aplicativo do Azure no portal do Azure](./load-tests/_static/azure-appservice-perf-test.png)

O teste pode ser um teste manual com uma URL especificada ou um arquivo de teste do Visual Studio Web, que pode testar várias URLs.

![Nova página de teste de desempenho no portal do Azure](./load-tests/_static/azure-appservice-perf-test-config.png)

No final do teste, gerar relatórios mostram as características de desempenho do aplicativo. Estatísticas de exemplo incluem:

* Tempo médio de resposta
* Taxa de transferência máxima: solicitações por segundo
* Percentual de falha

## <a name="third-party-tools"></a>Ferramentas de terceiros

A lista a seguir contém as ferramentas de desempenho da web de terceiros com vários conjuntos de recursos:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [West Wind WebSurge](http://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)

## <a name="additional-resources"></a>Recursos adicionais

* [Série de blogs de teste de carga](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
