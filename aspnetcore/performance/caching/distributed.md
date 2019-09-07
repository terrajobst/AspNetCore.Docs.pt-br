---
title: Caching distribuído no ASP.NET Core
author: guardrex
description: Saiba como usar um ASP.NET Core cache distribuído para melhorar o desempenho e a escalabilidade do aplicativo, especialmente em um ambiente de nuvem ou de farm de servidores.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: performance/caching/distributed
ms.openlocfilehash: 8417463038bcdc0f77852bec3c3bb8a618153009
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773849"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="948c2-103">Caching distribuído no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="948c2-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="948c2-104">De [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="948c2-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="948c2-105">Um cache distribuído é um cache compartilhado por vários servidores de aplicativos, normalmente mantido como um serviço externo para os servidores de aplicativos que o acessam.</span><span class="sxs-lookup"><span data-stu-id="948c2-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="948c2-106">Um cache distribuído pode melhorar o desempenho e a escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="948c2-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="948c2-107">Um cache distribuído tem várias vantagens em relação A outros cenários de cache em que os dados armazenados em cache são armazenados em servidores de aplicativos individuais.</span><span class="sxs-lookup"><span data-stu-id="948c2-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="948c2-108">Quando os dados armazenados em cache são distribuídos, os dados:</span><span class="sxs-lookup"><span data-stu-id="948c2-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="948c2-109">É *coerente* (consistente) entre solicitações para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="948c2-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="948c2-110">Sobreviver a reinicializações de servidor e implantações de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="948c2-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="948c2-111">Não usa memória local.</span><span class="sxs-lookup"><span data-stu-id="948c2-111">Doesn't use local memory.</span></span>

<span data-ttu-id="948c2-112">A configuração de cache distribuído é específica da implementação.</span><span class="sxs-lookup"><span data-stu-id="948c2-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="948c2-113">Este artigo descreve como configurar os caches distribuídos SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="948c2-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="948c2-114">Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="948c2-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="948c2-115">Independentemente da implementação selecionada, o aplicativo interage com o cache usando a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span><span class="sxs-lookup"><span data-stu-id="948c2-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="948c2-116">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="948c2-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="948c2-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="948c2-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="948c2-118">Para usar um SQL Server cache distribuído, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="948c2-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="948c2-119">Para usar um cache distribuído Redis, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="948c2-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="948c2-120">Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="948c2-120">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="948c2-121">Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="948c2-121">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="948c2-122">O pacote Redis não está incluído no `Microsoft.AspNetCore.App` pacote, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="948c2-122">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="948c2-123">Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="948c2-123">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="948c2-124">Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="948c2-124">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="948c2-125">O pacote Redis não está incluído no `Microsoft.AspNetCore.App` pacote, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="948c2-125">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="948c2-126">Interface IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="948c2-126">IDistributedCache interface</span></span>

<span data-ttu-id="948c2-127">A <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fornece os seguintes métodos para manipular itens na implementação do cache distribuído:</span><span class="sxs-lookup"><span data-stu-id="948c2-127">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="948c2-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> `byte[]` Aceita uma chave de cadeia de caracteres e recupera um item em cache como uma matriz, se encontrado no cache. &ndash;</span><span class="sxs-lookup"><span data-stu-id="948c2-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="948c2-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>`byte[]` , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> Adiciona&ndash; um item (como matriz) ao cache usando uma chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="948c2-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="948c2-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ,&ndash; Atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).</span><span class="sxs-lookup"><span data-stu-id="948c2-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="948c2-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> ,&ndash; Remove um item de cache com base em sua chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="948c2-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="948c2-132">Estabelecer serviços de cache distribuído</span><span class="sxs-lookup"><span data-stu-id="948c2-132">Establish distributed caching services</span></span>

<span data-ttu-id="948c2-133">Registre uma implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="948c2-133">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="948c2-134">As implementações fornecidas pelo Framework descritas neste tópico incluem:</span><span class="sxs-lookup"><span data-stu-id="948c2-134">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="948c2-135">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="948c2-135">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="948c2-136">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="948c2-136">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="948c2-137">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="948c2-137">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="948c2-138">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="948c2-138">Distributed Memory Cache</span></span>

<span data-ttu-id="948c2-139">O cache de memória distribuída (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) é uma implementação fornecida pela estrutura do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena itens na memória.</span><span class="sxs-lookup"><span data-stu-id="948c2-139">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="948c2-140">O cache de memória distribuída não é um cache distribuído real.</span><span class="sxs-lookup"><span data-stu-id="948c2-140">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="948c2-141">Os itens armazenados em cache são armazenados pela instância do aplicativo no servidor em que o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="948c2-141">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="948c2-142">O cache de memória distribuída é uma implementação útil:</span><span class="sxs-lookup"><span data-stu-id="948c2-142">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="948c2-143">Em cenários de desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="948c2-143">In development and testing scenarios.</span></span>
* <span data-ttu-id="948c2-144">Quando um único servidor é usado em produção e o consumo de memória não é um problema.</span><span class="sxs-lookup"><span data-stu-id="948c2-144">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="948c2-145">A implementação do cache de memória distribuída abstrai o armazenamento de dados em cache.</span><span class="sxs-lookup"><span data-stu-id="948c2-145">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="948c2-146">Ele permite implementar uma solução de cache distribuído verdadeira no futuro se vários nós ou tolerância a falhas se tornarem necessários.</span><span class="sxs-lookup"><span data-stu-id="948c2-146">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="948c2-147">O aplicativo de exemplo usa o cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento `Startup.ConfigureServices`no:</span><span class="sxs-lookup"><span data-stu-id="948c2-147">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="948c2-148">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="948c2-148">Distributed SQL Server Cache</span></span>

<span data-ttu-id="948c2-149">A implementação do cache SQL Server distribuído<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>() permite que o cache distribuído use um banco de dados SQL Server como seu armazenamento de backup.</span><span class="sxs-lookup"><span data-stu-id="948c2-149">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="948c2-150">Para criar uma SQL Server tabela de item em cache em uma instância de SQL Server, você pode `sql-cache` usar a ferramenta.</span><span class="sxs-lookup"><span data-stu-id="948c2-150">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="948c2-151">A ferramenta cria uma tabela com o nome e o esquema que você especificar.</span><span class="sxs-lookup"><span data-stu-id="948c2-151">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="948c2-152">Crie uma tabela no SQL Server executando o `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="948c2-152">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="948c2-153">Forneça a SQL Server instância (`Data Source`), banco de`Initial Catalog`dados (), esquema (por `dbo`exemplo,) e nome da tabela (por `TestCache`exemplo,):</span><span class="sxs-lookup"><span data-stu-id="948c2-153">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="948c2-154">Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:</span><span class="sxs-lookup"><span data-stu-id="948c2-154">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="948c2-155">A tabela criada pela `sql-cache` ferramenta tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="948c2-155">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela de cache do SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="948c2-157">Um aplicativo deve manipular valores de cache usando uma instância <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>do, não <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>um.</span><span class="sxs-lookup"><span data-stu-id="948c2-157">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="948c2-158">O aplicativo de exemplo <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> implementa em um ambiente de não desenvolvimento `Startup.ConfigureServices`no:</span><span class="sxs-lookup"><span data-stu-id="948c2-158">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="948c2-159">Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> , <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>e) normalmente são armazenados fora do controle do código-fonte (por exemplo, armazenado pelo [Gerenciador de segredo](xref:security/app-secrets) ou em *appSettings. JSON*/*appSettings. { Arquivos de ambiente}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="948c2-159">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="948c2-160">A cadeia de conexão pode conter credenciais que devem ser mantidas fora dos sistemas de controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="948c2-160">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="948c2-161">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="948c2-161">Distributed Redis Cache</span></span>

<span data-ttu-id="948c2-162">O [Redis](https://redis.io/) é um armazenamento de dados na memória de software livre, que geralmente é usado como um cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="948c2-162">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="948c2-163">Você pode usar o Redis localmente e pode configurar um [cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo de ASP.NET Core hospedado pelo Azure.</span><span class="sxs-lookup"><span data-stu-id="948c2-163">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="948c2-164">Um aplicativo configura a implementação do cache usando uma <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instância (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="948c2-164">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="948c2-165">Um aplicativo configura a implementação do cache usando uma <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instância (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="948c2-165">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="948c2-166">Um aplicativo configura a implementação do cache usando uma <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instância (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="948c2-166">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="948c2-167">Para instalar o Redis em seu computador local:</span><span class="sxs-lookup"><span data-stu-id="948c2-167">To install Redis on your local machine:</span></span>

* <span data-ttu-id="948c2-168">Instale o [pacote Redis de Chocolatey](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="948c2-168">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="948c2-169">Execute `redis-server` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="948c2-169">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="948c2-170">Usar o cache distribuído</span><span class="sxs-lookup"><span data-stu-id="948c2-170">Use the distributed cache</span></span>

<span data-ttu-id="948c2-171">Para usar a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, solicite uma instância <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de de qualquer Construtor no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="948c2-171">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="948c2-172">A instância é fornecida pela [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="948c2-172">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="948c2-173">Quando o aplicativo de exemplo é <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> iniciado, é injetado no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="948c2-173">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="948c2-174">A hora atual é armazenada em <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> cache usando (para obter mais [informações, consulte host genérico: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="948c2-174">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="948c2-175">Quando o aplicativo de exemplo é <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> iniciado, é injetado no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="948c2-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="948c2-176">A hora atual é armazenada em <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> cache usando (para obter mais [informações, consulte host da Web: Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)IApplicationLifetime):</span><span class="sxs-lookup"><span data-stu-id="948c2-176">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="948c2-177">O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` no para ser usado pela página de índice.</span><span class="sxs-lookup"><span data-stu-id="948c2-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="948c2-178">Cada vez que a página de índice é carregada, o cache é verificado quanto ao tempo `OnGetAsync`em cache.</span><span class="sxs-lookup"><span data-stu-id="948c2-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="948c2-179">Se o tempo em cache não tiver expirado, a hora será exibida.</span><span class="sxs-lookup"><span data-stu-id="948c2-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="948c2-180">Se 20 segundos tiverem decorrido desde a última vez em que o tempo de cache foi acessado (na última vez em que essa página foi carregada), a página exibirá o *tempo de cache expirado*.</span><span class="sxs-lookup"><span data-stu-id="948c2-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="948c2-181">Atualize imediatamente a hora em cache para a hora atual selecionando o botão **Redefinir tempo de cache** .</span><span class="sxs-lookup"><span data-stu-id="948c2-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="948c2-182">O botão dispara o `OnPostResetCachedTime` método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="948c2-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="948c2-183">Não é necessário usar um singleton ou tempo de vida com escopo para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instâncias (pelo menos para as implementações internas).</span><span class="sxs-lookup"><span data-stu-id="948c2-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="948c2-184">Você também pode criar uma <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instância onde você pode precisar de uma em vez de usar di, mas criar uma instância no código pode tornar seu código mais difícil de testar e violar o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="948c2-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="948c2-185">Recomendações</span><span class="sxs-lookup"><span data-stu-id="948c2-185">Recommendations</span></span>

<span data-ttu-id="948c2-186">Ao decidir qual implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:</span><span class="sxs-lookup"><span data-stu-id="948c2-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="948c2-187">Infraestrutura existente</span><span class="sxs-lookup"><span data-stu-id="948c2-187">Existing infrastructure</span></span>
* <span data-ttu-id="948c2-188">Requisitos de desempenho</span><span class="sxs-lookup"><span data-stu-id="948c2-188">Performance requirements</span></span>
* <span data-ttu-id="948c2-189">Custo</span><span class="sxs-lookup"><span data-stu-id="948c2-189">Cost</span></span>
* <span data-ttu-id="948c2-190">Experiência da equipe</span><span class="sxs-lookup"><span data-stu-id="948c2-190">Team experience</span></span>

<span data-ttu-id="948c2-191">As soluções de cache geralmente dependem do armazenamento na memória para fornecer uma recuperação rápida dos dados armazenados em cache, mas a memória é um recurso limitado e dispendiosa de expandir.</span><span class="sxs-lookup"><span data-stu-id="948c2-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="948c2-192">Armazene somente dados usados em um cache.</span><span class="sxs-lookup"><span data-stu-id="948c2-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="948c2-193">Geralmente, um cache Redis fornece maior taxa de transferência e menor latência do que um cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="948c2-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="948c2-194">No entanto, o benchmark geralmente é necessário para determinar as características de desempenho das estratégias de cache.</span><span class="sxs-lookup"><span data-stu-id="948c2-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="948c2-195">Quando SQL Server é usado como um armazenamento de backup de cache distribuído, o uso do mesmo banco de dados para o cache e o armazenamento e a recuperação comuns do aplicativo podem afetar negativamente o desempenho de ambos.</span><span class="sxs-lookup"><span data-stu-id="948c2-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="948c2-196">É recomendável usar uma instância de SQL Server dedicada para o armazenamento de backup de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="948c2-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="948c2-197">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="948c2-197">Additional resources</span></span>

* [<span data-ttu-id="948c2-198">Cache Redis no Azure</span><span class="sxs-lookup"><span data-stu-id="948c2-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="948c2-199">Banco de dados SQL no Azure</span><span class="sxs-lookup"><span data-stu-id="948c2-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="948c2-200">[ASP.NET Core o provedor IDistributedCache para o NCache em farms da Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="948c2-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
