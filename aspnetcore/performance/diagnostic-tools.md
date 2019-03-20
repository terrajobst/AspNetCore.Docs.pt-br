---
title: Ferramentas de diagnóstico de desempenho
author: mjrousos
description: Ferramentas úteis para diagnosticar problemas de desempenho em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 12/07/2018
uid: performance/diagnostic-tools
ms.openlocfilehash: 0fb9944d1082090f4f58f641a26ee96286920456
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209996"
---
# <a name="performance-diagnostic-tools"></a>Ferramentas de diagnóstico de desempenho

Por [Mike Rousos](https://github.com/mjrousos)

Este artigo lista as ferramentas para diagnosticar problemas de desempenho no ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Ferramentas de diagnóstico do Visual Studio

O [ferramentas de criação de perfil e diagnóstico](/visualstudio/profiling) incorporado ao Visual Studio são um bom lugar para começar a investigar problemas de desempenho. Essas ferramentas são poderosas e conveniente usar o ambiente de desenvolvimento do Visual Studio. As ferramentas permite que a análise de uso de CPU, uso de memória e eventos de desempenho em aplicativos ASP.NET Core. Interno que está sendo torna a criação de perfil fácil em tempo de desenvolvimento.

Mais informações estão disponíveis no [documentação do Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Informações do aplicativo

[Application Insights](/azure/application-insights/app-insights-overview) fornece dados de desempenho detalhados para seu aplicativo. Application Insights coleta automaticamente dados em taxas de resposta, taxas de falha, os tempos de resposta de dependência e muito mais. Application Insights dá suporte ao registro em log eventos personalizados e métricas específicas para seu aplicativo.

O Azure Application Insights fornece várias maneiras de fornecer informações nos aplicativos monitorados:

- [Mapa do aplicativo](/azure/application-insights/app-insights-app-map) – ajuda a identificar gargalos de desempenho ou falha pontos em todos os componentes de aplicativos distribuídos.
- [Folha métricas no portal do Application Insights](/azure/application-insights/app-insights-metrics-explorer?toc=/azure/azure-monitor/toc.json) mostra valores de medida e contagens de eventos.
- [Folha de desempenho no portal do Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Mostra detalhes de desempenho para operações diferentes no aplicativo monitorado.
  - Permite que o drill down em uma única operação para verificar todas as partes/dependências que contribuem para um longo tempo.
  - Profiler pode ser invocado a partir daqui para coletar rastreamentos de desempenho sob demanda.

- [De do Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) permite regular e sob demanda de criação de perfil de aplicativos .NET.  Mostra portal do Azure capturado rastreamentos de desempenho com as pilhas de chamadas e caminhos de acesso. Os arquivos de rastreamento também podem ser baixados para análise mais profunda com o PerfView.

Application Insights podem ser usados em ambientes de uma variedade:

- Otimizado para funcionar no Azure.
- Funciona em produção, desenvolvimento e preparo.
- Funciona localmente a partir [Visual Studio](/azure/application-insights/app-insights-visual-studio) ou em outros ambientes de hospedagem.

Para obter mais informações, veja [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[O PerfView](https://github.com/Microsoft/perfview) é uma ferramenta de análise de desempenho criada pela equipe do .NET especificamente para diagnosticar problemas de desempenho do .NET. O PerfView permite que a análise do CPU uso, memória e GC comportamento, eventos de desempenho e a hora do relógio.

Você pode aprender mais sobre como começar com e o PerfView [tutoriais em vídeo PerfView](http://channel9.msdn.com/Series/PerfView-Tutorial) ou ao ler o guia do usuário disponível na ferramenta ou [no GitHub](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consiste em dois componentes: Windows Performance Recorder (WPR) e o Windows Performance Analyzer (WPA). As ferramentas de produzem perfis detalhados de desempenho de aplicativos e sistemas de operacionais do Windows. WPT tem maneiras mais ricas de visualização de dados, mas suas coleta de dados são menos eficientes do que do PerfView.

## <a name="perfcollect"></a>PerfCollect

Enquanto o PerfView é uma ferramenta de análise de desempenho úteis para cenários do .NET, ele só é executado no Windows para que você não pode usá-lo para coletar rastreamentos de aplicativos do ASP.NET Core em execução em ambientes Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) é um script bash que usa as ferramentas de criação de perfil de Linux nativo ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) e [LTTng](https://lttng.org/)) para coletar rastreamentos no Linux que pode ser analisado por PerfView. PerfCollect é útil quando problemas de desempenho aparecem em ambientes Linux em que o PerfView não pode ser usado diretamente. Em vez disso, PerfCollect pode coletar rastreamentos de aplicativos .NET Core que, em seguida, são analisados em um computador Windows usando o PerfView.

Para obter mais informações sobre como instalar e começar a trabalhar com PerfCollect estão disponíveis [no GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Outras ferramentas de desempenho de terceiros

O exemplo a seguir lista algumas ferramentas de desempenho de terceiros que são úteis na investigação de desempenho de aplicativos .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- o dotTrace e dotMemory da JetBrains
- VTune da Intel
