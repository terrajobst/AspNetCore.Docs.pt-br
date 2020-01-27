---
title: Caching distribuído no ASP.NET Core
author: guardrex
description: Saiba como usar um ASP.NET Core cache distribuído para melhorar o desempenho e a escalabilidade do aplicativo, especialmente em um ambiente de nuvem ou de farm de servidores.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/caching/distributed
ms.openlocfilehash: 3e039a26505aed1bcc0299880039760fef19fd67
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727232"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="577a3-103">Caching distribuído no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="577a3-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="577a3-104">De [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="577a3-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="577a3-105">Um cache distribuído é um cache compartilhado por vários servidores de aplicativos, normalmente mantido como um serviço externo para os servidores de aplicativos que o acessam.</span><span class="sxs-lookup"><span data-stu-id="577a3-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="577a3-106">Um cache distribuído pode melhorar o desempenho e a escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="577a3-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="577a3-107">Um cache distribuído tem várias vantagens em relação A outros cenários de cache em que os dados armazenados em cache são armazenados em servidores de aplicativos individuais.</span><span class="sxs-lookup"><span data-stu-id="577a3-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="577a3-108">Quando os dados armazenados em cache são distribuídos, os dados:</span><span class="sxs-lookup"><span data-stu-id="577a3-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="577a3-109">É *coerente* (consistente) entre solicitações para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="577a3-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="577a3-110">Sobreviver a reinicializações de servidor e implantações de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="577a3-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="577a3-111">Não usa memória local.</span><span class="sxs-lookup"><span data-stu-id="577a3-111">Doesn't use local memory.</span></span>

<span data-ttu-id="577a3-112">A configuração de cache distribuído é específica da implementação.</span><span class="sxs-lookup"><span data-stu-id="577a3-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="577a3-113">Este artigo descreve como configurar os caches distribuídos SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="577a3-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="577a3-114">Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="577a3-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="577a3-115">Independentemente da implementação selecionada, o aplicativo interage com o cache usando a interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>.</span><span class="sxs-lookup"><span data-stu-id="577a3-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="577a3-116">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="577a3-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="577a3-117">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="577a3-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="577a3-118">Para usar um SQL Server cache distribuído, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="577a3-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="577a3-119">Para usar um cache distribuído Redis, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="577a3-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="577a3-120">Para usar o cache distribuído do NCache, adicione uma referência de pacote ao pacote do [NCache. Microsoft. Extensions. Caching. Open](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="577a3-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="577a3-121">Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="577a3-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="577a3-122">Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="577a3-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="577a3-123">O pacote Redis não está incluído no pacote `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="577a3-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="577a3-124">Para usar o cache distribuído do NCache, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [NCache. Microsoft. Extensions. Caching. openize](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="577a3-124">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="577a3-125">O pacote do NCache não está incluído no pacote de `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote do NCache separadamente em seu arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="577a3-125">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="577a3-126">Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="577a3-126">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="577a3-127">Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="577a3-127">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="577a3-128">O pacote Redis não está incluído no pacote `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="577a3-128">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="577a3-129">Para usar o cache distribuído do NCache, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [NCache. Microsoft. Extensions. Caching. openize](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="577a3-129">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="577a3-130">O pacote do NCache não está incluído no pacote de `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote do NCache separadamente em seu arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="577a3-130">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="577a3-131">Interface IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="577a3-131">IDistributedCache interface</span></span>

<span data-ttu-id="577a3-132">A interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> fornece os seguintes métodos para manipular itens na implementação do cache distribuído:</span><span class="sxs-lookup"><span data-stu-id="577a3-132">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="577a3-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; aceita uma chave de cadeia de caracteres e recupera um item em cache como uma matriz de `byte[]`, se encontrado no cache.</span><span class="sxs-lookup"><span data-stu-id="577a3-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="577a3-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; adiciona um item (como matriz de `byte[]`) ao cache usando uma chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="577a3-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="577a3-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).</span><span class="sxs-lookup"><span data-stu-id="577a3-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="577a3-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; remove um item de cache com base em sua chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="577a3-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="577a3-137">Estabelecer serviços de cache distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-137">Establish distributed caching services</span></span>

<span data-ttu-id="577a3-138">Registre uma implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="577a3-138">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="577a3-139">As implementações fornecidas pelo Framework descritas neste tópico incluem:</span><span class="sxs-lookup"><span data-stu-id="577a3-139">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="577a3-140">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="577a3-140">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="577a3-141">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-141">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="577a3-142">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-142">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="577a3-143">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-143">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="577a3-144">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="577a3-144">Distributed Memory Cache</span></span>

<span data-ttu-id="577a3-145">O cache de memória distribuída (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) é uma implementação fornecida pela estrutura de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena itens na memória.</span><span class="sxs-lookup"><span data-stu-id="577a3-145">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="577a3-146">O cache de memória distribuída não é um cache distribuído real.</span><span class="sxs-lookup"><span data-stu-id="577a3-146">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="577a3-147">Os itens armazenados em cache são armazenados pela instância do aplicativo no servidor em que o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="577a3-147">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="577a3-148">O cache de memória distribuída é uma implementação útil:</span><span class="sxs-lookup"><span data-stu-id="577a3-148">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="577a3-149">Em cenários de desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="577a3-149">In development and testing scenarios.</span></span>
* <span data-ttu-id="577a3-150">Quando um único servidor é usado em produção e o consumo de memória não é um problema.</span><span class="sxs-lookup"><span data-stu-id="577a3-150">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="577a3-151">A implementação do cache de memória distribuída abstrai o armazenamento de dados em cache.</span><span class="sxs-lookup"><span data-stu-id="577a3-151">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="577a3-152">Ele permite implementar uma solução de cache distribuído verdadeira no futuro se vários nós ou tolerância a falhas se tornarem necessários.</span><span class="sxs-lookup"><span data-stu-id="577a3-152">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="577a3-153">O aplicativo de exemplo usa o cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="577a3-153">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="577a3-154">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-154">Distributed SQL Server Cache</span></span>

<span data-ttu-id="577a3-155">A implementação do cache SQL Server distribuído (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) permite que o cache distribuído use um banco de dados SQL Server como seu armazenamento de backup.</span><span class="sxs-lookup"><span data-stu-id="577a3-155">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="577a3-156">Para criar uma SQL Server tabela de item em cache em uma instância de SQL Server, você pode usar a ferramenta de `sql-cache`.</span><span class="sxs-lookup"><span data-stu-id="577a3-156">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="577a3-157">A ferramenta cria uma tabela com o nome e o esquema que você especificar.</span><span class="sxs-lookup"><span data-stu-id="577a3-157">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="577a3-158">Crie uma tabela no SQL Server executando o comando `sql-cache create`.</span><span class="sxs-lookup"><span data-stu-id="577a3-158">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="577a3-159">Forneça a instância de SQL Server (`Data Source`), o banco de dados (`Initial Catalog`), o esquema (por exemplo, `dbo`) e o nome da tabela (por exemplo, `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="577a3-159">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="577a3-160">Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:</span><span class="sxs-lookup"><span data-stu-id="577a3-160">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="577a3-161">A tabela criada pela ferramenta de `sql-cache` tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="577a3-161">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela de cache do SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="577a3-163">Um aplicativo deve manipular valores de cache usando uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, não um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span><span class="sxs-lookup"><span data-stu-id="577a3-163">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="577a3-164">O aplicativo de exemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="577a3-164">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="577a3-165">Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) normalmente são armazenados fora do controle do código-fonte (por exemplo, armazenado pelo [Gerenciador de segredo](xref:security/app-secrets) ou em *appSettings. JSON*/*appSettings. { Arquivos de ambiente}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="577a3-165">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="577a3-166">A cadeia de conexão pode conter credenciais que devem ser mantidas fora dos sistemas de controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="577a3-166">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="577a3-167">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-167">Distributed Redis Cache</span></span>

<span data-ttu-id="577a3-168">O [Redis](https://redis.io/) é um armazenamento de dados na memória de software livre, que geralmente é usado como um cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="577a3-168">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="577a3-169">Você pode usar o Redis localmente e pode configurar um [cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo de ASP.NET Core hospedado pelo Azure.</span><span class="sxs-lookup"><span data-stu-id="577a3-169">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="577a3-170">Um aplicativo configura a implementação de cache usando uma instância de <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="577a3-170">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="577a3-171">Um aplicativo configura a implementação de cache usando uma instância de <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="577a3-171">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="577a3-172">Um aplicativo configura a implementação de cache usando uma instância de <xref:Microsoft.Extensions.Caching.Redis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="577a3-172">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="577a3-173">Para instalar o Redis em seu computador local:</span><span class="sxs-lookup"><span data-stu-id="577a3-173">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="577a3-174">Instale o [pacote Redis de Chocolatey](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="577a3-174">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="577a3-175">Execute `redis-server` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="577a3-175">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="577a3-176">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-176">Distributed NCache Cache</span></span>

<span data-ttu-id="577a3-177">O [NCache](https://github.com/Alachisoft/NCache) é um cache distribuído na memória de código aberto desenvolvido nativamente no .net e no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="577a3-177">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="577a3-178">O NCache funciona tanto localmente quanto configurado como um cluster de cache distribuído para um aplicativo ASP.NET Core em execução no Azure ou em outras plataformas de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="577a3-178">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="577a3-179">Para instalar e configurar o NCache em seu computador local, consulte [Guia do ncache introdução para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="577a3-179">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="577a3-180">Para configurar o NCache:</span><span class="sxs-lookup"><span data-stu-id="577a3-180">To configure NCache:</span></span>

1. <span data-ttu-id="577a3-181">Instale o [NuGet de código-fonte aberto do NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="577a3-181">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="577a3-182">Configure o cluster de cache em [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="577a3-182">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="577a3-183">Adicione o seguinte código ao `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="577a3-183">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="577a3-184">Usar o cache distribuído</span><span class="sxs-lookup"><span data-stu-id="577a3-184">Use the distributed cache</span></span>

<span data-ttu-id="577a3-185">Para usar a interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, solicite uma instância do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de qualquer Construtor no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="577a3-185">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="577a3-186">A instância é fornecida pela [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="577a3-186">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="577a3-187">Quando o aplicativo de exemplo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="577a3-187">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="577a3-188">A hora atual é armazenada em cache usando <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (para obter mais informações, consulte [host genérico: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="577a3-188">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="577a3-189">Quando o aplicativo de exemplo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="577a3-189">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="577a3-190">A hora atual é armazenada em cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obter mais informações, consulte [Web host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="577a3-190">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="577a3-191">O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> na `IndexModel` para uso pela página de índice.</span><span class="sxs-lookup"><span data-stu-id="577a3-191">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="577a3-192">Cada vez que a página de índice é carregada, o cache é verificado quanto ao tempo em cache em `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="577a3-192">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="577a3-193">Se o tempo em cache não tiver expirado, a hora será exibida.</span><span class="sxs-lookup"><span data-stu-id="577a3-193">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="577a3-194">Se 20 segundos tiverem decorrido desde a última vez em que o tempo de cache foi acessado (na última vez em que essa página foi carregada), a página exibirá o *tempo de cache expirado*.</span><span class="sxs-lookup"><span data-stu-id="577a3-194">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="577a3-195">Atualize imediatamente a hora em cache para a hora atual selecionando o botão **Redefinir tempo de cache** .</span><span class="sxs-lookup"><span data-stu-id="577a3-195">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="577a3-196">O botão dispara o método do manipulador de `OnPostResetCachedTime`.</span><span class="sxs-lookup"><span data-stu-id="577a3-196">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="577a3-197">Não é necessário usar uma vida útil singleton ou com escopo para instâncias de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (pelo menos para as implementações internas).</span><span class="sxs-lookup"><span data-stu-id="577a3-197">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="577a3-198">Você também pode criar uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> onde você possa precisar de uma em vez de usar DI, mas criar uma instância no código pode tornar seu código mais difícil de testar e violar o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="577a3-198">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="577a3-199">Recomendações</span><span class="sxs-lookup"><span data-stu-id="577a3-199">Recommendations</span></span>

<span data-ttu-id="577a3-200">Ao decidir qual implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:</span><span class="sxs-lookup"><span data-stu-id="577a3-200">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="577a3-201">Infraestrutura existente</span><span class="sxs-lookup"><span data-stu-id="577a3-201">Existing infrastructure</span></span>
* <span data-ttu-id="577a3-202">Requisitos de desempenho</span><span class="sxs-lookup"><span data-stu-id="577a3-202">Performance requirements</span></span>
* <span data-ttu-id="577a3-203">Custo</span><span class="sxs-lookup"><span data-stu-id="577a3-203">Cost</span></span>
* <span data-ttu-id="577a3-204">Experiência da equipe</span><span class="sxs-lookup"><span data-stu-id="577a3-204">Team experience</span></span>

<span data-ttu-id="577a3-205">As soluções de cache geralmente dependem do armazenamento na memória para fornecer uma recuperação rápida dos dados armazenados em cache, mas a memória é um recurso limitado e dispendiosa de expandir.</span><span class="sxs-lookup"><span data-stu-id="577a3-205">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="577a3-206">Armazene somente dados usados em um cache.</span><span class="sxs-lookup"><span data-stu-id="577a3-206">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="577a3-207">Geralmente, um cache Redis fornece maior taxa de transferência e menor latência do que um cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="577a3-207">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="577a3-208">No entanto, o benchmark geralmente é necessário para determinar as características de desempenho das estratégias de cache.</span><span class="sxs-lookup"><span data-stu-id="577a3-208">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="577a3-209">Quando SQL Server é usado como um armazenamento de backup de cache distribuído, o uso do mesmo banco de dados para o cache e o armazenamento e a recuperação comuns do aplicativo podem afetar negativamente o desempenho de ambos.</span><span class="sxs-lookup"><span data-stu-id="577a3-209">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="577a3-210">É recomendável usar uma instância de SQL Server dedicada para o armazenamento de backup de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="577a3-210">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="577a3-211">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="577a3-211">Additional resources</span></span>

* [<span data-ttu-id="577a3-212">Cache Redis no Azure</span><span class="sxs-lookup"><span data-stu-id="577a3-212">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="577a3-213">Banco de dados SQL no Azure</span><span class="sxs-lookup"><span data-stu-id="577a3-213">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="577a3-214">[ASP.NET Core o provedor IDistributedCache para o NCache em farms da Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="577a3-214">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
