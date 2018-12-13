---
title: Ferramentas de diagnóstico de desempenho
author: mjrousos
description: Ferramentas úteis para diagnosticar problemas de desempenho em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 12/07/2018
uid: performance/diagnostic-tools
ms.openlocfilehash: 3093b7d646e4fa943334c7b1e70ddc007ab18780
ms.sourcegitcommit: 1ea1b4fc58055c62728143388562689f1ef96cb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329192"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="8342b-103">Ferramentas de diagnóstico de desempenho</span><span class="sxs-lookup"><span data-stu-id="8342b-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="8342b-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="8342b-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="8342b-105">Este artigo lista as ferramentas para diagnosticar problemas de desempenho no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8342b-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="8342b-106">Ferramentas de diagnóstico do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8342b-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="8342b-107">O [ferramentas de criação de perfil e diagnóstico](/visualstudio/profiling) incorporado ao Visual Studio são um bom lugar para começar a investigar problemas de desempenho.</span><span class="sxs-lookup"><span data-stu-id="8342b-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="8342b-108">Essas ferramentas são poderosas e conveniente usar o ambiente de desenvolvimento do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8342b-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="8342b-109">As ferramentas permite que a análise de uso de CPU, uso de memória e eventos de desempenho em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8342b-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span>

<span data-ttu-id="8342b-110">Mais informações estão disponíveis no [documentação do Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="8342b-110">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="8342b-111">Informações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="8342b-111">Application Insights</span></span>

<span data-ttu-id="8342b-112">[Application Insights](/azure/application-insights/app-insights-overview) fornece dados de desempenho detalhados para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8342b-112">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="8342b-113">Application Insights coleta automaticamente dados em taxas de resposta, taxas de falha, os tempos de resposta de dependência e muito mais.</span><span class="sxs-lookup"><span data-stu-id="8342b-113">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="8342b-114">Application Insights dá suporte ao registro em log eventos personalizados e métricas específicas para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8342b-114">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="8342b-115">Application Insights podem ser usados em ambientes de uma variedade:</span><span class="sxs-lookup"><span data-stu-id="8342b-115">Application Insights can be used in a variety environments:</span></span>

* <span data-ttu-id="8342b-116">Otimizado para funcionar no Azure.</span><span class="sxs-lookup"><span data-stu-id="8342b-116">Optimized to work in Azure.</span></span>
* <span data-ttu-id="8342b-117">Funciona em produção, desenvolvimento e preparo.</span><span class="sxs-lookup"><span data-stu-id="8342b-117">Works in production, development, and staging.</span></span>
* <span data-ttu-id="8342b-118">Funciona localmente a partir [Visual Studio](/azure/application-insights/app-insights-visual-studio) ou em outros ambientes de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="8342b-118">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="8342b-119">Para obter mais informações, veja [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="8342b-119">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="8342b-120">PerfView</span><span class="sxs-lookup"><span data-stu-id="8342b-120">PerfView</span></span>

<span data-ttu-id="8342b-121">[O PerfView](https://github.com/Microsoft/perfview) é uma ferramenta de análise de desempenho criada pela equipe do .NET especificamente para diagnosticar problemas de desempenho do .NET.</span><span class="sxs-lookup"><span data-stu-id="8342b-121">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="8342b-122">O PerfView permite que a análise do CPU uso, memória e GC comportamento, eventos de desempenho e a hora do relógio.</span><span class="sxs-lookup"><span data-stu-id="8342b-122">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="8342b-123">Você pode aprender mais sobre como começar com e o PerfView [tutoriais em vídeo PerfView](http://channel9.msdn.com/Series/PerfView-Tutorial) ou ao ler o guia do usuário disponível na ferramenta ou [no GitHub](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="8342b-123">You can learn more about PerfView and how to get started with [PerfView video tutorials](http://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="perfcollect"></a><span data-ttu-id="8342b-124">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="8342b-124">PerfCollect</span></span>

<span data-ttu-id="8342b-125">Enquanto o PerfView é uma ferramenta de análise de desempenho úteis para cenários do .NET, ele só é executado no Windows para que você não pode usá-lo para coletar rastreamentos de aplicativos do ASP.NET Core em execução em ambientes Linux.</span><span class="sxs-lookup"><span data-stu-id="8342b-125">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="8342b-126">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) é um script bash que usa as ferramentas de criação de perfil de Linux nativo ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) e [LTTng](https://lttng.org/)) para coletar rastreamentos no Linux que pode ser analisado por PerfView.</span><span class="sxs-lookup"><span data-stu-id="8342b-126">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="8342b-127">PerfCollect é útil quando problemas de desempenho aparecem em ambientes Linux em que o PerfView não pode ser usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="8342b-127">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="8342b-128">Em vez disso, PerfCollect pode coletar rastreamentos de aplicativos .NET Core que, em seguida, são analisados em um computador Windows usando o PerfView.</span><span class="sxs-lookup"><span data-stu-id="8342b-128">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="8342b-129">Para obter mais informações sobre como instalar e começar a trabalhar com PerfCollect estão disponíveis [no GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="8342b-129">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>
