---
title: ASP.NET Core teste de carga/estresse
author: Jeremy-Meng
description: Saiba mais sobre várias ferramentas e abordagens notáveis para teste de carga e teste de estresse ASP.NET Core aplicativos.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: edaa9e873e8e489f0c560c1736f81358ca1720d0
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289018"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core teste de carga/estresse

Testes de carga e testes de estresse são importantes para garantir que um aplicativo Web seja eficaz e escalonável. Suas metas são diferentes, embora elas geralmente compartilhem testes semelhantes.

**Testes de carga** &ndash; testar se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário e ainda atender à meta de resposta. O aplicativo é executado sob condições normais.

**Testes de estresse** &ndash; estabilidade do aplicativo de teste quando executados sob condições extremas, geralmente por um longo período de tempo. Os testes colocam alta carga de usuário, picos ou aumentam a carga gradualmente, no aplicativo, ou limitam os recursos de computação do aplicativo.

Testes de estresse determinam se um aplicativo sob estresse pode se recuperar de uma falha e retornar normalmente para o comportamento esperado. Sob estresse, o aplicativo não é executado sob condições normais.

O Visual Studio 2019 é a última versão do Visual Studio com recursos de teste de carga. Para clientes que precisam de ferramentas de teste de carga no futuro, recomendamos ferramentas alternativas, como Apache JMeter, Akamai CloudTest e BlazeMeter. Para obter mais informações, consulte as [notas de versão do Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Ferramentas do Visual Studio

O Visual Studio permite que os usuários criem, desenvolvam e depurem testes de carga e desempenho da Web. Uma opção está disponível para criar testes por meio da gravação de ações em um navegador da Web.

Para obter informações sobre como criar, configurar e executar projetos de teste de carga usando o Visual Studio 2017, consulte [início rápido: criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Os testes de carga podem ser configurados para serem executados no local ou executados na nuvem usando o Azure DevOps.

## <a name="third-party-tools"></a>Ferramentas de terceiros

A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [Websurto de vento oeste](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
