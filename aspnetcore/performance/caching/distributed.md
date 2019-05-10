---
title: O cache no ASP.NET Core distribuído
author: guardrex
description: Saiba como usar um cache distribuído do ASP.NET Core para melhorar o desempenho do aplicativo e a escalabilidade, especialmente em um ambiente de farm de servidor ou de nuvem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: performance/caching/distributed
ms.openlocfilehash: 871e27c068de3a465cedb517418a35a87f992300
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085833"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="88563-103">O cache no ASP.NET Core distribuído</span><span class="sxs-lookup"><span data-stu-id="88563-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="88563-104">Por [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="88563-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="88563-105">Um cache distribuído é um cache compartilhado por vários servidores de aplicativo, normalmente é mantidos como um serviço externo para os servidores de aplicativo que acessá-lo.</span><span class="sxs-lookup"><span data-stu-id="88563-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="88563-106">Um cache distribuído pode melhorar o desempenho e escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="88563-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="88563-107">Um cache distribuído tem várias vantagens sobre outros cenários de cache onde os dados armazenados em cache são armazenados em servidores de aplicativos individuais.</span><span class="sxs-lookup"><span data-stu-id="88563-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="88563-108">Quando os dados armazenados em cache são distribuídos, os dados:</span><span class="sxs-lookup"><span data-stu-id="88563-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="88563-109">Está *coerente* (consistente) em todas as solicitações para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="88563-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="88563-110">Sobrevive a reinicializações do servidor e as implantações de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="88563-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="88563-111">Não use memória local.</span><span class="sxs-lookup"><span data-stu-id="88563-111">Doesn't use local memory.</span></span>

<span data-ttu-id="88563-112">Configuração de cache distribuído é específico da implementação.</span><span class="sxs-lookup"><span data-stu-id="88563-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="88563-113">Este artigo descreve como configurar o SQL Server e distribuídos caches Redis.</span><span class="sxs-lookup"><span data-stu-id="88563-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="88563-114">Implementações de terceiros também estão disponíveis, como [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="88563-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="88563-115">Independentemente de qual implementação for selecionada, o aplicativo interage com o cache usando o <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span><span class="sxs-lookup"><span data-stu-id="88563-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="88563-116">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88563-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="88563-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="88563-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="88563-118">Para usar um SQL Server distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) ou adicionar uma referência de pacote para o [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacote.</span><span class="sxs-lookup"><span data-stu-id="88563-118">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="88563-119">Para usar um Redis distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) e adicione uma referência de pacote para o [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) pacote.</span><span class="sxs-lookup"><span data-stu-id="88563-119">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="88563-120">O pacote do Redis não está incluído no `Microsoft.AspNetCore.App` empacotar, portanto, você deve referenciar o pacote de Redis separadamente no seu arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="88563-120">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="88563-121">Para usar um SQL Server distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) ou adicionar uma referência de pacote para o [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacote.</span><span class="sxs-lookup"><span data-stu-id="88563-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="88563-122">Para usar um Redis distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) e adicione uma referência de pacote para o [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pacote.</span><span class="sxs-lookup"><span data-stu-id="88563-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="88563-123">O pacote do Redis não está incluído no `Microsoft.AspNetCore.App` empacotar, portanto, você deve referenciar o pacote de Redis separadamente no seu arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="88563-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="88563-124">Interface IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="88563-124">IDistributedCache interface</span></span>

<span data-ttu-id="88563-125">O <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fornece os seguintes métodos para manipular itens na implementação de cache distribuído:</span><span class="sxs-lookup"><span data-stu-id="88563-125">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="88563-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Aceita uma chave de cadeia de caracteres e recupera um item em cache como um `byte[]` matriz se encontrada no cache.</span><span class="sxs-lookup"><span data-stu-id="88563-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="88563-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adiciona um item (como `byte[]` matriz) para o cache usando uma chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="88563-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="88563-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).</span><span class="sxs-lookup"><span data-stu-id="88563-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="88563-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Remove um item de cache com base em sua chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="88563-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="88563-130">Estabeleça serviços de cache distribuídos</span><span class="sxs-lookup"><span data-stu-id="88563-130">Establish distributed caching services</span></span>

<span data-ttu-id="88563-131">Registrar uma implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="88563-131">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="88563-132">Implementações fornecidos pela estrutura descritas neste tópico incluem:</span><span class="sxs-lookup"><span data-stu-id="88563-132">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="88563-133">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="88563-133">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="88563-134">Cache distribuído do SQL Server</span><span class="sxs-lookup"><span data-stu-id="88563-134">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="88563-135">Cache distribuído do Redis</span><span class="sxs-lookup"><span data-stu-id="88563-135">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="88563-136">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="88563-136">Distributed Memory Cache</span></span>

<span data-ttu-id="88563-137">O Cache de memória distribuída (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) é uma implementação fornecidos pela estrutura de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena os itens na memória.</span><span class="sxs-lookup"><span data-stu-id="88563-137">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="88563-138">O Cache de memória distribuída não é um cache distribuído real.</span><span class="sxs-lookup"><span data-stu-id="88563-138">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="88563-139">Itens armazenados em cache são armazenados por instância do aplicativo no servidor onde o aplicativo está sendo executado.</span><span class="sxs-lookup"><span data-stu-id="88563-139">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="88563-140">O Cache de memória distribuída é uma implementação úteis:</span><span class="sxs-lookup"><span data-stu-id="88563-140">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="88563-141">No desenvolvimento e cenários de teste.</span><span class="sxs-lookup"><span data-stu-id="88563-141">In development and testing scenarios.</span></span>
* <span data-ttu-id="88563-142">Quando um único servidor é usado em consumo de memória e de produção não é um problema.</span><span class="sxs-lookup"><span data-stu-id="88563-142">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="88563-143">Implementar os resumos de Cache distribuído memória, armazenamento de dados em cache.</span><span class="sxs-lookup"><span data-stu-id="88563-143">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="88563-144">Ele permite para implementar que uma verdadeira distribuído a solução de cache no futuro se vários nós ou tolerância a falhas que se tornar necessário.</span><span class="sxs-lookup"><span data-stu-id="88563-144">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="88563-145">O aplicativo de exemplo utiliza o Cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="88563-145">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="88563-146">Cache distribuído do SQL Server</span><span class="sxs-lookup"><span data-stu-id="88563-146">Distributed SQL Server Cache</span></span>

<span data-ttu-id="88563-147">A implementação de Cache distribuído do SQL Server (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) permite que o cache distribuído usar um banco de dados do SQL Server como seu armazenamento de backup.</span><span class="sxs-lookup"><span data-stu-id="88563-147">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="88563-148">Para criar uma tabela de item em cache do SQL Server em uma instância do SQL Server, você pode usar o `sql-cache` ferramenta.</span><span class="sxs-lookup"><span data-stu-id="88563-148">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="88563-149">A ferramenta cria uma tabela com o nome e o esquema que você especificar.</span><span class="sxs-lookup"><span data-stu-id="88563-149">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="88563-150">Crie uma tabela no SQL Server executando o `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="88563-150">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="88563-151">Fornecer a instância do SQL Server (`Data Source`), banco de dados (`Initial Catalog`), esquema (por exemplo, `dbo`) e o nome da tabela (por exemplo, `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="88563-151">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="88563-152">Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:</span><span class="sxs-lookup"><span data-stu-id="88563-152">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="88563-153">A tabela criada pelo `sql-cache` ferramenta tem o esquema a seguir:</span><span class="sxs-lookup"><span data-stu-id="88563-153">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela de Cache do SQL Server](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="88563-155">Um aplicativo deve manipular valores de cache usando uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, e não um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span><span class="sxs-lookup"><span data-stu-id="88563-155">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="88563-156">O aplicativo de exemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> em um ambiente de desenvolvimento não em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="88563-156">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="88563-157">Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) normalmente são armazenados fora do controle do código-fonte (por exemplo, são armazenados pela [Secret Manager](xref:security/app-secrets) ou na *appSettings. JSON* / *appsettings. . {ENVIRONMENT} JSON* arquivos).</span><span class="sxs-lookup"><span data-stu-id="88563-157">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="88563-158">A cadeia de caracteres de conexão pode conter credenciais que devem ser mantidas fora de sistemas de controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="88563-158">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="88563-159">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="88563-159">Distributed Redis Cache</span></span>

<span data-ttu-id="88563-160">[Redis](https://redis.io/) é um repositório de dados na memória do código-fonte aberto, que geralmente é usado como um cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="88563-160">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="88563-161">Você pode usar o Redis localmente, e você pode configurar uma [Cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo ASP.NET Core hospedados no Azure.</span><span class="sxs-lookup"><span data-stu-id="88563-161">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="88563-162">Um aplicativo configura a implementação de cache usando um <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instância (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) em um ambiente de desenvolvimento não em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="88563-162">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="88563-163">Um aplicativo configura a implementação de cache usando um <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instância (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="88563-163">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="88563-164">Para instalar o Redis em seu computador local:</span><span class="sxs-lookup"><span data-stu-id="88563-164">To install Redis on your local machine:</span></span>

* <span data-ttu-id="88563-165">Instalar o [Redis Chocolatey pacote](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="88563-165">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="88563-166">Executar `redis-server` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="88563-166">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="88563-167">Usar o cache distribuído</span><span class="sxs-lookup"><span data-stu-id="88563-167">Use the distributed cache</span></span>

<span data-ttu-id="88563-168">Para usar o <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, solicitar uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de qualquer construtor no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="88563-168">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="88563-169">A instância é fornecida pela [injeção de dependência (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="88563-169">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="88563-170">Quando o aplicativo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="88563-170">When the app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="88563-171">A hora atual é armazenado em cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obter mais informações, consulte [Host da Web: Interface IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="88563-171">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="88563-172">O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> para o `IndexModel` para uso pela página de índice.</span><span class="sxs-lookup"><span data-stu-id="88563-172">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="88563-173">Cada vez que a página de índice é carregada, o cache é verificado para o tempo em cache em `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="88563-173">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="88563-174">Se ainda não tiver expirado o tempo em cache, a hora é exibida.</span><span class="sxs-lookup"><span data-stu-id="88563-174">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="88563-175">Se 20 segundos decorridos desde a última vez em que o tempo em cache foi acessado (a última vez que esta página foi carregada), a página exibe *armazenado em cache tempo expirado*.</span><span class="sxs-lookup"><span data-stu-id="88563-175">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="88563-176">Atualizar imediatamente o tempo em cache para a hora atual, selecionando o **redefinição de tempo em cache** botão.</span><span class="sxs-lookup"><span data-stu-id="88563-176">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="88563-177">Os gatilhos de botão a `OnPostResetCachedTime` método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="88563-177">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="88563-178">Não é necessário usar um tempo de vida Singleton ou com escopo para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instâncias (pelo menos para as implementações internas).</span><span class="sxs-lookup"><span data-stu-id="88563-178">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="88563-179">Você também pode criar uma <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> da instância onde você pode precisar de uma em vez de usar a DI, mas a criação de uma instância no código pode tornar seu código mais difícil de testar e viola o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="88563-179">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="88563-180">Recomendações</span><span class="sxs-lookup"><span data-stu-id="88563-180">Recommendations</span></span>

<span data-ttu-id="88563-181">Ao decidir qual implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:</span><span class="sxs-lookup"><span data-stu-id="88563-181">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="88563-182">Infraestrutura existente</span><span class="sxs-lookup"><span data-stu-id="88563-182">Existing infrastructure</span></span>
* <span data-ttu-id="88563-183">Requisitos de desempenho</span><span class="sxs-lookup"><span data-stu-id="88563-183">Performance requirements</span></span>
* <span data-ttu-id="88563-184">Custo</span><span class="sxs-lookup"><span data-stu-id="88563-184">Cost</span></span>
* <span data-ttu-id="88563-185">Experiência da equipe</span><span class="sxs-lookup"><span data-stu-id="88563-185">Team experience</span></span>

<span data-ttu-id="88563-186">Soluções de cache normalmente se baseiam em armazenamento na memória para fornecer recuperação rápida de dados armazenados em cache, mas a memória é um recurso limitado e caros expandir.</span><span class="sxs-lookup"><span data-stu-id="88563-186">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="88563-187">Loja apenas dados usados normalmente em um cache.</span><span class="sxs-lookup"><span data-stu-id="88563-187">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="88563-188">Em geral, um cache Redis fornece maior taxa de transferência e latência mais baixa que o cache de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="88563-188">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="88563-189">No entanto, é geralmente necessário para determinar as características de desempenho de estratégias de cache de benchmark.</span><span class="sxs-lookup"><span data-stu-id="88563-189">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="88563-190">Quando o SQL Server é usado como um armazenamento de backup de cache distribuído, usar o mesmo banco de dados para o cache e armazenamento de dados comum do aplicativo e recuperação pode afetar negativamente o desempenho de ambos.</span><span class="sxs-lookup"><span data-stu-id="88563-190">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="88563-191">É recomendável usar uma instância dedicada do SQL Server para o cache distribuído do repositório de backup.</span><span class="sxs-lookup"><span data-stu-id="88563-191">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88563-192">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="88563-192">Additional resources</span></span>

* [<span data-ttu-id="88563-193">Redis Cache no Azure</span><span class="sxs-lookup"><span data-stu-id="88563-193">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="88563-194">Banco de dados SQL no Azure</span><span class="sxs-lookup"><span data-stu-id="88563-194">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="88563-195">[Provedor de IDistributedCache para NCache nos Farms da Web do ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="88563-195">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
