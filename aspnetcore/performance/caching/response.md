---
title: Cache de resposta no ASP.NET Core
author: rick-anderson
description: Saiba como usar o cache de resposta para reduzir os requisitos de largura de banda e elevar o desempenho de aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/28/2019
uid: performance/caching/response
ms.openlocfilehash: efcf443b1487827fe6cf4d43b6dda69adf4d61fb
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345740"
---
# <a name="response-caching-in-aspnet-core"></a>Cache de resposta no ASP.NET Core

Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), e [Luke Latham](https://github.com/guardrex)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([como baixar](xref:index#how-to-download-a-sample))

O cache das respostas reduz o número de solicitações de que um cliente ou proxy faz a um servidor web. Cache de resposta também reduz a quantidade de trabalho do servidor web executa para gerar uma resposta. Cache de resposta é controlado por cabeçalhos que especificam como deseja cliente, o proxy e middleware para respostas em cache.

O [do atributo ResponseCache](#responsecache-attribute) participa na configuração de cabeçalhos, quais clientes podem honrar ao armazenar em cache as respostas do cache de resposta. [Middleware de cache de resposta](xref:performance/caching/middleware) pode ser usada para respostas em cache no servidor. O middleware pode usar <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> propriedades para influenciar o comportamento de cache do lado do servidor.

## <a name="http-based-response-caching"></a>Cache de resposta baseado em HTTP

O [especificação de cache do HTTP 1.1](https://tools.ietf.org/html/rfc7234) descreve como caches de Internet devem se comportar. É o cabeçalho HTTP principal usado para armazenar em cache [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), que é usada para especificar o cache *diretivas*. As diretivas de controlam o comportamento de cache conforme solicitações tornar sua maneira de clientes para servidores e as respostas passarão de servidores de volta aos clientes. Solicitações e respostas movem através de servidores proxy e servidores proxy devem também estar em conformidade com a especificação de cache do HTTP 1.1.

Common `Cache-Control` diretivas são mostradas na tabela a seguir.

| Diretiva                                                       | Ação |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Um cache pode armazenar a resposta. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | A resposta não deve ser armazenada por um cache compartilhado. Um cache privado pode armazenar e reutilizar a resposta. |
| [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | O cliente não aceita uma resposta cuja idade é maior que o número especificado de segundos. Exemplos: `max-age=60` (60 segundos), `max-age=2592000` (1 mês) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Em solicitações**: Um cache não deve usar uma resposta armazenada para atender à solicitação. O servidor de origem regenera a resposta para o cliente e o middleware atualiza a resposta armazenada em seu cache.<br><br>**Nas respostas**: A resposta não deve ser usada para uma solicitação subsequente sem validação no servidor de origem. |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Em solicitações**: Um cache não deve armazenar a solicitação.<br><br>**Nas respostas**: Um cache não deve armazenar qualquer parte da resposta. |

Outros cabeçalhos de cache que desempenham uma função em cache são mostrados na tabela a seguir.

| Cabeçalho                                                     | Função |
| ---------------------------------------------------------- | -------- |
| [Idade](https://tools.ietf.org/html/rfc7234#section-5.1)     | Uma estimativa da quantidade de tempo em segundos desde a resposta foi gerada ou validada com êxito no servidor de origem. |
| [Expira](https://tools.ietf.org/html/rfc7234#section-5.3) | O tempo após o qual a resposta é considerada obsoleta. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existe para com versões anteriores a compatibilidade com o HTTP/1.0 armazena em cache para a configuração `no-cache` comportamento. Se o `Cache-Control` cabeçalho estiver presente, o `Pragma` cabeçalho será ignorado. |
| [Variar](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Especifica que uma resposta em cache não deve ser enviada, a menos que todos os do `Vary` correspondem de campos de cabeçalho na solicitação original da resposta em cache e a nova solicitação. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>As diretivas de controle de Cache de solicitação de aspectos de cache baseada em HTTP

O [especificação de cache do HTTP 1.1 para o cabeçalho Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requer um cache para honrar válido `Cache-Control` cabeçalho enviado pelo cliente. Um cliente pode fazer solicitações com um `no-cache` força o servidor gere uma nova resposta para cada solicitação e o valor do cabeçalho.

Respeitando sempre cliente `Cache-Control` cabeçalhos de solicitação faz sentido se você considerar o objetivo do cache de HTTP. Sob a especificação oficial, cache destina-se para reduzir a sobrecarga de rede e latência de satisfazer as solicitações em uma rede de clientes, proxies e servidores. Ele não é necessariamente uma maneira de controlar a carga em um servidor de origem.

Não é possível controlar de desenvolvedor sobre o comportamento de cache ao usar o [Middleware de cache de resposta](xref:performance/caching/middleware) porque o middleware adere à especificação de cache oficial. [Planejado aprimoramentos para o middleware](https://github.com/aspnet/AspNetCore/issues/2612) são uma oportunidade para configurar o middleware para ignorar uma solicitação `Cache-Control` cabeçalho ao decidir servir uma resposta em cache. Aprimoramentos planejados oferecem uma oportunidade de melhor carga do servidor de controle.

## <a name="other-caching-technology-in-aspnet-core"></a>Outra tecnologia de armazenamento em cache no ASP.NET Core

### <a name="in-memory-caching"></a>Cache na memória

Cache na memória usa a memória do servidor para armazenar dados armazenados em cache. Esse tipo de cache é adequado para um único servidor ou vários servidores usando *sessões adesivas*. Sessões adesivas significa que as solicitações de um cliente sejam sempre roteadas para o mesmo servidor para processamento.

Para obter mais informações, consulte <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Cache distribuído

Use um cache distribuído para armazenar dados na memória quando o aplicativo é hospedado em um farm de servidor ou de nuvem. O cache é compartilhado entre os servidores que processam solicitações. Um cliente pode enviar uma solicitação que é tratada por qualquer servidor no grupo se os dados armazenados em cache para o cliente estão disponíveis. ASP.NET Core oferece o SQL Server e os caches distribuído do Redis.

Para obter mais informações, consulte <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Auxiliar de marca de cache

Armazenar em cache o conteúdo de uma exibição MVC ou a página do Razor com o auxiliar de marca de Cache. O auxiliar de marca de Cache usa o cache na memória para armazenar dados.

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Auxiliar de Marca de Cache Distribuído

Armazenar em cache o conteúdo de uma exibição MVC ou a página do Razor em cenários de farm da web ou nuvem distribuídos com o auxiliar de marca de Cache distribuído. O auxiliar de marca de Cache distribuído usa o SQL Server ou do Redis para armazenar dados.

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>Atributo ResponseCache

O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Especifica os parâmetros necessários para a configuração de cabeçalhos apropriados no cache de resposta.

> [!WARNING]
> Desabilite o cache para o conteúdo que contém informações para clientes autenticados. Armazenamento em cache deve ser habilitado apenas para conteúdo que não são alteradas com base na identidade do usuário ou se um usuário está conectado.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> a resposta armazenada de varia de acordo com os valores de determinada lista de chaves de consulta. Quando um valor único de `*` é fornecido, o middleware varia as respostas de todos os parâmetros de cadeia de caracteres de consulta de solicitação.

[Middleware de cache de resposta](xref:performance/caching/middleware) deve ser habilitado para definir o <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propriedade. Caso contrário, uma exceção de tempo de execução é gerada. Não há um cabeçalho HTTP correspondente para o <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propriedade. A propriedade é um recurso HTTP manipulado pelo Middleware de cache de resposta. Para o middleware servir uma resposta em cache, a cadeia de caracteres de consulta e o valor de cadeia de caracteres de consulta devem corresponder com uma solicitação anterior. Por exemplo, considere a sequência de solicitações e os resultados mostrados na tabela a seguir.

| Solicitação                          | Resultado                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Retornada do servidor. |
| `http://example.com?key1=value1` | Retornado do middleware. |
| `http://example.com?key1=value2` | Retornada do servidor. |

A primeira solicitação é retornada pelo servidor e armazenados em cache no middleware. A segunda solicitação é retornada pelo middleware, como a cadeia de caracteres de consulta corresponde a solicitação anterior. A terceira solicitação não está no cache do middleware porque o valor de cadeia de caracteres de consulta não corresponde a uma solicitação anterior.

O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> é usado para configurar e criar (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) um <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>. O <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter> realiza o trabalho de atualização de cabeçalhos HTTP apropriados e recursos da resposta. O filtro:

* Remove qualquer cabeçalho existente para `Vary`, `Cache-Control`, e `Pragma`.
* Grava os cabeçalhos apropriados com base nas propriedades definidas <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.
* Atualiza a resposta de armazenamento em cache o recurso HTTP se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> está definido.

### <a name="vary"></a>Variar

Esse cabeçalho só será gravado quando o <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propriedade está definida. A propriedade definida como o `Vary` valor da propriedade. O exemplo a seguir usa o <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propriedade:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Usando o aplicativo de exemplo, exiba os cabeçalhos de resposta com as ferramentas de rede do navegador. Os seguintes cabeçalhos de resposta são enviados com a resposta de página Cache1:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore e Location.None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> substitui a maioria das outras propriedades. Quando essa propriedade é definida como `true`, o `Cache-Control` cabeçalho é definido como `no-store`. Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> é definido como `None`:

* `Cache-Control` é definido como `no-store,no-cache`.
* `Pragma` é definido como `no-cache`.

Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> está `false` e <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> é `None`, `Cache-Control`, e `Pragma` são definidos como `no-cache`.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> normalmente é definida como `true` para páginas de erro. A página Cache2 no aplicativo de exemplo produz os cabeçalhos de resposta que instruem o cliente não para armazenar a resposta.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

O aplicativo de exemplo retorna a página do Cache2 com os seguintes cabeçalhos:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Local e a duração

Para habilitar o cache, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> deve ser definida como um valor positivo e <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> deve ser `Any` (o padrão) ou `Client`. Nesse caso, o `Cache-Control` cabeçalho é definido como o valor do local seguido o `max-age` da resposta.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>da opções dos `Any` e `Client` traduzir `Cache-Control` valores de cabeçalho da `public` e `private`, respectivamente. Conforme observado anteriormente, definindo <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> à `None` define ambas `Cache-Control` e `Pragma` cabeçalhos para `no-cache`.

O exemplo a seguir mostra o modelo de página do aplicativo de exemplo e os cabeçalhos produzidos pela configuração Cache3 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> e deixar o padrão <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> valor:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

O aplicativo de exemplo retorna a página de Cache3 com o cabeçalho a seguir:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Perfis de cache

Em vez de duplicar as configurações de cache de resposta em muitos atributos de ação do controlador, perfis de cache podem ser configurados como opções ao configurar a páginas do Razor/MVC no `Startup.ConfigureServices`. Valores encontrados em um perfil de cache referenciada são usados como padrões pelo <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> e são substituídas por qualquer propriedade especificada no atributo.

Configure um perfil de cache. O exemplo a seguir mostra um perfil de cache de segundo 30 no aplicativo de exemplo `Startup.ConfigureServices`:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

Referências de modelo de página do aplicativo de exemplo Cache4 o `Default30` perfil de cache:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> pode ser aplicado a:

* Manipuladores da página Razor (classes) &ndash; atributos não podem ser aplicados a métodos do manipulador.
* Controladores MVC (classes).
* As ações do MVC (métodos) &ndash; atributos de nível de método substituem as configurações especificadas no nível de classe de atributos.

O cabeçalho resultante aplicado a resposta da página Cache4 pelo `Default30` perfil de cache:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Recursos adicionais

* [Armazena as respostas em Caches](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
