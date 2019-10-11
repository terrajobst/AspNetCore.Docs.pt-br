---
title: Práticas recomendadas de desempenho de ASP.NET Core
author: mjrousos
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core e evitar problemas comuns de desempenho.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/26/2019
uid: performance/performance-best-practices
ms.openlocfilehash: c239c6d86e460f8fb80dfc47b88c090a796c617d
ms.sourcegitcommit: c452e6af92e130413106c4863193f377cde4cd9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246484"
---
# <a name="aspnet-core-performance-best-practices"></a>Práticas recomendadas de desempenho de ASP.NET Core

Por [Mike Rousos](https://github.com/mjrousos)

Este artigo fornece diretrizes para práticas recomendadas de desempenho com o ASP.NET Core.

## <a name="cache-aggressively"></a>Cache agressivamente

O Caching é discutido em várias partes deste documento. Para obter mais informações, consulte <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Entender os caminhos de código quente

Neste documento, um *caminho de código quente* é definido como um caminho de código que é chamado com frequência e onde ocorre grande parte do tempo de execução. Os caminhos de código quente normalmente limitam o desempenho e a expansão do aplicativo e são discutidos em várias partes deste documento.

## <a name="avoid-blocking-calls"></a>Evite chamadas de bloqueio

ASP.NET Core aplicativos devem ser criados para processar várias solicitações simultaneamente. As APIs assíncronas permitem que um pequeno pool de threads lide com milhares de solicitações simultâneas, não aguardando chamadas de bloqueio. Em vez de aguardar a conclusão de uma tarefa síncrona de execução longa, o thread pode trabalhar em outra solicitação.

Um problema de desempenho comum no ASP.NET Core aplicativos é bloquear chamadas que poderiam ser assíncronas. Muitas chamadas de bloqueio síncronos levam à [privação do pool de threads](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) e tempos de resposta degradados.

**Não:**

* Bloquear a execução assíncrona chamando [Task. Wait](/dotnet/api/system.threading.tasks.task.wait) ou [Task. Result](/dotnet/api/system.threading.tasks.task-1.result).
* Adquirir bloqueios em caminhos de código comuns. Os aplicativos ASP.NET Core são mais com o melhor desempenho quando arquitetados para executar código em paralelo.

**Fazer**:

* Torne os [caminhos de código dinâmico](#understand-hot-code-paths) assíncronos.
* Chame o acesso a dados e APIs de operações de longa execução de forma assíncrona.
* Torne as ações de página do controlador/Razor assíncronas. A pilha de chamadas inteira é assíncrona para se beneficiar de padrões [Async/Await](/dotnet/csharp/programming-guide/concepts/async/) .

Um criador de perfil, como [Perfview](https://github.com/Microsoft/perfview), pode ser usado para localizar threads frequentemente adicionados ao [pool de threads](/windows/desktop/procthread/thread-pools). O evento `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` indica um thread adicionado ao pool de threads. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Minimizar alocações de objeto grande

O [coletor de lixo do .NET Core](/dotnet/standard/garbage-collection/) gerencia a alocação e a liberação da memória automaticamente em aplicativos ASP.NET Core. A coleta de lixo automática geralmente significa que os desenvolvedores não precisam se preocupar com o modo como ou quando a memória é liberada. No entanto, a limpeza de objetos não referenciados leva tempo de CPU, de modo que os desenvolvedores devem minimizar a alocação de objetos em [caminhos de código quente](#understand-hot-code-paths). A coleta de lixo é especialmente cara em objetos grandes (> 85 K bytes). Os objetos grandes são armazenados na [heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap) e exigem uma coleta de lixo completa (geração 2) para limpeza. Diferentemente das coleções de geração 0 e geração 1, uma coleção de geração 2 requer uma suspensão temporária da execução do aplicativo. A alocação e a desalocação frequentes de objetos grandes podem causar um desempenho inconsistente.

Recommendations

* **Considere armazenar** em cache objetos grandes que são usados com frequência. O Caching de objetos grandes impede alocações caras.
* **Faça** buffers de pool usando um [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) para armazenar matrizes grandes.
* **Não** aloque muitos objetos grandes de curta duração em caminhos de [código quente](#understand-hot-code-paths).

Problemas de memória, como os anteriores, podem ser diagnosticados examinando as estatísticas de GC (coleta de lixo) em [Perfview](https://github.com/Microsoft/perfview) e examinando:

* Tempo de pausa da coleta de lixo.
* Qual percentual do tempo do processador é gasto na coleta de lixo.
* Quantas coletas de lixo são de geração 0, 1 e 2.

Para obter mais informações, consulte [coleta de lixo e desempenho](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access"></a>Otimizar o acesso a dados

As interações com um armazenamento de dados e outros serviços remotos geralmente são as partes mais lentas de um aplicativo ASP.NET Core. Ler e gravar dados com eficiência é essencial para um bom desempenho.

Recommendations

* **Chame todas** as APIs de acesso a dados de forma assíncrona.
* **Não** recupere mais dados do que o necessário. Escreva consultas para retornar apenas os dados necessários para a solicitação HTTP atual.
* **Considere o** armazenamento em cache de dados acessados com frequência recuperados de um banco de dados ou serviço remoto se forem aceitáveis insuficientes. Dependendo do cenário, use um [MemoryCache](xref:performance/caching/memory) ou um [DistributedCache](xref:performance/caching/distributed). Para obter mais informações, consulte <xref:performance/caching/response>.
* **Minimize viagens** de ida e volta da rede. O objetivo é recuperar os dados necessários em uma única chamada em vez de várias chamadas.
* **Use** [consultas sem controle](/ef/core/querying/tracking#no-tracking-queries) no Entity Framework Core ao acessar dados para fins somente leitura. EF Core pode retornar os resultados de consultas sem controle com mais eficiência.
* **Filtre e** AGREGUE consultas LINQ (com instruções `.Where`, `.Select` ou `.Sum`, por exemplo) para que a filtragem seja executada pelo banco de dados.
* **Considere que** EF Core resolve alguns operadores de consulta no cliente, o que pode levar a uma execução de consulta ineficiente. Para obter mais informações, consulte [problemas de desempenho de avaliação do cliente](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Não use consultas** de projeção em coleções, o que pode resultar na execução de consultas SQL "N + 1". Para obter mais informações, consulte [otimização de Subconsultas correlacionadas](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Consulte [alto desempenho do EF](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) para obter abordagens que podem melhorar o desempenho em aplicativos de grande escala:

* [Pooling de DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Consultas compiladas explicitamente](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

É recomendável medir o impacto das abordagens de alto desempenho anteriores antes de confirmar a base de código. A complexidade adicional das consultas compiladas pode não justificar a melhoria do desempenho.

Problemas de consulta podem ser detectados examinando o tempo gasto acessando dados com [Application insights](/azure/application-insights/app-insights-overview) ou com ferramentas de criação de perfil. A maioria dos bancos de dados também disponibiliza estatísticas sobre consultas executadas com frequência.

## <a name="pool-http-connections-with-httpclientfactory"></a>Conexões HTTP do pool com HttpClientFactory

Embora o [HttpClient](/dotnet/api/system.net.http.httpclient) implemente a interface `IDisposable`, ele foi projetado para reutilização. As instâncias `HttpClient` fechadas deixam os soquetes abertos no estado de `TIME_WAIT` por um curto período de tempo. Se um caminho de código que cria e descarta objetos `HttpClient` é usado com frequência, o aplicativo pode esgotar os soquetes disponíveis. O [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) foi introduzido no ASP.NET Core 2,1 como uma solução para esse problema. Ele lida com o pool de conexões HTTP para otimizar o desempenho e a confiabilidade.

Recommendations

* **Não crie nem** descarte as instâncias `HttpClient` diretamente.
* **Use** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) para recuperar instâncias `HttpClient`. Para obter mais informações, consulte [usar o HttpClientFactory para implementar solicitações HTTP resilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Mantenha os caminhos de código comuns rápidos

Você quer que todo o seu código seja rápido, freqüentemente chamados de caminhos de código são os mais importantes para otimizar:

* Componentes de middleware no pipeline de processamento de solicitação do aplicativo, especialmente o middleware é executado no início do pipeline. Esses componentes têm um grande impacto no desempenho.
* Código que é executado para cada solicitação ou várias vezes por solicitação. Por exemplo, log personalizado, manipuladores de autorização ou inicialização de serviços transitórios.

Recommendations

* **Não use componentes** de middleware personalizados com tarefas de execução longa.
* **Use ferramentas** de criação de perfil de desempenho, como o [Visual Studio ferramentas de diagnóstico](/visualstudio/profiling/profiling-feature-tour) ou [Perfview](https://github.com/Microsoft/perfview)), para identificar [caminhos de código quente](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Concluir tarefas de execução longa fora de solicitações HTTP

A maioria das solicitações para um aplicativo ASP.NET Core pode ser tratada por um controlador ou modelo de página chamando os serviços necessários e retornando uma resposta HTTP. Para algumas solicitações que envolvem tarefas de longa execução, é melhor tornar o processo de solicitação/resposta assíncrono completo.

Recommendations

* **Não** aguarde a conclusão de tarefas de execução longa como parte do processamento de solicitação HTTP comum.
* **Considere manipular** solicitações de execução longa com [serviços em segundo plano](xref:fundamentals/host/hosted-services) ou fora do processo com uma [função do Azure](/azure/azure-functions/). Concluir o trabalho fora do processo é especialmente benéfico para tarefas com uso intensivo de CPU.
* **Use opções** de comunicação em tempo real, como [signalr](xref:signalr/introduction), para se comunicar com os clientes de forma assíncrona.

## <a name="minify-client-assets"></a>Ativos do cliente reduzir

ASP.NET Core aplicativos com front-ends complexos frequentemente atendem a muitos arquivos JavaScript, CSS ou de imagem. O desempenho das solicitações de carregamento inicial pode ser melhorado pelo:

* Agrupamento, que combina vários arquivos em um.
* Minificar, que reduz o tamanho dos arquivos removendo espaços em branco e comentários.

Recommendations

* **Use o** [suporte interno](xref:client-side/bundling-and-minification) do ASP.NET Core para agrupar e minificar ativos do cliente.
* **Considere outras** ferramentas de terceiros, como o [webpack](https://webpack.js.org/), para o gerenciamento de ativos de cliente complexo.

## <a name="compress-responses"></a>Compactar respostas

 Reduzir o tamanho da resposta geralmente aumenta a capacidade de resposta de um aplicativo, muitas vezes drasticamente. Uma maneira de reduzir os tamanhos de carga é compactar as respostas de um aplicativo. Para obter mais informações, consulte [compactação de resposta](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Usar a versão mais recente do ASP.NET Core

Cada nova versão do ASP.NET Core inclui melhorias de desempenho. Otimizações no .NET Core e ASP.NET Core significam que as versões mais recentes geralmente superam as versões mais antigas. Por exemplo, o .NET Core 2,1 adicionou suporte para expressões regulares compiladas e se beneficiou do [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx). ASP.NET Core 2,2 adicionado suporte para HTTP/2. [ASP.NET Core 3,0 adiciona muitas melhorias](xref:aspnetcore-3.0) que reduzem o uso de memória e melhoram a taxa de transferência. Se o desempenho for uma prioridade, considere atualizar para a versão atual do ASP.NET Core.

## <a name="minimize-exceptions"></a>Minimizar exceções

As exceções devem ser raras. Lançar e capturar exceções é lento em relação a outros padrões de fluxo de código. Por isso, as exceções não devem ser usadas para controlar o fluxo normal do programa.

Recommendations

* **Não** use exceções de lançamento ou captura como meio de fluxo de programa normal, especialmente em [caminhos de código quente](#understand-hot-code-paths).
* **Inclua a** lógica no aplicativo para detectar e manipular condições que poderiam causar uma exceção.
* **Gerar ou** capturar exceções para condições incomuns ou inesperadas.

As ferramentas de diagnóstico do aplicativo, como o Application Insights, podem ajudar a identificar exceções comuns em um aplicativo que pode afetar o desempenho.

## <a name="performance-and-reliability"></a>Desempenho e confiabilidade

As seções a seguir fornecem dicas de desempenho e problemas e soluções de confiabilidade conhecidas.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Evitar leitura ou gravação síncrona no corpo de HttpRequest/HttpResponse

Todas as e/s no ASP.NET Core é assíncrona. Os servidores implementam a interface `Stream`, que tem sobrecargas síncronas e assíncronas. Os itens assíncronos devem ser preferidos para evitar o bloqueio de threads do pool de threads O bloqueio de threads pode levar à privação do pool de threads.

**Não faça isso:** O exemplo a seguir usa <xref:System.IO.StreamReader.ReadToEnd*>. Ele bloqueia o thread atual para aguardar o resultado. Este é um exemplo de [sync sobre Async @ no__t-1.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

No código anterior, `Get` lê de forma síncrona todo o corpo da solicitação HTTP na memória. Se o cliente estiver carregando lentamente, o aplicativo estará fazendo a sincronização por meio de Async. O aplicativo é sincronizado por Async porque Kestrel **não oferece suporte a** Leituras síncronas.

**Faça o seguinte:** O exemplo a seguir usa <xref:System.IO.StreamReader.ReadToEndAsync*> e não bloqueia o thread durante a leitura.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

O código anterior lê de forma assíncrona todo o corpo da solicitação HTTP na memória.

> [!WARNING]
> Se a solicitação for grande, a leitura do corpo de solicitação HTTP inteiro na memória poderá levar a uma condição de OOM (memória insuficiente). OOM pode resultar em uma negação de serviço.  Para obter mais informações, consulte [evitar a leitura de corpos de solicitação grandes ou corpos de resposta na memória](#arlb) deste documento.

## <a name="prefer-readasformasync-over-requestform"></a>Preferir ReadAsFormAsync sobre solicitação. Form

Use `HttpContext.Request.ReadFormAsync` em vez de `HttpContext.Request.Form`.
`HttpContext.Request.Form` pode ser lido com segurança apenas com as seguintes condições:

* O formulário foi lido por uma chamada para `ReadAsFormAsync` e
* O valor de formulário armazenado em cache está sendo lido usando `HttpContext.Request.Form`

**Não faça isso:** O exemplo a seguir usa `HttpContext.Request.Form`.  `HttpContext.Request.Form` usa [sync sobre Async @ no__t-2 e pode levar à privação do pool de threads.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Faça o seguinte:** O exemplo a seguir usa `HttpContext.Request.ReadFormAsync` para ler o corpo do formulário de forma assíncrona.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Evite ler corpos de solicitação grandes ou corpos de resposta na memória

No .NET, cada alocação de objeto maior que 85 KB termina na[Loh](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)(Large Object heap). Os objetos grandes são caros de duas maneiras:

* O custo de alocação é alto porque a memória para um objeto grande alocado recentemente deve ser apagada. O CLR garante que a memória para todos os objetos alocados recentemente seja apagada.
* O LOH é coletado com o restante do heap. O LOH requer uma coleta de [lixo](/dotnet/standard/garbage-collection/fundamentals) completa ou uma [coleção de Gen2](/dotnet/standard/garbage-collection/fundamentals#generations).

Esta [postagem de blog](https://adamsitnik.com/Array-Pool/#the-problem) descreve o problema sucintamente:

> Quando um objeto grande é alocado, ele é marcado como um objeto Gen 2. Não Gen 0 como para objetos pequenos. As consequências são que, se você ficar sem memória no LOH, o GC limpará todo o heap gerenciado, não apenas o LOH. Portanto, ele limpa Gen 0, Gen 1 e Gen 2, incluindo LOH. Isso é chamado de coleta de lixo completa e é a coleta de lixo mais demorada. Para muitos aplicativos, pode ser aceitável. Mas, definitivamente, não para servidores Web de alto desempenho, onde poucos buffers de memória Big são necessários para lidar com uma solicitação da Web média (leitura de um soquete, descompactar, decodificar JSON & mais).

Naively armazenando um corpo de solicitação ou resposta grande em um único `byte[]` ou `string`:

* Pode resultar na execução rápida do espaço no LOH.
* Pode causar problemas de desempenho para o aplicativo devido a todos os GCs em execução.

## <a name="working-with-a-synchronous-data-processing-api"></a>Trabalhando com uma API de processamento de dados síncrono

Ao usar um serializador/desserializador que dá suporte apenas a leituras e gravações síncronas (por exemplo, [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Armazenar em buffer os dados na memória de forma assíncrona antes de passá-los para o serializador/desserializador.

> [!WARNING]
> Se a solicitação for grande, ela poderá levar a uma condição de OOM (memória insuficiente). OOM pode resultar em uma negação de serviço.  Para obter mais informações, consulte [evitar a leitura de corpos de solicitação grandes ou corpos de resposta na memória](#arlb) deste documento.

ASP.NET Core 3,0 usa <xref:System.Text.Json> por padrão para serialização JSON. <xref:System.Text.Json>:

* Lê e grava JSON de forma assíncrona.
* É otimizado para texto UTF-8.
* Normalmente, maior desempenho do que `Newtonsoft.Json`.

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Não armazenar IHttpContextAccessor. HttpContext em um campo

O [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) retorna o `HttpContext` da solicitação ativa quando acessado a partir do thread de solicitação. O `IHttpContextAccessor.HttpContext` **não** deve ser armazenado em um campo ou variável.

**Não faça isso:** O exemplo a seguir armazena o `HttpContext` em um campo e tenta usá-lo mais tarde.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

O código anterior freqüentemente captura um @no__t nulo ou incorreto no construtor.

**Faça o seguinte:** O exemplo a seguir:

* Armazena o <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> em um campo.
* Usa o campo `HttpContext` na hora correta e verifica `null`.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Não acessar o HttpContext de vários threads

`HttpContext` *não* é thread-safe. O acesso a `HttpContext` de vários threads em paralelo pode resultar em um comportamento indefinido, como travamentos, falhas e corrupção de dados.

**Não faça isso:** O exemplo a seguir faz três solicitações paralelas e registra o caminho de solicitação de entrada antes e depois da solicitação HTTP de saída. O caminho da solicitação é acessado de vários threads, potencialmente em paralelo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Faça o seguinte:** O exemplo a seguir copia todos os dados da solicitação de entrada antes de fazer as três solicitações paralelas.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Não use o HttpContext após a conclusão da solicitação

`HttpContext` só é válido contanto que haja uma solicitação HTTP ativa no pipeline de ASP.NET Core. Todo o pipeline de ASP.NET Core é uma cadeia assíncrona de delegados que executa cada solicitação. Quando o `Task` retornado dessa cadeia for concluído, o `HttpContext` será reciclado.

**Não faça isso:** O exemplo a seguir usa `async void`:

* Que é **sempre** uma prática inadequada em aplicativos ASP.NET Core.
* Acessa o `HttpResponse` depois que a solicitação HTTP é concluída.
* Falha no processo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Faça o seguinte:** O exemplo a seguir retorna um `Task` para a estrutura para que a solicitação HTTP não seja concluída até que a ação seja concluída.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Não Capture o HttpContext em threads em segundo plano

**Não faça isso:** O exemplo a seguir mostra que um fechamento está capturando o `HttpContext` da propriedade `Controller`. Esta é uma prática inadequada porque o item de trabalho pode:

* Executar fora do escopo da solicitação.
* Tentativa de ler o `HttpContext` errado.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Faça o seguinte:** O exemplo a seguir:

* Copia os dados necessários na tarefa em segundo plano durante a solicitação.
* Não faz referência a nada do controlador.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Não capture os serviços injetados nos controladores em threads em segundo plano

**Não faça isso:** O exemplo a seguir mostra que um fechamento está capturando o `DbContext` do parâmetro de ação `Controller`. Esta é uma prática inadequada.  O item de trabalho pode ser executado fora do escopo da solicitação. O `ContosoDbContext` tem como escopo a solicitação, resultando em um `ObjectDisposedException`.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Faça o seguinte:** O exemplo a seguir:

* Injeta um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> para criar um escopo no item de trabalho em segundo plano. `IServiceScopeFactory` é um singleton.
* Cria um novo escopo de injeção de dependência no thread em segundo plano.
* Não faz referência a nada do controlador.
* Não captura o `ContosoDbContext` da solicitação de entrada.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

O seguinte código realçado:

* Cria um escopo para o tempo de vida da operação em segundo plano e resolve os serviços dele.
* Usa `ContosoDbContext` do escopo correto.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Não modifique o código ou os cabeçalhos de status depois que o corpo da resposta for iniciado

ASP.NET Core não armazena em buffer o corpo da resposta HTTP. Na primeira vez em que a resposta é gravada:

* Os cabeçalhos são enviados junto com essa parte do corpo para o cliente.
* Não é mais possível alterar cabeçalhos de resposta.

**Não faça isso:** O código a seguir tenta adicionar cabeçalhos de resposta depois que a resposta já tiver sido iniciada:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

No código anterior, `context.Response.Headers["test"] = "test value";` gerará uma exceção se `next()` tiver sido gravado na resposta.

**Faça o seguinte:** O exemplo a seguir verifica se a resposta HTTP foi iniciada antes de modificar os cabeçalhos.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Faça o seguinte:** O exemplo a seguir usa `HttpResponse.OnStarting` para definir os cabeçalhos antes que os cabeçalhos de resposta sejam liberados para o cliente.

Verificar se a resposta não foi iniciada permite registrar um retorno de chamada que será invocado logo antes que os cabeçalhos de resposta sejam gravados. Verificando se a resposta não foi iniciada:

* Fornece a capacidade de acrescentar ou substituir cabeçalhos just-in-time.
* Não requer conhecimento do próximo middleware no pipeline.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Não chame Next () se você já tiver iniciado a gravação no corpo da resposta

Os componentes só esperam ser chamados se for possível que eles manipulem e manipulem a resposta.
