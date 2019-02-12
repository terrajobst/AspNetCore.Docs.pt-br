---
title: Memória de cache no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar dados em cache na memória no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 9a7727ad41a05f39d74877af3c8f2e3f7a620c7d
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103066"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Memória de cache no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), e [Steve Smith](https://ardalis.com/)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Noções básicas de cache

O cache pode melhorar significativamente o desempenho e a escalabilidade de um aplicativo, reduzindo o trabalho necessário para gerar o conteúdo. O armazenamento em cache funciona melhor com dados que raramente são alterados. O cache faz uma cópia dos dados que pode ser retornada muito mais rapidamente do que a partir da origem. Você deve escrever e testar seu aplicativo para que ele nunca dependa de dados armazenados em cache.

O ASP.NET Core dá suporte a vários caches diferentes. O cache mais simples se baseia o [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), que representa um cache armazenado na memória do servidor web. Aplicativos que são executados em um farm de servidores devem garantir que as sessões sejam aderentes ao usar o cache na memória. Sessões aderentes garantem que todas as solicitações subsequentes de um cliente vão para o mesmo servidor. Por exemplo, uso de aplicativos da Web do Azure [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) para rotear todas as solicitações subsequentes para o mesmo servidor.

Não adesivo sessões em um farm da web exigem uma [cache distribuído](distributed.md) para evitar problemas de consistência de cache. Para alguns aplicativos, um cache distribuído pode dar suporte a mais alta escalabilidade horizontal que um cache na memória. Usando um cache distribuído libera a memória de cache para um processo externo.

::: moniker range="< aspnetcore-2.0"

O cache `IMemoryCache` removerá entradas de cache sob pressão de memória, a menos que a [prioridade de cache](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) seja definida como `CacheItemPriority.NeverRemove`. Você pode definir o `CacheItemPriority` para ajustar a prioridade com que o cache remove itens sob pressão de memória.

::: moniker-end

O cache de memória pode armazenar qualquer objeto, enquanto a interface de cache distribuída é limitada a`byte[]`. Os itens de cache de armazenamento de cache na memória e distribuído como pares chave-valor.

## <a name="systemruntimecachingmemorycache"></a>System.Runtime.Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Pacote do NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) pode ser usado com:

* .NET standard 2.0 ou posterior.
* Qualquer [implementação do .NET](/dotnet/standard/net-standard#net-implementation-support) que tem como alvo o .NET Standard 2.0 ou posterior. Por exemplo, ASP.NET Core 2.0 ou posterior.
* .NET framework 4.5 ou posterior.

[Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (descrita neste tópico) é mais recomendada `System.Runtime.Caching` / `MemoryCache` porque ele é melhor integrado ao ASP.NET Core. Por exemplo, `IMemoryCache` funciona nativamente com o ASP.NET Core [injeção de dependência](xref:fundamentals/dependency-injection).

Use `System.Runtime.Caching` / `MemoryCache` como uma ponte de compatibilidade ao portar código do ASP.NET 4.x ASP.NET Core.

## <a name="cache-guidelines"></a>Diretrizes de cache

* Código deveria ter sempre uma opção de fallback para buscar dados e **não** dependem de um valor em cache que está sendo disponível.
* O cache usa um recurso escasso, de memória. Limitar o crescimento de cache:
  * Fazer **não** usar entrada externo como chaves de cache.
  * Use as expirações para limitar o crescimento de cache.
  * [Usar SetSize, tamanho e SizeLimit para limitar o tamanho do cache](#use-setsize-size-and-sizelimit-to-limit-cache-size)

## <a name="using-imemorycache"></a>Usando IMemoryCache

O cache na memória é um *service* que é referenciado em seu aplicativo usando [injeção de dependência](../../fundamentals/dependency-injection.md). Chame `AddMemoryCache` em `ConfigureServices`:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Solicitar o `IMemoryCache` instância no construtor:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache` requer o pacote NuGet [Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache` requer o pacote NuGet [Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), que está disponível na [Microsoft.AspNetCore.All metapacote](xref:fundamentals/metapackage).

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache` requer o pacote NuGet [Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), que está disponível na [metapacote Microsoft](xref:fundamentals/metapackage-app).

::: moniker-end

O seguinte código usa [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) para verificar se um horário está no cache. Se não estiver armazenado em cache um tempo, uma nova entrada é criada e adicionada ao cache com [definir](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

A hora atual e o tempo em cache são exibidos:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

O valor `DateTime` em cache permanecerá no cache enquanto houver solicitações dentro do tempo limite (e nenhuma remoção devido à pressão de memória). A imagem abaixo mostra a hora atual e uma hora mais antiga recuperada do cache:

![Exibição de índice com dois horários diferentes exibidos](memory/_static/time.png)

O código a seguir usa [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) e [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) para fazer o cache dos dados. 

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

O código a seguir chama o método [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) para buscar a hora em cache:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, e [Obtenha](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) fazem parte de métodos de extensão do [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) classe que estende a capacidade do <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Ver [métodos IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) e [CacheExtensions métodos](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) para obter uma descrição dos outros métodos de cache.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

O exemplo a seguir:

- Define o tempo de expiração absoluta. Isso é o tempo máximo que a entrada pode ser armazenado em cache e impede que o item se torne muito desatualizado quando a expiração deslizante continuamente é renovada.
- Define um tempo de expiração deslizante. Solicitações que acessam esse item em cache redefinirá o relógio de expiração deslizante.
- Define a prioridade de cache para `CacheItemPriority.NeverRemove`.
- Define uma [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) que será chamado depois que a entrada é removida do cache. O retorno de chamada é executado em um thread diferente do código que remove o item do cache.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Usar SetSize, tamanho e SizeLimit para limitar o tamanho do cache

Um `MemoryCache` instância pode, opcionalmente, especificar e impor um limite de tamanho. O limite de tamanho de memória não tem uma unidade de medida de definido porque o cache não tem nenhum mecanismo para medir o tamanho de entradas. Se o limite de tamanho de memória de cache for definido, todas as entradas devem especificar o tamanho. O tamanho especificado está em unidades que o desenvolvedor optar por.

Por exemplo:

* Se o aplicativo web foi principalmente cache de cadeias de caracteres, cada tamanho de entrada de cache pode ser o comprimento da cadeia de caracteres.
* O aplicativo pode especificar o tamanho de todas as entradas como 1 e o limite de tamanho é a contagem de entradas.

O código a seguir cria um sem unidade tamanho fixo [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) acessível pelo [injeção de dependência](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) não tem unidades. As entradas em cache devem especificar o tamanho em qualquer unidade que consideram mais apropriados se o tamanho da memória de cache tiver sido definido. Todos os usuários de uma instância de cache devem usar a mesma unidade de sistema. Uma entrada não será armazenada, se a soma dos tamanhos da entrada armazenada em cache excede o valor especificado pelo `SizeLimit`. Se nenhum limite de tamanho do cache for definido, o tamanho do cache definida na entrada será ignorado.

O código a seguir registra `MyMemoryCache` com o [injeção de dependência](xref:fundamentals/dependency-injection) contêiner.

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` é criado como um cache de memória independentes para os componentes que reconhecem esse cache de tamanho limitado e saber como definir o tamanho do cache de entrada adequadamente.

O seguinte código usa `MyMemoryCache`:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

O tamanho da entrada de cache pode ser definido [tamanho](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) ou o [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) método de extensão:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>Dependências de cache

O exemplo a seguir mostra como expirar uma entrada de cache, se uma entrada dependente expira. Um `CancellationChangeToken` é adicionado ao item em cache. Quando `Cancel` é chamado de `CancellationTokenSource`, ambas as entradas de cache são removidas.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Usando um `CancellationTokenSource` permite que várias entradas de cache a ser removido como um grupo. Com o `using` padrão no código acima, as entradas de cache criado dentro de `using` bloco herdará as configurações de expiração e gatilhos.

## <a name="additional-notes"></a>Observações adicionais

- Ao usar um retorno de chamada para preencher novamente um item de cache:

  - Várias solicitações podem localizar o valor da chave em cache vazio porque o retorno de chamada não foi concluído.
  - Isso pode resultar em vários threads repopulação o item em cache.

- Quando uma entrada de cache é usada para criar outro, o filho copia a entrada de pai tokens de expiração e as configurações de expiração com base no tempo. O filho não está expirada pela remoção manual ou a atualização da entrada pai.

- Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) para definir os retornos de chamada que serão acionados depois que a entrada de cache é removida do cache.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
