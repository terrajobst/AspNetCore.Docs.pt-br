---
title: Ferramentas de diagnóstico de desempenho
author: mjrousos
description: Ferramentas úteis para diagnosticar problemas de desempenho em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: d273897b9ad26d57eb94b196b58f14019a96d07d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661073"
---
# <a name="performance-diagnostic-tools"></a>Ferramentas de Diagnóstico de desempenho

Por [Mike Rousos](https://github.com/mjrousos)

Este artigo lista as ferramentas para diagnosticar problemas de desempenho no ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Ferramentas de Diagnóstico do Visual Studio

As [ferramentas de criação de perfil e diagnóstico](/visualstudio/profiling) internas do Visual Studio são um bom lugar para começar a investigar problemas de desempenho. Essas ferramentas são poderosas e convenientes de usar no ambiente de desenvolvimento do Visual Studio. As ferramentas permitem a análise do uso da CPU, do uso de memória e dos eventos de desempenho em aplicativos ASP.NET Core. Ser interno facilita a criação de perfil no tempo de desenvolvimento.

Mais informações estão disponíveis na [documentação do Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application insights](/azure/application-insights/app-insights-overview) fornece dados de desempenho detalhados para seu aplicativo. Application Insights coleta automaticamente dados sobre taxas de resposta, taxas de falha, tempos de resposta de dependência e muito mais. O Application Insights dá suporte ao log de eventos personalizados e métricas específicos para seu aplicativo.

Aplicativo Azure insights fornece várias maneiras de fornecer informações sobre aplicativos monitorados:

- [Mapa de aplicativos](/azure/application-insights/app-insights-app-map) – ajuda a identificar gargalos de desempenho ou pontos de acesso de falha em todos os componentes de aplicativos distribuídos.
- O [Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar visualmente tendências e investigar picos e quedas em valores de métricas.
- [Folha desempenho no portal Application insights](/azure/application-insights/app-insights-tutorial-performance):

  - Mostra detalhes de desempenho para operações diferentes no aplicativo monitorado.
  - Permite detalhar uma única operação para verificar todas as partes/dependências que contribuem para uma longa duração.
  - O criador de perfil pode ser invocado aqui para coletar rastreamentos de desempenho sob demanda.

- [Aplicativo Azure o profiler insights](/azure/azure-monitor/app/profiler) permite a criação de perfil regular e sob demanda de aplicativos .net.  Portal do Azure mostra rastreamentos de desempenho capturados com pilhas de chamadas e Hot Paths. Os arquivos de rastreamento também podem ser baixados para análise mais profunda usando o PerfView.

Application Insights pode ser usado em vários ambientes:

- Otimizado para funcionar no Azure.
- Funciona em produção, desenvolvimento e preparo.
- Funciona localmente no [Visual Studio](/azure/application-insights/app-insights-visual-studio) ou em outros ambientes de hospedagem.

Para obter mais informações, consulte [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

O [Perfview](https://github.com/Microsoft/perfview) é uma ferramenta de análise de desempenho criada pela equipe do .net especificamente para diagnosticar problemas de desempenho do .net. O PerfView permite a análise do uso da CPU, do comportamento da memória e do GC, dos eventos de desempenho e do tempo do relógio de parede.

Você pode aprender mais sobre o PerfView e como começar a usar os [tutoriais de vídeo do Perfview](https://channel9.msdn.com/Series/PerfView-Tutorial) ou lendo o guia do usuário disponível na ferramenta ou [no GitHub](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Kit de ferramentas de desempenho do Windows

O [Kit de ferramentas de desempenho do Windows](/windows-hardware/test/wpt/) (WPT) consiste em dois componentes: gravador de desempenho do Windows (WPR) e o Windows Performance Analyzer (WPA). As ferramentas produzem perfis de desempenho detalhados de aplicativos e sistemas operacionais Windows. O WPT tem maneiras mais avançadas de Visualizar dados, mas sua coleta de dados é menos eficiente do que a PerfView.

## <a name="perfcollect"></a>PerfCollect

Embora o PerfView seja uma ferramenta de análise de desempenho útil para cenários .NET, ele só é executado no Windows para que você não possa usá-lo para coletar rastreamentos de aplicativos ASP.NET Core executados em ambientes Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) é um script bash que usa ferramentas de criação de perfil do Linux nativas ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) e [LTTng](https://lttng.org/)) para coletar rastreamentos no Linux que podem ser analisados pelo Perfview. O PerfCollect é útil quando problemas de desempenho aparecem em ambientes Linux em que PerfView não pode ser usado diretamente. Em vez disso, o PerfCollect pode coletar rastreamentos de aplicativos .NET Core que são analisados em um computador Windows usando o PerfView.

Mais informações sobre como instalar e começar a usar o PerfCollect estão disponíveis [no GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Outras ferramentas de desempenho de terceiros

Veja a seguir uma lista de algumas ferramentas de desempenho de terceiros que são úteis na investigação de desempenho de aplicativos .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace e dotMemory da JetBrains
- VTune da Intel
