---
title: Práticas recomendadas de desempenho do ASP.NET Core
author: mjrousos
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core e evitar problemas comuns de desempenho.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 05/10/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 7651dff18f98c60057660c8946c3daa66d272f6a
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65536076"
---
# <a name="aspnet-core-performance-best-practices"></a>Práticas recomendadas de desempenho do ASP.NET Core

Por [Mike Rousos](https://github.com/mjrousos)

Este tópico fornece diretrizes para desempenho, as práticas recomendadas com o ASP.NET Core.

<a name="hot"></a>

Neste documento, uma *caminho de código a quente* é definido como um caminho de código que é chamado com frequência e em que grande parte do tempo de execução ocorre. Caminhos de código ativo normalmente limitam a expansão de aplicativo e o desempenho.

## <a name="cache-aggressively"></a>Armazenar em cache agressivamente

Armazenamento em cache é discutido em várias partes deste documento. Para obter mais informações, consulte <xref:performance/caching/response>.

## <a name="avoid-blocking-calls"></a>Evitar chamadas de bloqueio

Aplicativos ASP.NET Core devem ser projetados para processar muitas solicitações simultaneamente. APIs assíncronas permitem que um pequeno pool de threads para lidar com milhares de solicitações simultâneas por não aguardando chamadas de bloqueio. Em vez de esperar em uma tarefa de longa execução síncrona para ser concluída, o thread pode trabalhar em outra solicitação.

Um problema de desempenho comuns em aplicativos ASP.NET Core está bloqueando chamadas que poderiam ser assíncronas. Gerar muitas chamadas de bloqueio síncronas [privação de Pool de threads](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) e degradação de tempos de resposta.

**Não**:

* Bloquear a execução assíncrona chamando [Task. wait](/dotnet/api/system.threading.tasks.task.wait) ou [Task. Result](/dotnet/api/system.threading.tasks.task-1.result).
* Adquira bloqueios em caminhos de código comum. Aplicativos ASP.NET Core são mais eficazes quando projetado para executar código em paralelo.

**Fazer**:

* Tornar [hot caminhos de código](#hot) assíncrona.
* Chame APIs de operações de longa execução e de acesso a dados de forma assíncrona.
* Tornar o controlador/Razor ações de página assíncrono. A pilha de chamadas inteira é assíncrona para se beneficiar da [async/await](/dotnet/csharp/programming-guide/concepts/async/) padrões.

Um criador de perfil, como [PerfView](https://github.com/Microsoft/perfview), pode ser usado para localizar segmentos adicionados com frequência para o [Pool de threads](/windows/desktop/procthread/thread-pools). O `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` evento indica que um thread adicionado ao pool de threads. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>Minimizar as alocações de objeto grande

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core -->
O [coletor de lixo do .NET Core](/dotnet/standard/garbage-collection/) gerencia a alocação e liberação de memória automaticamente em aplicativos ASP.NET Core. Coleta de lixo automática geralmente significa que os desenvolvedores não precisam se preocupar sobre como ou quando a memória é liberada. No entanto, limpeza de objetos não referenciados leva tempo de CPU, portanto, os desenvolvedores devem minimizar alocando objetos no [hot caminhos de código](#hot). Coleta de lixo é especialmente dispendiosa em objetos grandes (> 85 mil bytes). Objetos grandes são armazenados na [heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap) e exigem uma completa (geração 2) a coleta de lixo para limpar. Ao contrário de geração 0 e coletas da geração 1, uma coleta de geração 2 requer uma suspensão temporária da execução do aplicativo. Frequente alocação e desalocação de objetos grandes podem causar inconsistência do desempenho.

Recomendações:

* **Fazer** armazenar em cache os objetos grandes que são usados com frequência. O cache de objetos grandes evita alocações caras.
* **Fazer** pool de buffers usando um [ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1) para armazenar as matrizes grandes.
* **Não o fazem** alocar curta duração, muitos objetos grandes na [hot caminhos de código](#hot).

Problemas de memória, como descrito acima, podem ser diagnosticados ao examinar estatísticas de (GC) coleta de lixo na [PerfView](https://github.com/Microsoft/perfview) e examinando:

* Tempo de pausa da coleta de lixo.
* Qual é a porcentagem do tempo do processador é gasto na coleta de lixo.
* Quantas coletas de lixo são a geração 0, 1 e 2.

Para obter mais informações, consulte [coleta de lixo e desempenho](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access"></a>Otimizar o acesso a dados

Interações com um armazenamento de dados e outros serviços remotos geralmente são as partes mais lentas de um aplicativo ASP.NET Core. Ler e gravar dados com eficiência é essencial para um bom desempenho.

Recomendações:

* **Fazer** chamar APIs de acesso a todos os dados de forma assíncrona.
* **Não** recuperar mais dados do que é necessário. Escreva consultas para retornar apenas os dados que é necessários para a solicitação HTTP atual.
* **Fazer** considerar o cache frequente acessados os dados recuperados do banco de dados ou serviço remoto se um pouco desatualizados dados forem aceitáveis. Dependendo do cenário, use uma [MemoryCache](xref:performance/caching/memory) ou um [DistributedCache](xref:performance/caching/distributed). Para obter mais informações, consulte <xref:performance/caching/response>.
* **Fazer** minimizar viagens de ida e volta de rede. O objetivo é recuperar os dados necessários em uma única chamada em vez de várias chamadas.
* **Fazer** usar [consultas sem controle](/ef/core/querying/tracking#no-tracking-queries) no Entity Framework Core ao acessar dados para propósitos de somente leitura. O EF Core pode retornar os resultados de consultas sem controle com mais eficiência.
* **Fazer** filtro e consultas de agregação LINQ (com `.Where`, `.Select`, ou `.Sum` instruções, por exemplo) para que a filtragem é executada pelo banco de dados.
* **Fazer** considerar que o EF Core resolve alguns operadores de consulta no cliente, que pode levar à execução de consulta ineficiente. Para obter mais informações, consulte [problemas de desempenho de avaliação do cliente](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Não** usar consultas de projeção em coleções, que podem resultar na execução de "N + 1" consultas SQL. Para obter mais informações, consulte [otimização de subconsultas correlacionadas](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Ver [EF alto desempenho](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) para abordagens que podem melhorar o desempenho em aplicativos de alta escala:

* [Pool de DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Consultas explicitamente compiladas](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

É recomendável medindo o impacto das abordagens de alto desempenho anteriores antes de confirmar a base de código. A complexidade adicional de consultas compiladas pode não justificar a melhoria de desempenho.

Consulta problemas podem ser detectados, examinando o tempo gasto acesso a dados com [Application Insights](/azure/application-insights/app-insights-overview) ou com ferramentas de criação de perfil. A maioria dos bancos de dados também disponibilizar as estatísticas relacionadas a consultas executadas com frequência.

## <a name="pool-http-connections-with-httpclientfactory"></a>HTTP do pool de conexões com HttpClientFactory

Embora [HttpClient](/dotnet/api/system.net.http.httpclient) implementa o `IDisposable` interface, ele é projetado para reutilização. Fechado `HttpClient` instâncias deixe soquetes abertos no `TIME_WAIT` estado por um curto período de tempo. Se um caminho de código que cria e descarta `HttpClient` objetos é usado com frequência, o aplicativo pode esgotar soquetes disponíveis. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) foi introduzido no ASP.NET Core 2.1 como uma solução para esse problema. Ele lida com o pooling de conexões de HTTP para otimizar o desempenho e confiabilidade.

Recomendações:

* **Não o fazem** criar e descartar `HttpClient` instâncias diretamente.
* **Fazer** usar [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) recuperar `HttpClient` instâncias. Para obter mais informações, consulte [HttpClientFactory de uso para implementar as solicitações HTTP resilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Manter os caminhos de código comuns rápidos

Você deseja que todos os seus códigos a ser rápido, com frequência chamado caminhos de código são mais importantes para otimizar:

* Componentes de middleware no pipeline de processamento de solicitação do aplicativo, especialmente middleware executado no início do pipeline. Esses componentes têm um grande impacto no desempenho.
* Código que é executado para cada solicitação ou várias vezes por solicitação. Por exemplo, registro em log personalizado, manipuladores de autorização ou inicialização de serviços temporários.

Recomendações:

* **Não** usar componentes de middleware personalizado com tarefas de longa execução.
* **Fazer** usar ferramentas de criação de perfil, como de desempenho [ferramentas de diagnóstico Visual Studio](/visualstudio/profiling/profiling-feature-tour) ou [PerfView](https://github.com/Microsoft/perfview)), para identificar [hot caminhos de código](#hot).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Concluir tarefas de execução longa fora de solicitações HTTP

A maioria das solicitações para um aplicativo ASP.NET Core podem ser tratadas por um controlador ou o modelo de página chamando serviços necessários e retornando uma resposta HTTP. Para algumas solicitações que envolvem tarefas de longa execução, é melhor tornar o processo de solicitação-resposta inteira assíncrona.

Recomendações:

* **Não** Aguarde até que as tarefas de longa execução ser concluída como parte do processamento de solicitação HTTP comuns.
* **Fazer** considere a manipulação de solicitações de execução longa com [serviços em segundo plano](xref:fundamentals/host/hosted-services) ou fora do processo com um [Azure Function](/azure/azure-functions/). A conclusão do trabalho fora do processo é especialmente útil para tarefas de uso intensivo de CPU.
* **Fazer** usar opções de comunicação em tempo real, como [SignalR](xref:signalr/introduction), para se comunicar com clientes de forma assíncrona.

## <a name="minify-client-assets"></a>Minificar ativos do cliente

Aplicativos ASP.NET Core com front-ends complexos com frequência servem muitos JavaScript, CSS ou arquivos de imagem. Desempenho de solicitações de carga inicial pode ser melhorado por:

* O agrupamento, que combina vários arquivos em um.
* Minificação, que reduz o tamanho dos arquivos, removendo o espaço em branco e comentários.

Recomendações:

* **Fazer** usar o ASP.NET Core [suporte interno](xref:client-side/bundling-and-minification) para agrupamento e minificação de ativos de cliente.
* **Fazer** considerar outras ferramentas de terceiros, como [Webpack](https://webpack.js.org/), para gerenciamento de ativos de cliente complexo.

## <a name="compress-responses"></a>Compactar respostas

 Reduzindo o tamanho da resposta geralmente aumenta a capacidade de resposta de um aplicativo, muitas vezes drasticamente. É uma maneira de reduzir os tamanhos do conteúdo compactar respostas do aplicativo. Para obter mais informações, consulte [compactação de resposta](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Use a versão mais recente do ASP.NET Core

Cada nova versão do ASP.NET Core inclui aprimoramentos de desempenho. Otimizações no .NET Core e ASP.NET Core significam que versões mais recentes geralmente superam as versões mais antigas. Por exemplo, o .NET Core 2.1 adicionado suporte para expressões regulares compiladas e se beneficiaram [ `Span<T>` ](https://msdn.microsoft.com/magazine/mt814808.aspx). Suporte do ASP.NET Core 2.2 adicionado para HTTP/2. Se o desempenho for uma prioridade, considere atualizar para a versão atual do ASP.NET Core.

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>Minimizar as exceções

As exceções devem ser raras. Lançar e capturar exceções são lento em relação a outros padrões de fluxo de código. Por isso, as exceções não devem ser usadas para controlar o fluxo normal do programa.

Recomendações:

* **Não o fazem** uso gerar ou capturar exceções como um meio de fluxo normal do programa, especialmente em [hot caminhos de código](#hot).
* **Fazer** incluir lógica no aplicativo para detectar e lidar com condições que poderiam causar uma exceção.
* **Fazer** lançar ou capturar exceções para condições incomuns ou inesperadas.

Ferramentas de diagnóstico do aplicativo, como o Application Insights podem ajudar a identificar exceções comuns em um aplicativo que pode afetar o desempenho.
