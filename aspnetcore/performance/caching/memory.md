---
title: Cache na memória no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar dados em cache na memória no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 8/22/2019
uid: performance/caching/memory
ms.openlocfilehash: d6b2aa363c552fdbda7f6e9ec5d476768c17d8a5
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779187"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Cache na memória no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo)e [Steve Smith](https://ardalis.com/)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Noções básicas de cache

O Caching pode melhorar significativamente o desempenho e a escalabilidade de um aplicativo, reduzindo o trabalho necessário para gerar conteúdo. O Caching funciona melhor com dados que são alterados com pouca frequência **e** é caro de gerar. O Caching faz uma cópia dos dados que podem ser retornados muito mais rápido do que a partir da origem. Os aplicativos devem ser escritos e testados para **nunca** depender de dados armazenados em cache.

O ASP.NET Core dá suporte a vários caches diferentes. O cache mais simples é baseado no [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). `IMemoryCache` representa um cache armazenado na memória do servidor Web. Os aplicativos em execução em um farm de servidores (vários servidores) devem garantir que as sessões sejam adesivas ao usar o cache na memória. As sessões adesivas garantem que todas as solicitações subsequentes de um cliente vão para o mesmo servidor. Por exemplo, os aplicativos Web do Azure usam [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) para rotear todas as solicitações subsequentes para o mesmo servidor.

As sessões não adesivas em um web farm exigem um [cache distribuído](distributed.md) para evitar problemas de consistência do cache. Para alguns aplicativos, um cache distribuído pode dar suporte a escalabilidade horizontal maior que um cache na memória. O uso de um cache distribuído descarrega a memória de cache para um processo externo.

O cache na memória pode armazenar qualquer objeto. A interface de cache distribuído é limitada a `byte[]`. O armazenamento em cache na memória e distribuído armazena itens de cache como pares de chave-valor.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching> / <xref:System.Runtime.Caching.MemoryCache> ([pacote NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) pode ser usado com:

* .NET Standard 2,0 ou posterior.
* Qualquer [implementação .net](/dotnet/standard/net-standard#net-implementation-support) que tenha como destino .net Standard 2,0 ou posterior. Por exemplo, ASP.NET Core 2,0 ou posterior.
* .NET Framework 4,5 ou posterior.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (descrito neste artigo) é recomendado sobre `System.Runtime.Caching` / `MemoryCache` porque ele é mais integrado ao ASP.NET Core. Por exemplo, `IMemoryCache` funciona nativamente com ASP.NET Core [injeção de dependência](xref:fundamentals/dependency-injection).

Use `System.Runtime.Caching` / `MemoryCache` como uma ponte de compatibilidade ao portar o código de ASP.NET 4. x para ASP.NET Core.

## <a name="cache-guidelines"></a>Diretrizes de cache

* O código deve sempre ter uma opção de fallback para buscar dados e **não** depende de um valor em cache estar disponível.
* O cache usa um recurso escasso, memória. Limitar o crescimento do cache:
  * Não **use a** entrada externa como chaves de cache.
  * Use expirações para limitar o crescimento do cache.
  * [Use SetSize, size e SizeLimit para limitar o tamanho do cache](#use-setsize-size-and-sizelimit-to-limit-cache-size). O tempo de execução de ASP.NET Core **não limita o** tamanho do cache com base na pressão de memória. Cabe ao desenvolvedor limitar o tamanho do cache.

## <a name="use-imemorycache"></a>Usar IMemoryCache

> [!WARNING]
> Usar um cache de memória *compartilhada* de [injeção de dependência](xref:fundamentals/dependency-injection) e chamar `SetSize`, `Size` ou `SizeLimit` para limitar o tamanho do cache pode causar falha no aplicativo. Quando um limite de tamanho é definido em um cache, todas as entradas devem especificar um tamanho ao serem adicionadas. Isso pode levar a problemas, já que os desenvolvedores podem não ter controle total sobre o que usa o cache compartilhado. Por exemplo, Entity Framework Core usa o cache compartilhado e não especifica um tamanho. Se um aplicativo definir um limite de tamanho de cache e usar EF Core, o aplicativo lançará um `InvalidOperationException`.
> Ao usar `SetSize`, `Size` ou `SizeLimit` para limitar o cache, crie um singleton de cache para cache. Para obter mais informações e um exemplo, consulte [usar SetSize, tamanho e SizeLimit para limitar o tamanho do cache](#use-setsize-size-and-sizelimit-to-limit-cache-size).
> Um cache compartilhado é um compartilhado por outras estruturas ou bibliotecas. Por exemplo, EF Core usa o cache compartilhado e não especifica um tamanho. 

O cache na memória é um *serviço* que é referenciado de um aplicativo usando [injeção de dependência](xref:fundamentals/dependency-injection). Solicite a instância de `IMemoryCache` no construtor:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

O código a seguir usa [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) para verificar se há uma hora no cache. Se uma hora não for armazenada em cache, uma nova entrada será criada e adicionada ao cache com o [conjunto](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_). A classe `CacheKeys` faz parte do exemplo de download.

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

A hora atual e a hora em cache são exibidas:

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

O valor de `DateTime` em cache permanece no cache enquanto houver solicitações dentro do período de tempo limite.

O código a seguir usa [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) e [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) para armazenar dados em cache.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

O código a seguir chama [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) para buscar o tempo em cache:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

O código a seguir obtém ou cria um item armazenado em cache com expiração absoluta:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

Um conjunto de itens em cache com uma expiração deslizante só corre o risco de se tornar obsoleto. Se ele for acessado com mais frequência do que o intervalo de expiração deslizante, o item nunca expirará. Combine uma expiração deslizante com uma expiração absoluta para garantir que o item expire quando seu tempo de expiração absoluto passar. A expiração absoluta define um limite superior para o tempo em que o item pode ser armazenado em cache enquanto ainda permite que o item expire antes, se ele não for solicitado dentro do intervalo de expiração deslizante. Quando a expiração absoluta e deslizante são especificadas, as expirações são logicamente de or. Se o intervalo de expiração deslizante *ou* o tempo de expiração absoluto forem aprovados, o item será removido do cache.

O código a seguir obtém ou cria um item armazenado em cache com a expiração deslizante *e* absoluta:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

O código anterior garante que os dados não serão armazenados em cache mais do que o tempo absoluto.

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> e <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> são métodos de extensão na classe <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions>. Esses métodos estendem a capacidade de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

O exemplo a seguir:

* Define um tempo de expiração deslizante. As solicitações que acessam este item armazenado em cache redefinirão o relógio de expiração deslizante.
* Define a prioridade do cache como [CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).
* Define um [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) que será chamado depois que a entrada for removida do cache. O retorno de chamada é executado em um thread diferente do código que remove o item do cache.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Use SetSize, size e SizeLimit para limitar o tamanho do cache

Uma instância de `MemoryCache` pode, opcionalmente, especificar e impor um limite de tamanho. O limite de tamanho do cache não tem uma unidade de medida definida porque o cache não tem nenhum mecanismo para medir o tamanho das entradas. Se o limite de tamanho do cache for definido, todas as entradas deverão especificar tamanho. O tempo de execução de ASP.NET Core não limita o tamanho do cache com base na pressão de memória. Cabe ao desenvolvedor limitar o tamanho do cache. O tamanho especificado é em unidades que o desenvolvedor escolhe.

Por exemplo:

* Se o aplicativo Web estava armazenando cadeia de caracteres em cache principalmente, cada tamanho de entrada de cache poderia ser o comprimento da cadeia de caracteres.
* O aplicativo pode especificar o tamanho de todas as entradas como 1 e o limite de tamanho é a contagem de entradas.

Se <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> não estiver definido, o cache aumentará sem limite. O tempo de execução de ASP.NET Core não corta o cache quando a memória do sistema está baixa. Muitos aplicativos foram arquitetados para:

* Limitar o crescimento do cache.
* Chame <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> ou <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> quando a memória disponível for limitada:

O código a seguir cria um tamanho fixo unitário <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> acessível pela [injeção de dependência](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` não tem unidades. As entradas armazenadas em cache devem especificar o tamanho em qualquer unidade que julgar mais apropriada se o limite de tamanho do cache tiver sido definido. Todos os usuários de uma instância de cache devem usar o mesmo sistema de unidade. Uma entrada não será armazenada em cache se a soma dos tamanhos de entrada armazenados em cache exceder o valor especificado por `SizeLimit`. Se nenhum limite de tamanho de cache for definido, o tamanho do cache definido na entrada será ignorado.

O código a seguir registra `MyMemoryCache` com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` é criado como um cache de memória independente para os componentes que reconhecem esse cache de tamanho limitado e sabem como definir adequadamente o tamanho da entrada de cache.

O código a seguir usa `MyMemoryCache`:

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

O tamanho da entrada de cache pode ser definido por <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> ou os métodos de extensão de <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*>:

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` tenta remover o percentual especificado do cache na seguinte ordem:

* Todos os itens expirados.
* Itens por prioridade. Os itens de prioridade mais baixa são removidos primeiro.
* Objetos usados menos recentemente.
* Itens com a expiração absoluta mais antiga.
* Itens com a expiração deslizante mais antiga.

Itens fixados com <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> de prioridade nunca são removidos. O código a seguir remove um item de cache e chama `Compact`:

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Consulte [Compact Source no GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) para obter mais informações.

## <a name="cache-dependencies"></a>Dependências de cache

O exemplo a seguir mostra como expirar uma entrada de cache se uma entrada dependente expirar. Um `CancellationChangeToken` é adicionado ao item armazenado em cache. Quando `Cancel` é chamado na `CancellationTokenSource`, as duas entradas de cache são removidas.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

O uso de um `CancellationTokenSource` permite que várias entradas de cache sejam removidas como um grupo. Com o padrão de `using` no código acima, as entradas de cache criadas dentro do bloco de `using` herdarão os gatilhos e as configurações de expiração.

## <a name="additional-notes"></a>Observações adicionais

* A expiração não ocorre em segundo plano. Não há nenhum temporizador que examina ativamente o cache em busca de itens expirados. Qualquer atividade no cache (`Get`, `Set` `Remove`) pode disparar uma verificação em segundo plano para itens expirados. Um temporizador no `CancellationTokenSource` (`CancelAfter`) também removeria a entrada e dispararia uma verificação de itens expirados. Por exemplo, em vez de usar `SetAbsoluteExpiration(TimeSpan.FromHours(1))`, use `CancellationTokenSource.CancelAfter(TimeSpan.FromHours(1))` para o token registrado. Quando esse token é disparado, ele remove a entrada imediatamente e dispara os retornos de chamada de remoção. Para obter mais informações, consulte [este problema do GitHub](https://github.com/aspnet/Caching/issues/248).
* Ao usar um retorno de chamada para popular novamente um item de cache:

  * Várias solicitações podem encontrar o valor de chave em cache vazio porque o retorno de chamada não foi concluído.
  * Isso pode resultar em vários threads repopulando o item armazenado em cache.

* Quando uma entrada de cache é usada para criar outra, o filho copia os tokens de expiração da entrada pai e as configurações de expiração com base no tempo. O filho não expirou pela remoção manual ou pela atualização da entrada pai.

* Use <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> para definir os retornos de chamada que serão acionados depois que a entrada de cache for removida do cache.
* Para a maioria dos aplicativos, o `IMemoryCache` está habilitado. Por exemplo, chamar `AddMvc`, `AddControllersWithViews`, `AddRazorPages`, `AddMvcCore().AddRazorViewEngine` e muitos outros métodos de `Add{Service}` no `ConfigureServices`, habilita `IMemoryCache`. Para aplicativos que não chamam um dos métodos de `Add{Service}` anteriores, pode ser necessário chamar <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> no `ConfigureServices`.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
Por [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo)e [Steve Smith](https://ardalis.com/)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Noções básicas de cache

O Caching pode melhorar significativamente o desempenho e a escalabilidade de um aplicativo, reduzindo o trabalho necessário para gerar conteúdo. O Caching funciona melhor com dados que são alterados com pouca frequência. O Caching faz uma cópia dos dados que podem ser retornados muito mais rápido do que a partir da fonte original. O código deve ser escrito e testado para **nunca** depender de dados armazenados em cache.

O ASP.NET Core dá suporte a vários caches diferentes. O cache mais simples é baseado no [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), que representa um cache armazenado na memória do servidor Web. Os aplicativos que são executados em um farm de servidores (vários servidores) devem garantir que as sessões sejam adesivas ao usar o cache na memória. As sessões adesivas garantem que todas as solicitações posteriores de um cliente passem para o mesmo servidor. Por exemplo, os aplicativos Web do Azure usam [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) para rotear todas as solicitações de um agente do usuário para o mesmo servidor.

As sessões não adesivas em um web farm exigem um [cache distribuído](distributed.md) para evitar problemas de consistência do cache. Para alguns aplicativos, um cache distribuído pode dar suporte a escalabilidade horizontal maior que um cache na memória. O uso de um cache distribuído descarrega a memória de cache para um processo externo.

O cache na memória pode armazenar qualquer objeto. A interface de cache distribuído é limitada a `byte[]`. O armazenamento em cache na memória e distribuído armazena itens de cache como pares de chave-valor.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching> / <xref:System.Runtime.Caching.MemoryCache> ([pacote NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) pode ser usado com:

* .NET Standard 2,0 ou posterior.
* Qualquer [implementação .net](/dotnet/standard/net-standard#net-implementation-support) que tenha como destino .net Standard 2,0 ou posterior. Por exemplo, ASP.NET Core 2,0 ou posterior.
* .NET Framework 4,5 ou posterior.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (descrito neste artigo) é recomendado sobre `System.Runtime.Caching` / `MemoryCache` porque ele é mais integrado ao ASP.NET Core. Por exemplo, `IMemoryCache` funciona nativamente com ASP.NET Core [injeção de dependência](xref:fundamentals/dependency-injection).

Use `System.Runtime.Caching` / `MemoryCache` como uma ponte de compatibilidade ao portar o código de ASP.NET 4. x para ASP.NET Core.

## <a name="cache-guidelines"></a>Diretrizes de cache

* O código deve sempre ter uma opção de fallback para buscar dados e **não** depende de um valor em cache estar disponível.
* O cache usa um recurso escasso, memória. Limitar o crescimento do cache:
  * Não **use a** entrada externa como chaves de cache.
  * Use expirações para limitar o crescimento do cache.
  * [Use SetSize, size e SizeLimit para limitar o tamanho do cache](#use-setsize-size-and-sizelimit-to-limit-cache-size). O tempo de execução de ASP.NET Core não limita o tamanho do cache com base na pressão de memória. Cabe ao desenvolvedor limitar o tamanho do cache.

## <a name="using-imemorycache"></a>Usando IMemoryCache

> [!WARNING]
> Usar um cache de memória *compartilhada* de [injeção de dependência](xref:fundamentals/dependency-injection) e chamar `SetSize`, `Size` ou `SizeLimit` para limitar o tamanho do cache pode causar falha no aplicativo. Quando um limite de tamanho é definido em um cache, todas as entradas devem especificar um tamanho ao serem adicionadas. Isso pode levar a problemas, já que os desenvolvedores podem não ter controle total sobre o que usa o cache compartilhado. Por exemplo, Entity Framework Core usa o cache compartilhado e não especifica um tamanho. Se um aplicativo definir um limite de tamanho de cache e usar EF Core, o aplicativo lançará um `InvalidOperationException`.
> Ao usar `SetSize`, `Size` ou `SizeLimit` para limitar o cache, crie um singleton de cache para cache. Para obter mais informações e um exemplo, consulte [usar SetSize, tamanho e SizeLimit para limitar o tamanho do cache](#use-setsize-size-and-sizelimit-to-limit-cache-size).

O cache na memória é um *serviço* que é referenciado de seu aplicativo usando [injeção de dependência](../../fundamentals/dependency-injection.md). Chamar `AddMemoryCache` em `ConfigureServices`:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Solicite a instância de `IMemoryCache` no construtor:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache` requer o pacote NuGet [Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), que está disponível no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

O código a seguir usa [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) para verificar se há uma hora no cache. Se uma hora não for armazenada em cache, uma nova entrada será criada e adicionada ao cache com o [conjunto](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

A hora atual e a hora em cache são exibidas:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

O valor de `DateTime` em cache permanece no cache enquanto houver solicitações dentro do período de tempo limite. A imagem a seguir mostra a hora atual e uma hora mais antiga recuperada do cache:

![Exibição de índice com duas horas diferentes exibidas](memory/_static/time.png)

O código a seguir usa [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) e [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) para armazenar dados em cache.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

O código a seguir chama [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) para buscar o tempo em cache:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> e [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) são os métodos de extensão que fazem parte da classe [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) que estende a capacidade de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Consulte [métodos IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) e [métodos CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) para obter uma descrição de outros métodos de cache.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

O exemplo a seguir:

* Define um tempo de expiração deslizante. As solicitações que acessam este item armazenado em cache redefinirão o relógio de expiração deslizante.
* Define a prioridade de cache para `CacheItemPriority.NeverRemove`.
* Define um [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) que será chamado depois que a entrada for removida do cache. O retorno de chamada é executado em um thread diferente do código que remove o item do cache.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Use SetSize, size e SizeLimit para limitar o tamanho do cache

Uma instância de `MemoryCache` pode, opcionalmente, especificar e impor um limite de tamanho. O limite de tamanho do cache não tem uma unidade de medida definida porque o cache não tem nenhum mecanismo para medir o tamanho das entradas. Se o limite de tamanho do cache for definido, todas as entradas deverão especificar tamanho. O tempo de execução de ASP.NET Core não limita o tamanho do cache com base na pressão de memória. Cabe ao desenvolvedor limitar o tamanho do cache. O tamanho especificado é em unidades que o desenvolvedor escolhe.

Por exemplo:

* Se o aplicativo Web estava armazenando cadeia de caracteres em cache principalmente, cada tamanho de entrada de cache poderia ser o comprimento da cadeia de caracteres.
* O aplicativo pode especificar o tamanho de todas as entradas como 1 e o limite de tamanho é a contagem de entradas.

Se <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> não estiver definido, o cache aumentará sem limite. O tempo de execução de ASP.NET Core não corta o cache quando a memória do sistema está baixa. Muitos aplicativos foram arquitetados para:

* Limitar o crescimento do cache.
* Chame <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> ou <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> quando a memória disponível for limitada:

O código a seguir cria um tamanho fixo unitário <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> acessível pela [injeção de dependência](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` não tem unidades. As entradas armazenadas em cache devem especificar o tamanho em qualquer unidade que julgar mais apropriada se o limite de tamanho do cache tiver sido definido. Todos os usuários de uma instância de cache devem usar o mesmo sistema de unidade. Uma entrada não será armazenada em cache se a soma dos tamanhos de entrada armazenados em cache exceder o valor especificado por `SizeLimit`. Se nenhum limite de tamanho de cache for definido, o tamanho do cache definido na entrada será ignorado.

O código a seguir registra `MyMemoryCache` com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` é criado como um cache de memória independente para os componentes que reconhecem esse cache de tamanho limitado e sabem como definir adequadamente o tamanho da entrada de cache.

O código a seguir usa `MyMemoryCache`:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

O tamanho da entrada de cache pode ser definido por [tamanho](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) ou pelo método de extensão [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` tenta remover o percentual especificado do cache na seguinte ordem:

* Todos os itens expirados.
* Itens por prioridade. Os itens de prioridade mais baixa são removidos primeiro.
* Objetos usados menos recentemente.
* Itens com a expiração absoluta mais antiga.
* Itens com a expiração deslizante mais antiga.

Itens fixados com <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> de prioridade nunca são removidos.

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Consulte [Compact Source no GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) para obter mais informações.

## <a name="cache-dependencies"></a>Dependências de cache

O exemplo a seguir mostra como expirar uma entrada de cache se uma entrada dependente expirar. Um `CancellationChangeToken` é adicionado ao item armazenado em cache. Quando `Cancel` é chamado na `CancellationTokenSource`, as duas entradas de cache são removidas.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

O uso de um `CancellationTokenSource` permite que várias entradas de cache sejam removidas como um grupo. Com o padrão de `using` no código acima, as entradas de cache criadas dentro do bloco de `using` herdarão os gatilhos e as configurações de expiração.

## <a name="additional-notes"></a>Observações adicionais

* Ao usar um retorno de chamada para popular novamente um item de cache:

  * Várias solicitações podem encontrar o valor de chave em cache vazio porque o retorno de chamada não foi concluído.
  * Isso pode resultar em vários threads repopulando o item armazenado em cache.

* Quando uma entrada de cache é usada para criar outra, o filho copia os tokens de expiração da entrada pai e as configurações de expiração com base no tempo. O filho não expirou pela remoção manual ou pela atualização da entrada pai.

* Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) para definir os retornos de chamada que serão acionados depois que a entrada de cache for removida do cache.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
