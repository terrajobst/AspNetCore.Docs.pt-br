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
# <a name="distributed-caching-in-aspnet-core"></a>Caching distribuído no ASP.NET Core

De [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir)e [Steve Smith](https://ardalis.com/)

Um cache distribuído é um cache compartilhado por vários servidores de aplicativos, normalmente mantido como um serviço externo para os servidores de aplicativos que o acessam. Um cache distribuído pode melhorar o desempenho e a escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.

Um cache distribuído tem várias vantagens em relação A outros cenários de cache em que os dados armazenados em cache são armazenados em servidores de aplicativos individuais.

Quando os dados armazenados em cache são distribuídos, os dados:

* É *coerente* (consistente) entre solicitações para vários servidores.
* Sobreviver a reinicializações de servidor e implantações de aplicativo.
* Não usa memória local.

A configuração de cache distribuído é específica da implementação. Este artigo descreve como configurar os caches distribuídos SQL Server e Redis. Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)). Independentemente da implementação selecionada, o aplicativo interage com o cache usando a interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

::: moniker range=">= aspnetcore-3.0"

Para usar um SQL Server cache distribuído, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Para usar um cache distribuído Redis, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .

Para usar o cache distribuído do NCache, adicione uma referência de pacote ao pacote do [NCache. Microsoft. Extensions. Caching. Open](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) . O pacote Redis não está incluído no pacote `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.

Para usar o cache distribuído do NCache, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [NCache. Microsoft. Extensions. Caching. openize](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . O pacote do NCache não está incluído no pacote de `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote do NCache separadamente em seu arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) . O pacote Redis não está incluído no pacote `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.

Para usar o cache distribuído do NCache, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [NCache. Microsoft. Extensions. Caching. openize](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . O pacote do NCache não está incluído no pacote de `Microsoft.AspNetCore.App`, portanto, você deve fazer referência ao pacote do NCache separadamente em seu arquivo de projeto.

::: moniker-end

## <a name="idistributedcache-interface"></a>Interface IDistributedCache

A interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> fornece os seguintes métodos para manipular itens na implementação do cache distribuído:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; aceita uma chave de cadeia de caracteres e recupera um item em cache como uma matriz de `byte[]`, se encontrado no cache.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; adiciona um item (como matriz de `byte[]`) ao cache usando uma chave de cadeia de caracteres.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; remove um item de cache com base em sua chave de cadeia de caracteres.

## <a name="establish-distributed-caching-services"></a>Estabelecer serviços de cache distribuído

Registre uma implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `Startup.ConfigureServices`. As implementações fornecidas pelo Framework descritas neste tópico incluem:

* [Cache de memória distribuída](#distributed-memory-cache)
* [Cache SQL Server distribuído](#distributed-sql-server-cache)
* [Cache Redis distribuído](#distributed-redis-cache)
* [Cache do NCache distribuído](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Cache de memória distribuída

O cache de memória distribuída (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) é uma implementação fornecida pela estrutura de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena itens na memória. O cache de memória distribuída não é um cache distribuído real. Os itens armazenados em cache são armazenados pela instância do aplicativo no servidor em que o aplicativo está em execução.

O cache de memória distribuída é uma implementação útil:

* Em cenários de desenvolvimento e teste.
* Quando um único servidor é usado em produção e o consumo de memória não é um problema. A implementação do cache de memória distribuída abstrai o armazenamento de dados em cache. Ele permite implementar uma solução de cache distribuído verdadeira no futuro se vários nós ou tolerância a falhas se tornarem necessários.

O aplicativo de exemplo usa o cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento no `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a>Cache SQL Server distribuído

A implementação do cache SQL Server distribuído (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) permite que o cache distribuído use um banco de dados SQL Server como seu armazenamento de backup. Para criar uma SQL Server tabela de item em cache em uma instância de SQL Server, você pode usar a ferramenta de `sql-cache`. A ferramenta cria uma tabela com o nome e o esquema que você especificar.

Crie uma tabela no SQL Server executando o comando `sql-cache create`. Forneça a instância de SQL Server (`Data Source`), o banco de dados (`Initial Catalog`), o esquema (por exemplo, `dbo`) e o nome da tabela (por exemplo, `TestCache`):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:

```console
Table and index were created successfully.
```

A tabela criada pela ferramenta de `sql-cache` tem o seguinte esquema:

![Tabela de cache do SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Um aplicativo deve manipular valores de cache usando uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, não um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.

O aplicativo de exemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) normalmente são armazenados fora do controle do código-fonte (por exemplo, armazenado pelo [Gerenciador de segredo](xref:security/app-secrets) ou em *appSettings. JSON*/*appSettings. { Arquivos de ambiente}. JSON* ). A cadeia de conexão pode conter credenciais que devem ser mantidas fora dos sistemas de controle do código-fonte.

### <a name="distributed-redis-cache"></a>Cache Redis distribuído

O [Redis](https://redis.io/) é um armazenamento de dados na memória de software livre, que geralmente é usado como um cache distribuído. Você pode usar o Redis localmente e pode configurar um [cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo de ASP.NET Core hospedado pelo Azure.

::: moniker range=">= aspnetcore-3.0"

Um aplicativo configura a implementação de cache usando uma instância de <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Um aplicativo configura a implementação de cache usando uma instância de <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) em um ambiente de não desenvolvimento no `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Um aplicativo configura a implementação de cache usando uma instância de <xref:Microsoft.Extensions.Caching.Redis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

Para instalar o Redis em seu computador local:

1. Instale o [pacote Redis de Chocolatey](https://chocolatey.org/packages/redis-64/).
1. Execute `redis-server` em um prompt de comando.

### <a name="distributed-ncache-cache"></a>Cache do NCache distribuído

O [NCache](https://github.com/Alachisoft/NCache) é um cache distribuído na memória de código aberto desenvolvido nativamente no .net e no .NET Core. O NCache funciona tanto localmente quanto configurado como um cluster de cache distribuído para um aplicativo ASP.NET Core em execução no Azure ou em outras plataformas de hospedagem.

Para instalar e configurar o NCache em seu computador local, consulte [Guia do ncache introdução para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Para configurar o NCache:

1. Instale o [NuGet de código-fonte aberto do NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Configure o cluster de cache em [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Adicione o seguinte código a `Startup.ConfigureServices`:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Usar o cache distribuído

Para usar a interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, solicite uma instância do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de qualquer Construtor no aplicativo. A instância é fornecida pela [injeção de dependência (di)](xref:fundamentals/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

Quando o aplicativo de exemplo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure`. A hora atual é armazenada em cache usando <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (para obter mais informações, consulte [host genérico: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Quando o aplicativo de exemplo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure`. A hora atual é armazenada em cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obter mais informações, consulte [Web host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> na `IndexModel` para uso pela página de índice.

Cada vez que a página de índice é carregada, o cache é verificado quanto ao tempo em cache em `OnGetAsync`. Se o tempo em cache não tiver expirado, a hora será exibida. Se 20 segundos tiverem decorrido desde a última vez em que o tempo de cache foi acessado (na última vez em que essa página foi carregada), a página exibirá o *tempo de cache expirado*.

Atualize imediatamente a hora em cache para a hora atual selecionando o botão **Redefinir tempo de cache** . O botão dispara o método do manipulador de `OnPostResetCachedTime`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> Não é necessário usar uma vida útil singleton ou com escopo para instâncias de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (pelo menos para as implementações internas).
>
> Você também pode criar uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> onde você possa precisar de uma em vez de usar DI, mas criar uma instância no código pode tornar seu código mais difícil de testar e violar o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recomendações

Ao decidir qual implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:

* Infraestrutura existente
* Requisitos de desempenho
* Custo
* Experiência da equipe

As soluções de cache geralmente dependem do armazenamento na memória para fornecer uma recuperação rápida dos dados armazenados em cache, mas a memória é um recurso limitado e dispendiosa de expandir. Armazene somente dados usados em um cache.

Geralmente, um cache Redis fornece maior taxa de transferência e menor latência do que um cache SQL Server. No entanto, o benchmark geralmente é necessário para determinar as características de desempenho das estratégias de cache.

Quando SQL Server é usado como um armazenamento de backup de cache distribuído, o uso do mesmo banco de dados para o cache e o armazenamento e a recuperação comuns do aplicativo podem afetar negativamente o desempenho de ambos. É recomendável usar uma instância de SQL Server dedicada para o armazenamento de backup de cache distribuído.

## <a name="additional-resources"></a>Recursos adicionais

* [Cache Redis no Azure](/azure/azure-cache-for-redis/)
* [Banco de dados SQL no Azure](/azure/sql-database/)
* [ASP.NET Core o provedor IDistributedCache para o NCache em farms da Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
