---
title: Testes de carga/estresse do ASP.NET Core
author: Jeremy-Meng
description: Descreve várias ferramentas importantes e abordagens para testes de carga e aplicativos ASP.NET Core de teste de carga.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: 39632af2c92dac548c03e24d35a5e8a03e00890d
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209827"
---
# <a name="load-and-stress-testing-aspnet-core"></a>ASP.NET Core teste de estresse e carregar

Teste de carga e testes de estresse são importantes para garantir que um aplicativo web é eficaz e escalonável. Suas metas são diferentes, mesmo que eles compartilham muitas vezes testes semelhantes.

**Testes de carga**: Testa se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e ainda assim satisfazer a meta de resposta. O aplicativo é executado em condições normais.

**Testes de estresse**: Estabilidade do aplicativo de testes ao executar sob condições extremas e muitas vezes um longo período de tempo:

* Carga de usuário com altos – picos ou aumentando gradualmente.
* Recursos de computação limitados.

Sob carga excessiva, pode o aplicativo se recuperar de falha e normalmente retornar ao comportamento esperado? Sob carga excessiva, o aplicativo está *não* executado sob condições normais.

O Visual Studio 2019 será a última versão do Visual Studio com funcionalidades de teste de carga. Para clientes que exigem ferramentas de teste de carga, é recomendável usar ferramentas de teste de carga alternativas, assim como Apache JMeter, Akamai CloudTest e Blazemeter. Para obter mais informações, consulte o [notas de versão visualização do Visual Studio 2019](/visualstudio/releases/2019/release-notes-preview#test-tools).

A testes de carga service no DevOps do Azure terminará em 2020. Para obter mais informações, consulte [fim de serviço da vida útil de teste de carga baseado em nuvem](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Ferramentas do Visual Studio

Visual Studio permite aos usuários criar, desenvolver e depurar testes de carga e desempenho na web. Uma opção está disponível para criar testes gravando ações em um navegador da web.

[Início Rápido: Criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) mostra como criar, configurar e executar um teste de carga projetos usando o Visual Studio 2017.

Consulte [Recursos adicionais](#add) para obter mais informações.

Testes de carga podem ser configurados para executar no local ou executados na nuvem usando o DevOps do Azure.

## <a name="azure-devops"></a>Azure DevOps

Execuções de teste de carga podem ser iniciadas usando o [planos de teste do Azure DevOps](/azure/devops/test/load-test/index?view=vsts) service.

![Página de aterrissagem de teste de carga de DevOps do Azure](./load-tests/_static/azure-devops-load-test.png)

O serviço suporta os seguintes tipos de formato de teste:

* Teste do Visual Studio – teste da web criado no Visual Studio.
* Teste com base em arquivo HTTP – tráfego HTTP capturado dentro do arquivo morto é reproduzida durante o teste.
* [Teste com base na URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – permite especificar URLs para carregar testes, tipos de solicitação, cabeçalhos e cadeias de caracteres de consulta. Executar a configuração de parâmetros, como duração, padrão de carga, o número de usuários, etc., pode ser configurado.
* [Apache JMeter](https://jmeter.apache.org/) de teste.

## <a name="azure-portal"></a>Portal do Azure

[Portal do Azure permite configurar e executar testes de carga de aplicativos Web,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) diretamente a partir da guia de desempenho do serviço de aplicativo no portal do Azure.

![Serviço de aplicativo do Azure no Portal do Azure](./load-tests/_static/azure-appservice-perf-test.png)

O teste pode ser um teste manual com uma URL especificada, ou um arquivo de teste do Visual Studio Web, que pode testar várias URLs.

![Nova página de teste de desempenho no Portal do Azure](./load-tests/_static/azure-appservice-perf-test-config.png)

No final do teste, os relatórios são gerados para mostrar as características de desempenho do aplicativo. Estatísticas de exemplo incluem:

* Tempo médio de resposta
* Taxa de transferência máxima: solicitações por segundo
* Percentual de falha

## <a name="third-party-tools"></a>Ferramentas de terceiros

A lista a seguir contém as ferramentas de desempenho da web de terceiros com vários conjuntos de recursos:

* [Apache JMeter](https://jmeter.apache.org/) : Pacote de destaque completo de ferramentas de teste de carga. Limite de thread: precisa de um thread por usuário.
* [AB - servidor HTTP Apache ferramenta de benchmark](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/) : Ferramenta da área de trabalho com gravadores de GUI e de teste. Mais eficaz do que o JMeter.
* [Locust.IO](https://locust.io/) : Não é limitado por threads.

<a name="add"></a>

## <a name="additional-resources"></a>Recursos adicionais

[Série de blogs de teste de carga](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) por Charles Sterling. Com data, mas a maioria dos tópicos ainda é relevante.
