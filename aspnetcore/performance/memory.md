---
title: Gerenciamento de memória e padrões no ASP.NET Core
author: rick-anderson
description: Saiba como a memória é gerenciada em ASP.NET Core e como o coletor de lixo (GC) funciona.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: performance/memory
ms.openlocfilehash: dfc789d080beec09a4f0eb34c3809b9f2df0d4b5
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357272"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="ca65d-103">Gerenciamento de memória e coleta de lixo (GC) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca65d-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="ca65d-104">Por [Sébastien Ros](https://github.com/sebastienros) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca65d-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ca65d-105">O gerenciamento de memória é complexo, mesmo em uma estrutura gerenciada como o .NET.</span><span class="sxs-lookup"><span data-stu-id="ca65d-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="ca65d-106">Analisar e compreender os problemas de memória pode ser desafiador.</span><span class="sxs-lookup"><span data-stu-id="ca65d-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="ca65d-107">Este artigo:</span><span class="sxs-lookup"><span data-stu-id="ca65d-107">This article:</span></span>

* <span data-ttu-id="ca65d-108">Foi motivada por muitos *vazamentos de memória* e problemas de *GC não funcionar* .</span><span class="sxs-lookup"><span data-stu-id="ca65d-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="ca65d-109">A maioria desses problemas foi causada por não entender como o consumo de memória funciona no .NET Core ou não entender como ele é medido.</span><span class="sxs-lookup"><span data-stu-id="ca65d-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="ca65d-110">Demonstra o uso de memória problemática e sugere abordagens alternativas.</span><span class="sxs-lookup"><span data-stu-id="ca65d-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="ca65d-111">Como a coleta de lixo (GC) funciona no .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca65d-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="ca65d-112">O GC aloca segmentos de heap em que cada segmento é um intervalo contíguo de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="ca65d-113">Os objetos colocados no heap são categorizados em uma das três gerações: 0, 1 ou 2.</span><span class="sxs-lookup"><span data-stu-id="ca65d-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="ca65d-114">A geração determina a frequência com que o GC tenta liberar memória em objetos gerenciados que não são mais referenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="ca65d-115">As gerações com números menores são GC com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="ca65d-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="ca65d-116">Os objetos são movidos de uma geração para outra com base em seu tempo de vida.</span><span class="sxs-lookup"><span data-stu-id="ca65d-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="ca65d-117">Como os objetos residem mais tempo, eles são movidos para uma geração mais alta.</span><span class="sxs-lookup"><span data-stu-id="ca65d-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="ca65d-118">Conforme mencionado anteriormente, as gerações mais altas são GC com menos frequência.</span><span class="sxs-lookup"><span data-stu-id="ca65d-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="ca65d-119">Os objetos de vida de curto prazo sempre permanecem na geração 0.</span><span class="sxs-lookup"><span data-stu-id="ca65d-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="ca65d-120">Por exemplo, os objetos que são referenciados durante a vida útil de uma solicitação da Web são de vida curta.</span><span class="sxs-lookup"><span data-stu-id="ca65d-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="ca65d-121">[Singletons](xref:fundamentals/dependency-injection#service-lifetimes) de nível de aplicativo geralmente migram para a geração 2.</span><span class="sxs-lookup"><span data-stu-id="ca65d-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="ca65d-122">Quando um aplicativo ASP.NET Core é iniciado, o GC:</span><span class="sxs-lookup"><span data-stu-id="ca65d-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="ca65d-123">Reserva alguma memória para os segmentos de heap iniciais.</span><span class="sxs-lookup"><span data-stu-id="ca65d-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="ca65d-124">Confirma uma pequena parte da memória quando o tempo de execução é carregado.</span><span class="sxs-lookup"><span data-stu-id="ca65d-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="ca65d-125">As alocações de memória anteriores são feitas por motivos de desempenho.</span><span class="sxs-lookup"><span data-stu-id="ca65d-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="ca65d-126">O benefício de desempenho vem de segmentos de heap na memória contígua.</span><span class="sxs-lookup"><span data-stu-id="ca65d-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="ca65d-127">Chame GC. Reúnem</span><span class="sxs-lookup"><span data-stu-id="ca65d-127">Call GC.Collect</span></span>

<span data-ttu-id="ca65d-128">Chamando [GC. Coletar](xref:System.GC.Collect*) explicitamente:</span><span class="sxs-lookup"><span data-stu-id="ca65d-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="ca65d-129">**Não** deve ser feito por aplicativos de ASP.NET Core de produção.</span><span class="sxs-lookup"><span data-stu-id="ca65d-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="ca65d-130">É útil ao investigar vazamentos de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="ca65d-131">Ao investigar, o GC removeu todos os objetos pendente da memória, portanto, a memória pode ser medida.</span><span class="sxs-lookup"><span data-stu-id="ca65d-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="ca65d-132">Analisando o uso de memória de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="ca65d-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="ca65d-133">Ferramentas dedicadas podem ajudar a analisar o uso de memória:</span><span class="sxs-lookup"><span data-stu-id="ca65d-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="ca65d-134">Contando referências de objeto</span><span class="sxs-lookup"><span data-stu-id="ca65d-134">Counting object references</span></span>
- <span data-ttu-id="ca65d-135">Medindo quanto impacto o GC tem sobre o uso da CPU</span><span class="sxs-lookup"><span data-stu-id="ca65d-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="ca65d-136">Medindo o espaço de memória usado para cada geração</span><span class="sxs-lookup"><span data-stu-id="ca65d-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="ca65d-137">Use as seguintes ferramentas para analisar o uso de memória:</span><span class="sxs-lookup"><span data-stu-id="ca65d-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="ca65d-138">[dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): pode ser usado em máquinas de produção.</span><span class="sxs-lookup"><span data-stu-id="ca65d-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="ca65d-139">Analisar o uso de memória sem o depurador do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca65d-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="ca65d-140">Analisar o uso de memória no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca65d-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="ca65d-141">Detectando problemas de memória</span><span class="sxs-lookup"><span data-stu-id="ca65d-141">Detecting memory issues</span></span>

<span data-ttu-id="ca65d-142">O Gerenciador de tarefas pode ser usado para ter uma ideia da quantidade de memória que um aplicativo ASP.NET está usando.</span><span class="sxs-lookup"><span data-stu-id="ca65d-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="ca65d-143">O valor de memória do Gerenciador de tarefas:</span><span class="sxs-lookup"><span data-stu-id="ca65d-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="ca65d-144">Representa a quantidade de memória usada pelo processo ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ca65d-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="ca65d-145">Inclui os objetos de vida do aplicativo e outros consumidores de memória, como o uso de memória nativa.</span><span class="sxs-lookup"><span data-stu-id="ca65d-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="ca65d-146">Se o valor de memória do Gerenciador de tarefas aumentar indefinidamente e nunca achatar, o aplicativo terá um vazamento de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="ca65d-147">As seções a seguir demonstram e explicam vários padrões de uso de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="ca65d-148">Exemplo de aplicativo de uso de memória de vídeo</span><span class="sxs-lookup"><span data-stu-id="ca65d-148">Sample display memory usage app</span></span>

<span data-ttu-id="ca65d-149">O [aplicativo de exemplo MemoryLeak](https://github.com/sebastienros/memoryleak) está disponível no github.</span><span class="sxs-lookup"><span data-stu-id="ca65d-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="ca65d-150">O aplicativo MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="ca65d-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="ca65d-151">Inclui um controlador de diagnóstico que reúne a memória em tempo real e os dados do GC para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="ca65d-152">Tem uma página de índice que exibe os dados de memória e GC.</span><span class="sxs-lookup"><span data-stu-id="ca65d-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="ca65d-153">A página de índice é atualizada a cada segundo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="ca65d-154">Contém um controlador de API que fornece vários padrões de carga de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="ca65d-155">Não é uma ferramenta com suporte, no entanto, pode ser usada para exibir os padrões de uso de memória de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca65d-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="ca65d-156">Execute MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="ca65d-156">Run MemoryLeak.</span></span> <span data-ttu-id="ca65d-157">A memória alocada aumenta lentamente até que ocorra um GC.</span><span class="sxs-lookup"><span data-stu-id="ca65d-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="ca65d-158">A memória aumenta porque a ferramenta aloca um objeto personalizado para capturar dados.</span><span class="sxs-lookup"><span data-stu-id="ca65d-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="ca65d-159">A imagem a seguir mostra a página de índice MemoryLeak quando ocorre um GC de Gen 0.</span><span class="sxs-lookup"><span data-stu-id="ca65d-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="ca65d-160">O gráfico mostra 0 RPS (solicitações por segundo) porque nenhum ponto de extremidade de API do controlador de API foi chamado.</span><span class="sxs-lookup"><span data-stu-id="ca65d-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![gráfico anterior](memory/_static/0RPS.png)

<span data-ttu-id="ca65d-162">O gráfico exibe dois valores para o uso de memória:</span><span class="sxs-lookup"><span data-stu-id="ca65d-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="ca65d-163">Alocado: a quantidade de memória ocupada por objetos gerenciados</span><span class="sxs-lookup"><span data-stu-id="ca65d-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="ca65d-164">[Conjunto de trabalho](/windows/win32/memory/working-set): o conjunto de páginas no espaço de endereço virtual do processo que estão atualmente residentes na memória física.</span><span class="sxs-lookup"><span data-stu-id="ca65d-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="ca65d-165">O conjunto de trabalho mostrado é o mesmo valor que o Gerenciador de tarefas exibe.</span><span class="sxs-lookup"><span data-stu-id="ca65d-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="ca65d-166">Objetos transitórios</span><span class="sxs-lookup"><span data-stu-id="ca65d-166">Transient objects</span></span>

<span data-ttu-id="ca65d-167">A API a seguir cria uma instância de cadeia de caracteres de 10 KB e a retorna para o cliente.</span><span class="sxs-lookup"><span data-stu-id="ca65d-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="ca65d-168">Em cada solicitação, um novo objeto é alocado na memória e gravado na resposta.</span><span class="sxs-lookup"><span data-stu-id="ca65d-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="ca65d-169">As cadeias são armazenadas como caracteres UTF-16 no .NET para que cada caractere aceite 2 bytes na memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="ca65d-170">O grafo a seguir é gerado com uma carga relativamente pequena no para mostrar como as alocações de memória são afetadas pelo GC.</span><span class="sxs-lookup"><span data-stu-id="ca65d-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![gráfico anterior](memory/_static/bigstring.png)

<span data-ttu-id="ca65d-172">O gráfico anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="ca65d-172">The preceding chart shows:</span></span>

* <span data-ttu-id="ca65d-173">4K RPS (solicitações por segundo).</span><span class="sxs-lookup"><span data-stu-id="ca65d-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="ca65d-174">As coleções de GC de geração 0 ocorrem a cada dois segundos.</span><span class="sxs-lookup"><span data-stu-id="ca65d-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="ca65d-175">O conjunto de trabalho é constante em aproximadamente 500 MB.</span><span class="sxs-lookup"><span data-stu-id="ca65d-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="ca65d-176">A CPU é 12%.</span><span class="sxs-lookup"><span data-stu-id="ca65d-176">CPU is 12%.</span></span>
* <span data-ttu-id="ca65d-177">O consumo de memória e a versão (por meio de GC) são estáveis.</span><span class="sxs-lookup"><span data-stu-id="ca65d-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="ca65d-178">O gráfico a seguir é obtido com a taxa de transferência máxima que pode ser tratada pelo computador.</span><span class="sxs-lookup"><span data-stu-id="ca65d-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![gráfico anterior](memory/_static/bigstring2.png)

<span data-ttu-id="ca65d-180">O gráfico anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="ca65d-180">The preceding chart shows:</span></span>

* <span data-ttu-id="ca65d-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="ca65d-181">22K RPS</span></span>
* <span data-ttu-id="ca65d-182">As coleções de GC de geração 0 ocorrem várias vezes por segundo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="ca65d-183">As coleções de geração 1 são disparadas porque o aplicativo alocou significativamente mais memória por segundo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="ca65d-184">O conjunto de trabalho é constante em aproximadamente 500 MB.</span><span class="sxs-lookup"><span data-stu-id="ca65d-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="ca65d-185">A CPU é 33%.</span><span class="sxs-lookup"><span data-stu-id="ca65d-185">CPU is 33%.</span></span>
* <span data-ttu-id="ca65d-186">O consumo de memória e a versão (por meio de GC) são estáveis.</span><span class="sxs-lookup"><span data-stu-id="ca65d-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="ca65d-187">A CPU (33%) Não está superutilizado, portanto, a coleta de lixo pode acompanhar um grande número de alocações.</span><span class="sxs-lookup"><span data-stu-id="ca65d-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="ca65d-188">GC de estação de trabalho vs. servidor GC</span><span class="sxs-lookup"><span data-stu-id="ca65d-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="ca65d-189">O coletor de lixo do .NET tem dois modos diferentes:</span><span class="sxs-lookup"><span data-stu-id="ca65d-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="ca65d-190">**Estação**de trabalho GC: otimizada para o desktop.</span><span class="sxs-lookup"><span data-stu-id="ca65d-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="ca65d-191">**GC do servidor**.</span><span class="sxs-lookup"><span data-stu-id="ca65d-191">**Server GC**.</span></span> <span data-ttu-id="ca65d-192">O GC padrão para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca65d-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="ca65d-193">Otimizado para o servidor.</span><span class="sxs-lookup"><span data-stu-id="ca65d-193">Optimized for the server.</span></span>

<span data-ttu-id="ca65d-194">O modo GC pode ser definido explicitamente no arquivo de projeto ou no arquivo *runtimeconfig. JSON* do aplicativo publicado.</span><span class="sxs-lookup"><span data-stu-id="ca65d-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="ca65d-195">A marcação a seguir mostra a configuração `ServerGarbageCollection` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="ca65d-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="ca65d-196">Alterar `ServerGarbageCollection` no arquivo de projeto requer que o aplicativo seja recriado.</span><span class="sxs-lookup"><span data-stu-id="ca65d-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="ca65d-197">**Observação:** A coleta de lixo do servidor **não** está disponível em computadores com um único núcleo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="ca65d-198">Para obter mais informações, consulte <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="ca65d-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="ca65d-199">A imagem a seguir mostra o perfil de memória em um 5K RPS usando o GC de estação de trabalho.</span><span class="sxs-lookup"><span data-stu-id="ca65d-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![gráfico anterior](memory/_static/workstation.png)

<span data-ttu-id="ca65d-201">As diferenças entre este gráfico e a versão do servidor são significativas:</span><span class="sxs-lookup"><span data-stu-id="ca65d-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="ca65d-202">O conjunto de trabalho cai de 500 MB a 70 MB.</span><span class="sxs-lookup"><span data-stu-id="ca65d-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="ca65d-203">O GC faz coleções de geração 0 várias vezes por segundo, em vez de cada dois segundos.</span><span class="sxs-lookup"><span data-stu-id="ca65d-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="ca65d-204">O GC cai de 300 MB a 10 MB.</span><span class="sxs-lookup"><span data-stu-id="ca65d-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="ca65d-205">Em um ambiente de servidor Web típico, o uso da CPU é mais importante do que a memória, portanto, o GC do servidor é melhor.</span><span class="sxs-lookup"><span data-stu-id="ca65d-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="ca65d-206">Se a utilização de memória for alta e o uso da CPU for relativamente baixo, o GC de estação de trabalho poderá ser mais eficaz.</span><span class="sxs-lookup"><span data-stu-id="ca65d-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="ca65d-207">Por exemplo, alta densidade hospedando vários aplicativos Web em que a memória é escassa.</span><span class="sxs-lookup"><span data-stu-id="ca65d-207">For example, high density hosting several web apps where memory is scarce.</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="ca65d-208">Referências de objeto persistente</span><span class="sxs-lookup"><span data-stu-id="ca65d-208">Persistent object references</span></span>

<span data-ttu-id="ca65d-209">O GC não pode liberar objetos que são referenciados.</span><span class="sxs-lookup"><span data-stu-id="ca65d-209">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="ca65d-210">Os objetos que são referenciados, mas que não são mais necessários, resultam em um vazamento de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-210">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="ca65d-211">Se o aplicativo frequentemente alocar objetos e falhar em liberá-los depois que eles não forem mais necessários, o uso de memória aumentará ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-211">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="ca65d-212">A API a seguir cria uma instância de cadeia de caracteres de 10 KB e a retorna para o cliente.</span><span class="sxs-lookup"><span data-stu-id="ca65d-212">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="ca65d-213">A diferença com o exemplo anterior é que essa instância é referenciada por um membro estático, o que significa que ela nunca está disponível para coleta.</span><span class="sxs-lookup"><span data-stu-id="ca65d-213">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="ca65d-214">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="ca65d-214">The preceding code:</span></span>

* <span data-ttu-id="ca65d-215">É um exemplo de um vazamento de memória típico.</span><span class="sxs-lookup"><span data-stu-id="ca65d-215">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="ca65d-216">Com chamadas frequentes, faz com que a memória do aplicativo aumente até que o processo falhe com uma exceção `OutOfMemory`.</span><span class="sxs-lookup"><span data-stu-id="ca65d-216">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![gráfico anterior](memory/_static/eternal.png)

<span data-ttu-id="ca65d-218">Na imagem anterior:</span><span class="sxs-lookup"><span data-stu-id="ca65d-218">In the preceding image:</span></span>

* <span data-ttu-id="ca65d-219">O teste de carga do ponto de extremidade `/api/staticstring` causa um aumento linear na memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-219">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="ca65d-220">O GC tenta liberar memória à medida que a pressão de memória cresce, chamando uma coleção de geração 2.</span><span class="sxs-lookup"><span data-stu-id="ca65d-220">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="ca65d-221">O GC não pode liberar a memória vazada.</span><span class="sxs-lookup"><span data-stu-id="ca65d-221">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="ca65d-222">Alocada e conjunto de trabalho aumenta com o tempo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-222">Allocated and working set increase with time.</span></span>

<span data-ttu-id="ca65d-223">Alguns cenários, como caching, exigem que as referências de objeto sejam mantidas até que a pressão de memória as force a ser liberada.</span><span class="sxs-lookup"><span data-stu-id="ca65d-223">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="ca65d-224">A classe <xref:System.WeakReference> pode ser usada para esse tipo de código de cache.</span><span class="sxs-lookup"><span data-stu-id="ca65d-224">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="ca65d-225">Um objeto `WeakReference` é coletado sob pressões de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-225">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="ca65d-226">A implementação padrão de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> usa `WeakReference`.</span><span class="sxs-lookup"><span data-stu-id="ca65d-226">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="ca65d-227">Memória nativa</span><span class="sxs-lookup"><span data-stu-id="ca65d-227">Native memory</span></span>

<span data-ttu-id="ca65d-228">Alguns objetos do .NET Core dependem da memória nativa.</span><span class="sxs-lookup"><span data-stu-id="ca65d-228">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="ca65d-229">A memória nativa **não** pode ser coletada pelo GC.</span><span class="sxs-lookup"><span data-stu-id="ca65d-229">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="ca65d-230">O objeto .NET usando a memória nativa deve liberá-lo usando código nativo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-230">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="ca65d-231">O .NET fornece a interface <xref:System.IDisposable> para permitir que os desenvolvedores liberem memória nativa.</span><span class="sxs-lookup"><span data-stu-id="ca65d-231">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="ca65d-232">Mesmo se <xref:System.IDisposable.Dispose*> não for chamado, as classes implementadas corretamente chamarão `Dispose` quando o [finalizador](/dotnet/csharp/programming-guide/classes-and-structs/destructors) for executado.</span><span class="sxs-lookup"><span data-stu-id="ca65d-232">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="ca65d-233">Considere o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ca65d-233">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="ca65d-234">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) é uma classe gerenciada, portanto, qualquer instância será coletada no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="ca65d-234">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="ca65d-235">A imagem a seguir mostra o perfil de memória ao invocar a API de `fileprovider` continuamente.</span><span class="sxs-lookup"><span data-stu-id="ca65d-235">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![gráfico anterior](memory/_static/fileprovider.png)

<span data-ttu-id="ca65d-237">O gráfico anterior mostra um problema óbvio com a implementação dessa classe, pois ela mantém o aumento do uso da memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-237">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="ca65d-238">Esse é um problema conhecido que está sendo acompanhado nesse [problema](https://github.com/aspnet/Home/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="ca65d-238">This is a known problem that is being tracked in [this issue](https://github.com/aspnet/Home/issues/3110).</span></span>

<span data-ttu-id="ca65d-239">O mesmo vazamento pode ocorrer no código do usuário, de uma das seguintes formas:</span><span class="sxs-lookup"><span data-stu-id="ca65d-239">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="ca65d-240">Não está liberando a classe corretamente.</span><span class="sxs-lookup"><span data-stu-id="ca65d-240">Not releasing the class correctly.</span></span>
* <span data-ttu-id="ca65d-241">Esquecer de invocar o método `Dispose`dos objetos dependentes que devem ser descartados.</span><span class="sxs-lookup"><span data-stu-id="ca65d-241">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="ca65d-242">Heap de objetos grandes</span><span class="sxs-lookup"><span data-stu-id="ca65d-242">Large objects heap</span></span>

<span data-ttu-id="ca65d-243">A alocação de memória frequente/ciclos livres pode fragmentar a memória, especialmente ao alocar grandes partes de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-243">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="ca65d-244">Os objetos são alocados em blocos contíguos de memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-244">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="ca65d-245">Para atenuar a fragmentação, quando o GC libera memória, ele trys para desfragmentá-la.</span><span class="sxs-lookup"><span data-stu-id="ca65d-245">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="ca65d-246">Esse processo é chamado de **compactação**.</span><span class="sxs-lookup"><span data-stu-id="ca65d-246">This process is called **compaction**.</span></span> <span data-ttu-id="ca65d-247">A compactação envolve a movimentação de objetos.</span><span class="sxs-lookup"><span data-stu-id="ca65d-247">Compaction involves moving objects.</span></span> <span data-ttu-id="ca65d-248">Mover objetos grandes impõe uma penalidade de desempenho.</span><span class="sxs-lookup"><span data-stu-id="ca65d-248">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="ca65d-249">Por esse motivo, o GC cria uma zona de memória especial para objetos _grandes_ , chamada de Loh ( [heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap) ).</span><span class="sxs-lookup"><span data-stu-id="ca65d-249">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="ca65d-250">Os objetos maiores que 85.000 bytes (aproximadamente 83 KB) são:</span><span class="sxs-lookup"><span data-stu-id="ca65d-250">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="ca65d-251">Colocado no LOH.</span><span class="sxs-lookup"><span data-stu-id="ca65d-251">Placed on the LOH.</span></span>
* <span data-ttu-id="ca65d-252">Não compactado.</span><span class="sxs-lookup"><span data-stu-id="ca65d-252">Not compacted.</span></span>
* <span data-ttu-id="ca65d-253">Coletados durante a geração 2 GCs.</span><span class="sxs-lookup"><span data-stu-id="ca65d-253">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="ca65d-254">Quando o LOH estiver cheio, o GC irá disparar uma coleção de geração 2.</span><span class="sxs-lookup"><span data-stu-id="ca65d-254">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="ca65d-255">Coleções de geração 2:</span><span class="sxs-lookup"><span data-stu-id="ca65d-255">Generation 2 collections:</span></span>

* <span data-ttu-id="ca65d-256">São inerentemente lentas.</span><span class="sxs-lookup"><span data-stu-id="ca65d-256">Are inherently slow.</span></span>
* <span data-ttu-id="ca65d-257">Além disso, incorre no custo de disparar uma coleção em todas as outras gerações.</span><span class="sxs-lookup"><span data-stu-id="ca65d-257">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="ca65d-258">O código a seguir compacta o LOH imediatamente:</span><span class="sxs-lookup"><span data-stu-id="ca65d-258">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="ca65d-259">Consulte <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> para obter informações sobre como compactar o LOH.</span><span class="sxs-lookup"><span data-stu-id="ca65d-259">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="ca65d-260">Em contêineres que usam o .NET Core 3,0 e posterior, o LOH é compactado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="ca65d-260">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="ca65d-261">A seguinte API que ilustra esse comportamento:</span><span class="sxs-lookup"><span data-stu-id="ca65d-261">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="ca65d-262">O gráfico a seguir mostra o perfil de memória de chamar o ponto de extremidade `/api/loh/84975`, em carga máxima:</span><span class="sxs-lookup"><span data-stu-id="ca65d-262">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![gráfico anterior](memory/_static/loh1.png)

<span data-ttu-id="ca65d-264">O gráfico a seguir mostra o perfil de memória de chamar o ponto de extremidade `/api/loh/84976`, alocando *apenas mais um byte*:</span><span class="sxs-lookup"><span data-stu-id="ca65d-264">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![gráfico anterior](memory/_static/loh2.png)

<span data-ttu-id="ca65d-266">Observação: a estrutura de `byte[]` tem bytes de sobrecarga.</span><span class="sxs-lookup"><span data-stu-id="ca65d-266">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="ca65d-267">É por isso que 84.976 bytes dispara o limite de 85.000.</span><span class="sxs-lookup"><span data-stu-id="ca65d-267">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="ca65d-268">Comparando os dois gráficos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ca65d-268">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="ca65d-269">O conjunto de trabalho é semelhante para ambos os cenários, cerca de 450 MB.</span><span class="sxs-lookup"><span data-stu-id="ca65d-269">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="ca65d-270">As solicitações em LOH (84.975 bytes) mostram a maioria das coleções de 0 geração.</span><span class="sxs-lookup"><span data-stu-id="ca65d-270">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="ca65d-271">As solicitações over LOH geram coleções de geração 2 de constantes.</span><span class="sxs-lookup"><span data-stu-id="ca65d-271">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="ca65d-272">As coleções de geração 2 são caras.</span><span class="sxs-lookup"><span data-stu-id="ca65d-272">Generation 2 collections are expensive.</span></span> <span data-ttu-id="ca65d-273">Mais CPU é necessária e a taxa de transferência cai quase 50%.</span><span class="sxs-lookup"><span data-stu-id="ca65d-273">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="ca65d-274">Objetos grandes temporários são particularmente problemáticos porque causam Gen2 GCs.</span><span class="sxs-lookup"><span data-stu-id="ca65d-274">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="ca65d-275">Para obter o desempenho máximo, o uso de objeto grande deve ser minimizado.</span><span class="sxs-lookup"><span data-stu-id="ca65d-275">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="ca65d-276">Se possível, divida objetos grandes.</span><span class="sxs-lookup"><span data-stu-id="ca65d-276">If possible, split up large objects.</span></span> <span data-ttu-id="ca65d-277">Por exemplo, o middleware de [cache de resposta](xref:performance/caching/response) no ASP.NET Core dividir as entradas de cache em blocos com menos de 85.000 bytes.</span><span class="sxs-lookup"><span data-stu-id="ca65d-277">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="ca65d-278">Os links a seguir mostram a abordagem de ASP.NET Core para manter os objetos sob o limite de LOH:</span><span class="sxs-lookup"><span data-stu-id="ca65d-278">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>
- [<span data-ttu-id="ca65d-279">ResponseCaching/streams/StreamUtilities. cs</span><span class="sxs-lookup"><span data-stu-id="ca65d-279">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/aspnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
- [<span data-ttu-id="ca65d-280">ResponseCaching/MemoryResponseCache. cs</span><span class="sxs-lookup"><span data-stu-id="ca65d-280">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="ca65d-281">Para obter mais informações, consulte .</span><span class="sxs-lookup"><span data-stu-id="ca65d-281">For more information, see:</span></span>

* [<span data-ttu-id="ca65d-282">Heap de objeto grande descoberto</span><span class="sxs-lookup"><span data-stu-id="ca65d-282">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="ca65d-283">Heap de objeto grande</span><span class="sxs-lookup"><span data-stu-id="ca65d-283">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="ca65d-284">HttpClient</span><span class="sxs-lookup"><span data-stu-id="ca65d-284">HttpClient</span></span>

<span data-ttu-id="ca65d-285">O uso incorreto de <xref:System.Net.Http.HttpClient> pode resultar em um vazamento de recurso.</span><span class="sxs-lookup"><span data-stu-id="ca65d-285">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="ca65d-286">Recursos do sistema, como conexões de banco de dados, soquetes, identificadores de arquivo, etc.:</span><span class="sxs-lookup"><span data-stu-id="ca65d-286">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="ca65d-287">São mais escassos que a memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-287">Are more scarce than memory.</span></span>
* <span data-ttu-id="ca65d-288">São mais problemáticas quando vazados do que a memória.</span><span class="sxs-lookup"><span data-stu-id="ca65d-288">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="ca65d-289">Os desenvolvedores .NET experientes sabem chamar <xref:System.IDisposable.Dispose*> em objetos que implementam <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="ca65d-289">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="ca65d-290">Não descartar objetos que implementam `IDisposable` normalmente resulta em memória vazada ou em recursos de sistema vazados.</span><span class="sxs-lookup"><span data-stu-id="ca65d-290">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="ca65d-291">`HttpClient` implementa `IDisposable`, mas **não** deve ser descartado em todas as invocações.</span><span class="sxs-lookup"><span data-stu-id="ca65d-291">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="ca65d-292">Em vez disso, `HttpClient` deve ser reutilizada.</span><span class="sxs-lookup"><span data-stu-id="ca65d-292">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="ca65d-293">O ponto de extremidade a seguir cria e descarta uma nova instância de `HttpClient` em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="ca65d-293">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="ca65d-294">Sob carga, as seguintes mensagens de erro são registradas em log:</span><span class="sxs-lookup"><span data-stu-id="ca65d-294">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="ca65d-295">Embora as instâncias de `HttpClient` sejam descartadas, a conexão de rede real leva algum tempo para ser liberada pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="ca65d-295">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="ca65d-296">Ao criar continuamente novas conexões, ocorre o _esgotamento de portas_ .</span><span class="sxs-lookup"><span data-stu-id="ca65d-296">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="ca65d-297">Cada conexão de cliente requer sua própria porta de cliente.</span><span class="sxs-lookup"><span data-stu-id="ca65d-297">Each client connection requires its own client port.</span></span>

<span data-ttu-id="ca65d-298">Uma maneira de evitar o esgotamento de porta é reutilizar a mesma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ca65d-298">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="ca65d-299">A instância de `HttpClient` é liberada quando o aplicativo é interrompido.</span><span class="sxs-lookup"><span data-stu-id="ca65d-299">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="ca65d-300">Este exemplo mostra que nem todos os recursos descartáveis devem ser descartados após cada uso.</span><span class="sxs-lookup"><span data-stu-id="ca65d-300">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="ca65d-301">Consulte o seguinte para obter uma maneira melhor de lidar com o tempo de vida de uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="ca65d-301">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="ca65d-302">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="ca65d-302">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="ca65d-303">Blog de fábrica do HTTPClient</span><span class="sxs-lookup"><span data-stu-id="ca65d-303">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="ca65d-304">Pooling de objetos</span><span class="sxs-lookup"><span data-stu-id="ca65d-304">Object pooling</span></span>

<span data-ttu-id="ca65d-305">O exemplo anterior mostrou como a instância de `HttpClient` pode se tornar estática e reutilizada por todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="ca65d-305">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="ca65d-306">A reutilização impede a execução de recursos.</span><span class="sxs-lookup"><span data-stu-id="ca65d-306">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="ca65d-307">Pool de objetos:</span><span class="sxs-lookup"><span data-stu-id="ca65d-307">Object pooling:</span></span>

* <span data-ttu-id="ca65d-308">Usa o padrão de reutilização.</span><span class="sxs-lookup"><span data-stu-id="ca65d-308">Uses the reuse pattern.</span></span>
* <span data-ttu-id="ca65d-309">O é projetado para objetos que são caros de criar.</span><span class="sxs-lookup"><span data-stu-id="ca65d-309">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="ca65d-310">Um pool é uma coleção de objetos pré-carregados que podem ser reservados e liberados entre threads.</span><span class="sxs-lookup"><span data-stu-id="ca65d-310">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="ca65d-311">Os pools podem definir regras de alocação, como limites, tamanhos predefinidos ou taxa de crescimento.</span><span class="sxs-lookup"><span data-stu-id="ca65d-311">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="ca65d-312">O pacote NuGet [Microsoft. Extensions. objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contém classes que ajudam a gerenciar esses pools.</span><span class="sxs-lookup"><span data-stu-id="ca65d-312">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="ca65d-313">O ponto de extremidade de API a seguir instancia um buffer de `byte` que é preenchido com números aleatórios em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="ca65d-313">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="ca65d-314">A exibição de gráfico a seguir chamando a API anterior com carga moderada:</span><span class="sxs-lookup"><span data-stu-id="ca65d-314">The following chart display calling the preceding API with moderate load:</span></span>

![gráfico anterior](memory/_static/array.png)

<span data-ttu-id="ca65d-316">No gráfico anterior, as coletas de geração 0 acontecem aproximadamente uma vez por segundo.</span><span class="sxs-lookup"><span data-stu-id="ca65d-316">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="ca65d-317">O código anterior pode ser otimizado com o pool do buffer de `byte` usando [ArrayPool\<t >](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="ca65d-317">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="ca65d-318">Uma instância estática é reutilizada entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="ca65d-318">A static instance is reused across requests.</span></span>

<span data-ttu-id="ca65d-319">O que é diferente com essa abordagem é que um objeto em pool é retornado da API.</span><span class="sxs-lookup"><span data-stu-id="ca65d-319">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="ca65d-320">Isso significa que:</span><span class="sxs-lookup"><span data-stu-id="ca65d-320">That means:</span></span>

* <span data-ttu-id="ca65d-321">O objeto está fora do seu controle assim que você retorna do método.</span><span class="sxs-lookup"><span data-stu-id="ca65d-321">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="ca65d-322">Não é possível liberar o objeto.</span><span class="sxs-lookup"><span data-stu-id="ca65d-322">You can't release the object.</span></span>

<span data-ttu-id="ca65d-323">Para configurar a alienação do objeto:</span><span class="sxs-lookup"><span data-stu-id="ca65d-323">To set up disposal of the object:</span></span>

* <span data-ttu-id="ca65d-324">Encapsular a matriz em pool em um objeto descartável.</span><span class="sxs-lookup"><span data-stu-id="ca65d-324">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="ca65d-325">Registre o objeto em pool com [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="ca65d-325">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="ca65d-326">`RegisterForDispose` se encarregará de chamar `Dispose`no objeto de destino para que ele seja liberado apenas quando a solicitação HTTP for concluída.</span><span class="sxs-lookup"><span data-stu-id="ca65d-326">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="ca65d-327">A aplicação da mesma carga que a versão não-agrupada resulta no seguinte gráfico:</span><span class="sxs-lookup"><span data-stu-id="ca65d-327">Applying the same load as the non-pooled version results in the following chart:</span></span>

![gráfico anterior](memory/_static/pooledarray.png)

<span data-ttu-id="ca65d-329">A principal diferença são os bytes alocados e, como consequência, menos coleções de geração 0.</span><span class="sxs-lookup"><span data-stu-id="ca65d-329">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca65d-330">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ca65d-330">Additional resources</span></span>

* [<span data-ttu-id="ca65d-331">Coleta de lixo</span><span class="sxs-lookup"><span data-stu-id="ca65d-331">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="ca65d-332">Compreendendo modos de GC diferentes com o Visualizador de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="ca65d-332">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="ca65d-333">Heap de objeto grande descoberto</span><span class="sxs-lookup"><span data-stu-id="ca65d-333">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="ca65d-334">Heap de objeto grande</span><span class="sxs-lookup"><span data-stu-id="ca65d-334">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
