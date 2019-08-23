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
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core teste de carga/estresse

Testes de carga e testes de estresse são importantes para garantir que um aplicativo Web seja eficaz e escalonável. Suas metas são diferentes, embora elas geralmente compartilhem testes semelhantes.

**Testes de carga** &ndash; Teste se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e, ao mesmo tempo, atender à meta de resposta. O aplicativo é executado sob condições normais.

**Testes de estresse** &ndash; Teste a estabilidade do aplicativo quando executada sob condições extremas, muitas vezes por um longo período de tempo. Os testes colocam alta carga de usuário, picos ou aumentam a carga gradualmente, no aplicativo, ou limitam os recursos de computação do aplicativo.

Testes de estresse determinam se um aplicativo sob estresse pode se recuperar de uma falha e retornar normalmente para o comportamento esperado. Sob estresse, o aplicativo não é executado sob condições normais.

O Visual Studio 2019 é a última versão do Visual Studio com recursos de teste de carga. Para clientes que precisam de ferramentas de teste de carga no futuro, recomendamos ferramentas alternativas, como Apache JMeter, Akamai CloudTest e BlazeMeter. Para obter mais informações, consulte as [notas de versão do Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

O serviço de teste de carga no Azure DevOps está terminando em 2020. Para obter mais informações, consulte [fim da vida útil do serviço de teste de carga baseado em nuvem](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Ferramentas do Visual Studio

O Visual Studio permite que os usuários criem, desenvolvam e depurem testes de carga e desempenho da Web. Uma opção está disponível para criar testes por meio da gravação de ações em um navegador da Web.

Para obter informações sobre como criar, configurar e executar um projeto de teste de carga usando o Visual Studio 2017 [, consulte início rápido: Criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Os testes de carga podem ser configurados para serem executados no local ou executados na nuvem usando o Azure DevOps.

## <a name="azure-devops"></a>Azure DevOps

As execuções de teste de carga podem ser iniciadas usando o serviço de [Test Plans DevOps do Azure](/azure/devops/test/load-test/index?view=vsts) .

![Página de aterrissagem de teste de carga do Azure DevOps](./load-tests/_static/azure-devops-load-test.png)

O serviço oferece suporte aos seguintes formatos de teste:

* Teste na &ndash; Web do Visual Studio criado no Visual Studio.
* O arquivo &ndash; http capturado para o tráfego http dentro do arquivo é reproduzido durante o teste.
* [Baseado em URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Permite especificar URLs para teste de carga, tipos de solicitação, cabeçalhos e cadeias de caracteres de consulta. Executar parâmetros de configuração como duração, padrão de carga e número de usuários podem ser configurados.
* [Apache JMeter](https://jmeter.apache.org/).

## <a name="azure-portal"></a>Portal do Azure

[Portal do Azure permite configurar e executar testes de carga de aplicativos Web](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) diretamente da guia **desempenho** do serviço de aplicativo no portal do Azure.

![Serviço de Azure App no portal do Azure](./load-tests/_static/azure-appservice-perf-test.png)

O teste pode ser um teste manual com uma URL especificada ou um arquivo de teste da Web do Visual Studio, que pode testar várias URLs.

![Nova página de teste de desempenho no portal do Azure](./load-tests/_static/azure-appservice-perf-test-config.png)

No final do teste, os relatórios gerados mostram as características de desempenho do aplicativo. As estatísticas de exemplo incluem:

* Tempo médio de resposta
* Taxa de transferência máxima: solicitações por segundo
* Percentual de falha

## <a name="third-party-tools"></a>Ferramentas de terceiros

A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [Websurto de vento oeste](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
