---
title: O cache no ASP.NET Core distribuído
author: guardrex
description: Saiba como usar um cache distribuído do ASP.NET Core para melhorar o desempenho do aplicativo e a escalabilidade, especialmente em um ambiente de farm de servidor ou de nuvem.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: performance/caching/distributed
ms.openlocfilehash: 7337ee3b823064c942832d8a44e4d4289bc4fd0e
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899418"
---
# <a name="distributed-caching-in-aspnet-core"></a>O cache no ASP.NET Core distribuído

Por [Steve Smith](https://ardalis.com/) e [Luke Latham](https://github.com/guardrex)

Um cache distribuído é um cache compartilhado por vários servidores de aplicativo, normalmente é mantidos como um serviço externo para os servidores de aplicativo que acessá-lo. Um cache distribuído pode melhorar o desempenho e escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.

Um cache distribuído tem várias vantagens sobre outros cenários de cache onde os dados armazenados em cache são armazenados em servidores de aplicativos individuais.

Quando os dados armazenados em cache são distribuídos, os dados:

* Está *coerente* (consistente) em todas as solicitações para vários servidores.
* Sobrevive a reinicializações do servidor e as implantações de aplicativo.
* Não use memória local.

Configuração de cache distribuído é específico da implementação. Este artigo descreve como configurar o SQL Server e distribuídos caches Redis. Implementações de terceiros também estão disponíveis, como [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)). Independentemente de qual implementação for selecionada, o aplicativo interage com o cache usando o <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

::: moniker range=">= aspnetcore-2.2"

Para usar um SQL Server distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) ou adicionar uma referência de pacote para o [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacote.

Para usar um Redis distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) e adicione uma referência de pacote para o [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) pacote. O pacote do Redis não está incluído no `Microsoft.AspNetCore.App` empacotar, portanto, você deve referenciar o pacote de Redis separadamente no seu arquivo de projeto.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Para usar um SQL Server distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) ou adicionar uma referência de pacote para o [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacote.

Para usar um Redis distributed cache, a referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) e adicione uma referência de pacote para o [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pacote. O pacote do Redis não está incluído no `Microsoft.AspNetCore.App` empacotar, portanto, você deve referenciar o pacote de Redis separadamente no seu arquivo de projeto.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Para usar um SQL Server distributed cache, a referência a [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) ou adicionar uma referência de pacote para o [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacote.

Para usar um Redis distributed cache, a referência a [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) ou adicionar uma referência de pacote para o [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pacote. O pacote de Redis está incluído no `Microsoft.AspNetCore.All` empacotar, para que você não precisa fazer referência ao pacote Redis separadamente no seu arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Para usar um SQL Server distributed cache, adicione uma referência de pacote para o [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacote.

Para usar um Redis cache distribuído, adicione uma referência de pacote para o [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pacote.

::: moniker-end

## <a name="idistributedcache-interface"></a>Interface IDistributedCache

O <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fornece os seguintes métodos para manipular itens na implementação de cache distribuído:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Aceita uma chave de cadeia de caracteres e recupera um item em cache como um `byte[]` matriz se encontrada no cache.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adiciona um item (como `byte[]` matriz) para o cache usando uma chave de cadeia de caracteres.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Remove um item de cache com base em sua chave de cadeia de caracteres.

## <a name="establish-distributed-caching-services"></a>Estabeleça serviços de cache distribuídos

Registrar uma implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> em `Startup.ConfigureServices`. Implementações fornecidos pela estrutura descritas neste tópico incluem:

* [Cache de memória distribuída](#distributed-memory-cache)
* [Cache distribuído do SQL Server](#distributed-sql-server-cache)
* [Cache distribuído do Redis](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>Cache de memória distribuída

O Cache de memória distribuída (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) é uma implementação fornecidos pela estrutura de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena os itens na memória. O Cache de memória distribuída não é um cache distribuído real. Itens armazenados em cache são armazenados por instância do aplicativo no servidor onde o aplicativo está sendo executado.

O Cache de memória distribuída é uma implementação úteis:

* No desenvolvimento e cenários de teste.
* Quando um único servidor é usado em consumo de memória e de produção não é um problema. Implementar os resumos de Cache distribuído memória, armazenamento de dados em cache. Ele permite para implementar que uma verdadeira distribuído a solução de cache no futuro se vários nós ou tolerância a falhas que se tornar necessário.

O aplicativo de exemplo utiliza o Cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

### <a name="distributed-sql-server-cache"></a>Cache distribuído do SQL Server

A implementação de Cache distribuído do SQL Server (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) permite que o cache distribuído usar um banco de dados do SQL Server como seu armazenamento de backup. Para criar uma tabela de item em cache do SQL Server em uma instância do SQL Server, você pode usar o `sql-cache` ferramenta. A ferramenta cria uma tabela com o nome e o esquema que você especificar.

::: moniker range="< aspnetcore-2.1"

Adicione `SqlConfig.Tools` para o `<ItemGroup>` elemento do arquivo de projeto e execute `dotnet restore`.

```xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.Extensions.Caching.SqlConfig.Tools"
                          Version="2.0.2" />
</ItemGroup>
```

::: moniker-end

Crie uma tabela no SQL Server executando o `sql-cache create` comando. Fornecer a instância do SQL Server (`Data Source`), banco de dados (`Initial Catalog`), esquema (por exemplo, `dbo`) e o nome da tabela (por exemplo, `TestCache`):

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:

```console
Table and index were created successfully.
```

A tabela criada pelo `sql-cache` ferramenta tem o esquema a seguir:

![Tabela de Cache do SQL Server](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Um aplicativo deve manipular valores de cache usando uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, e não um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.

O aplicativo de exemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> em um ambiente de desenvolvimento não:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=9-15)]

> [!NOTE]
> Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) normalmente são armazenados fora do controle do código-fonte (por exemplo, são armazenados pela [Secret Manager](xref:security/app-secrets) ou na *appSettings. JSON* / *appsettings. . {Environment} JSON* arquivos). A cadeia de caracteres de conexão pode conter credenciais que devem ser mantidas fora de sistemas de controle do código-fonte.

### <a name="distributed-redis-cache"></a>Cache Redis distribuído

[Redis](https://redis.io/) é um repositório de dados na memória do código-fonte aberto, que geralmente é usado como um cache distribuído. Você pode usar o Redis localmente, e você pode configurar uma [Cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo ASP.NET Core hospedados no Azure. Um aplicativo configura a implementação de cache usando um <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instância (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Para instalar o Redis em seu computador local:

* Instalar o [Redis Chocolatey pacote](https://chocolatey.org/packages/redis-64/).
* Executar `redis-server` em um prompt de comando.

## <a name="use-the-distributed-cache"></a>Usar o cache distribuído

Para usar o <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, solicitar uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de qualquer construtor no aplicativo. A instância é fornecida pela [injeção de dependência (DI)](xref:fundamentals/dependency-injection).

Quando o aplicativo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure`. A hora atual é armazenado em cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obter mais informações, consulte [Host da Web: Interface IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> para o `IndexModel` para uso pela página de índice.

Cada vez que a página de índice é carregada, o cache é verificado para o tempo em cache em `OnGetAsync`. Se ainda não tiver expirado o tempo em cache, a hora é exibida. Se 20 segundos decorridos desde a última vez em que o tempo em cache foi acessado (a última vez que esta página foi carregada), a página exibe *armazenado em cache tempo expirado*.

Atualizar imediatamente o tempo em cache para a hora atual, selecionando o **redefinição de tempo em cache** botão. Os gatilhos de botão a `OnPostResetCachedTime` método do manipulador.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Não é necessário usar um tempo de vida Singleton ou com escopo para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instâncias (pelo menos para as implementações internas).
>
> Você também pode criar uma <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> da instância onde você pode precisar de uma em vez de usar a DI, mas a criação de uma instância no código pode tornar seu código mais difícil de testar e viola o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recomendações

Ao decidir qual implementação de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:

* Infraestrutura existente
* Requisitos de desempenho
* Custo
* Experiência da equipe

Soluções de cache normalmente se baseiam em armazenamento na memória para fornecer recuperação rápida de dados armazenados em cache, mas a memória é um recurso limitado e caros expandir. Loja apenas dados usados normalmente em um cache.

Em geral, um cache Redis fornece maior taxa de transferência e latência mais baixa que o cache de SQL Server. No entanto, é geralmente necessário para determinar as características de desempenho de estratégias de cache de benchmark.

Quando o SQL Server é usado como um armazenamento de backup de cache distribuído, usar o mesmo banco de dados para o cache e armazenamento de dados comum do aplicativo e recuperação pode afetar negativamente o desempenho de ambos. É recomendável usar uma instância dedicada do SQL Server para o cache distribuído do repositório de backup.

## <a name="additional-resources"></a>Recursos adicionais

* [Redis Cache no Azure](https://azure.microsoft.com/documentation/services/redis-cache/)
* [Banco de dados SQL no Azure](https://azure.microsoft.com/documentation/services/sql-database/)
* [Provedor de IDistributedCache para NCache nos Farms da Web do ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
