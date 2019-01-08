---
title: Cache de resposta no ASP.NET Core
author: rick-anderson
description: Saiba como usar o cache de resposta para reduzir os requisitos de largura de banda e elevar o desempenho de aplicativos ASP.NET Core.
ms.author: riande
ms.date: 01/07/2018
uid: performance/caching/response
ms.openlocfilehash: 5fbcaddff6e53d01a19ba8a7455c719feb614326
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098942"
---
# <a name="response-caching-in-aspnet-core"></a>Cache de resposta no ASP.NET Core

Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), e [Luke Latham](https://github.com/guardrex)

> [!NOTE]
> Cache de resposta nas páginas do Razor está disponível no ASP.NET Core 2.1 ou posterior.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([como baixar](xref:index#how-to-download-a-sample))

O cache das respostas reduz o número de solicitações de que um cliente ou proxy faz a um servidor web. Cache de resposta também reduz a quantidade de trabalho do servidor web executa para gerar uma resposta. Cache de resposta é controlado por cabeçalhos que especificam como deseja cliente, o proxy e middleware para respostas em cache.

O [do atributo ResponseCache](#responsecache-attribute) participa na configuração de cabeçalhos, quais clientes podem honrar ao armazenar em cache as respostas do cache de resposta. [Middleware de cache de resposta](xref:performance/caching/middleware) pode ser usada para respostas em cache no servidor. O middleware pode usar `ResponseCache` propriedades para influenciar o comportamento de cache do lado do servidor do atributo.

## <a name="http-based-response-caching"></a>Cache de resposta baseado em HTTP

O [especificação de cache do HTTP 1.1](https://tools.ietf.org/html/rfc7234) descreve como caches de Internet devem se comportar. É o cabeçalho HTTP principal usado para armazenar em cache [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), que é usada para especificar o cache *diretivas*. As diretivas de controlam o comportamento de cache conforme as solicitações cheguem de clientes para servidores e respostas passarão de servidores de volta aos clientes. Solicitações e respostas movem através de servidores proxy e servidores proxy devem também estar em conformidade com a especificação de cache do HTTP 1.1.

Common `Cache-Control` diretivas são mostradas na tabela a seguir.

| Diretiva                                                       | Ação |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Um cache pode armazenar a resposta. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | A resposta não deve ser armazenada por um cache compartilhado. Um cache privado pode armazenar e reutilizar a resposta. |
| [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | O cliente não aceitará uma resposta cuja idade é maior que o número especificado de segundos. Exemplos: `max-age=60` (60 segundos), `max-age=2592000` (1 mês) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Em solicitações**: Um cache não deve usar uma resposta armazenada para atender à solicitação. Observação: O servidor de origem gera novamente a resposta para o cliente e o middleware atualiza a resposta armazenada em seu cache.<br><br>**Nas respostas**: A resposta não deve ser usada para uma solicitação subsequente sem validação no servidor de origem. |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Em solicitações**: Um cache não deve armazenar a solicitação.<br><br>**Nas respostas**: Um cache não deve armazenar qualquer parte da resposta. |

Outros cabeçalhos de cache que desempenham uma função em cache são mostrados na tabela a seguir.

| Cabeçalho                                                     | Função |
| ---------------------------------------------------------- | -------- |
| [Idade](https://tools.ietf.org/html/rfc7234#section-5.1)     | Uma estimativa da quantidade de tempo em segundos desde a resposta foi gerada ou validada com êxito no servidor de origem. |
| [Expira](https://tools.ietf.org/html/rfc7234#section-5.3) | A data/hora após o qual a resposta é considerada obsoleta. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existe para com versões anteriores a compatibilidade com o HTTP/1.0 armazena em cache para a configuração `no-cache` comportamento. Se o `Cache-Control` cabeçalho estiver presente, o `Pragma` cabeçalho será ignorado. |
| [Variar](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Especifica que uma resposta em cache não deve ser enviada, a menos que todos os do `Vary` correspondem de campos de cabeçalho na solicitação original da resposta em cache e a nova solicitação. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>As diretivas de controle de Cache de solicitação de aspectos de cache baseada em HTTP

O [especificação de cache do HTTP 1.1 para o cabeçalho Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requer um cache para honrar válido `Cache-Control` cabeçalho enviado pelo cliente. Um cliente pode fazer solicitações com um `no-cache` força o servidor gere uma nova resposta para cada solicitação e o valor do cabeçalho.

Respeitando sempre cliente `Cache-Control` cabeçalhos de solicitação faz sentido se você considerar o objetivo do cache de HTTP. Sob a especificação oficial, cache destina-se para reduzir a sobrecarga de rede e latência de satisfazer as solicitações em uma rede de clientes, proxies e servidores. Ele não é necessariamente uma maneira de controlar a carga em um servidor de origem.

Não é possível controlar de desenvolvedor sobre o comportamento de cache ao usar o [Middleware de cache de resposta](xref:performance/caching/middleware) porque o middleware adere à especificação de cache oficial. [Planejado aprimoramentos para o middleware](https://github.com/aspnet/AspNetCore/issues/2612) são uma oportunidade para configurar o middleware para ignorar uma solicitação `Cache-Control` cabeçalho ao decidir servir uma resposta em cache. Aprimoramentos planejados oferecem uma oportunidade de melhor carga do servidor de controle.

## <a name="other-caching-technology-in-aspnet-core"></a>Outra tecnologia de armazenamento em cache no ASP.NET Core

### <a name="in-memory-caching"></a>Cache na memória

Cache na memória usa a memória do servidor para armazenar dados armazenados em cache. Esse tipo de cache é adequado para um único servidor ou vários servidores usando *sessões adesivas*. Sessões adesivas significa que as solicitações de um cliente sejam sempre roteadas para o mesmo servidor para processamento.

Para obter mais informações, consulte [armazenar em Cache na memória](xref:performance/caching/memory).

### <a name="distributed-cache"></a>Cache distribuído

Use um cache distribuído para armazenar dados na memória quando o aplicativo é hospedado em um farm de servidor ou de nuvem. O cache é compartilhado entre os servidores que processam solicitações. Um cliente pode enviar uma solicitação que é tratada por qualquer servidor no grupo se os dados armazenados em cache para o cliente estão disponíveis. ASP.NET Core oferece o SQL Server e os caches distribuído do Redis.

Para obter mais informações, consulte <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Auxiliar de marca de cache

Você pode armazenar em cache o conteúdo de uma exibição MVC ou a página do Razor com o auxiliar de marca de Cache. O auxiliar de marca de Cache usa o cache na memória para armazenar dados.

Para obter mais informações, consulte [auxiliar de marca de Cache no ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="distributed-cache-tag-helper"></a>Auxiliar de Marca de Cache Distribuído

Você pode armazenar em cache o conteúdo de uma exibição MVC ou a página do Razor em nuvem distribuído ou cenários de web farm com o auxiliar de marca de Cache distribuído. O auxiliar de marca de Cache distribuído usa o SQL Server ou do Redis para armazenar dados.

Para obter mais informações, consulte [auxiliar de marca de Cache distribuído](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).

## <a name="responsecache-attribute"></a>Atributo ResponseCache

O [ResponseCacheAttribute](/dotnet/api/Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Especifica os parâmetros necessários para a configuração de cabeçalhos apropriados no cache de resposta.

> [!WARNING]
> Desabilite o cache para o conteúdo que contém informações para clientes autenticados. Armazenamento em cache deve ser habilitado apenas para conteúdo que não são alteradas com base na identidade do usuário ou se um usuário está conectado.

[VaryByQueryKeys](/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute.varybyquerykeys) a resposta armazenada de varia de acordo com os valores de determinada lista de chaves de consulta. Quando um valor único de `*` é fornecido, o middleware varia as respostas de todos os parâmetros de cadeia de caracteres de consulta de solicitação. `VaryByQueryKeys` exige o ASP.NET Core 1.1 ou posterior.

[Middleware de cache de resposta](xref:performance/caching/middleware) deve ser habilitado para definir o `VaryByQueryKeys` propriedade; caso contrário, uma exceção de tempo de execução é gerada. Não há um cabeçalho HTTP correspondente para o `VaryByQueryKeys` propriedade. A propriedade é um recurso HTTP tratado pelo Middleware de cache de resposta. Para o middleware servir uma resposta em cache, a cadeia de caracteres de consulta e o valor de cadeia de caracteres de consulta devem corresponder com uma solicitação anterior. Por exemplo, considere a sequência de solicitações e os resultados mostrados na tabela a seguir.

| Solicitação                          | Resultado                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | Retornado do servidor     |
| `http://example.com?key1=value1` | Retornado de middleware |
| `http://example.com?key1=value2` | Retornado do servidor     |

A primeira solicitação é retornada pelo servidor e armazenados em cache no middleware. A segunda solicitação é retornada pelo middleware, como a cadeia de caracteres de consulta corresponde a solicitação anterior. A terceira solicitação não está no cache do middleware porque o valor de cadeia de caracteres de consulta não corresponde a uma solicitação anterior. 

O `ResponseCacheAttribute` é usado para configurar e criar (via `IFilterFactory`) uma [ResponseCacheFilter](/dotnet/api/microsoft.aspnetcore.mvc.internal.responsecachefilter). O `ResponseCacheFilter` realiza o trabalho de atualização de cabeçalhos HTTP apropriados e recursos da resposta. O filtro:

* Remove qualquer cabeçalho existente para `Vary`, `Cache-Control`, e `Pragma`. 
* Grava os cabeçalhos apropriados com base nas propriedades definidas `ResponseCacheAttribute`. 
* Atualiza a resposta de armazenamento em cache o recurso HTTP se `VaryByQueryKeys` está definido.

### <a name="vary"></a>Variar

Esse cabeçalho só será gravado quando o `VaryByHeader` propriedade está definida. Ele é definido como o `Vary` valor da propriedade. O exemplo a seguir usa o `VaryByHeader` propriedade:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

Você pode exibir os cabeçalhos de resposta com as ferramentas de rede do seu navegador. A imagem a seguir mostra F12 borda de saída na **rede** guia quando o `About2` método de ação é atualizado:

![Na guia rede de borda F12 saída quando o método de ação About2 é chamado](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore e Location.None

`NoStore` substitui a maioria das outras propriedades. Quando essa propriedade é definida como `true`, o `Cache-Control` cabeçalho é definido como `no-store`. Se `Location` é definido como `None`:

* `Cache-Control` é definido como `no-store,no-cache`.
* `Pragma` é definido como `no-cache`.

Se `NoStore` está `false` e `Location` é `None`, `Cache-Control` e `Pragma` são definidos como `no-cache`.

Você normalmente define `NoStore` para `true` nas páginas de erro. Por exemplo:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

Isso resulta nos seguintes cabeçalhos:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Local e a duração

Para habilitar o cache, `Duration` deve ser definida como um valor positivo e `Location` deve ser `Any` (o padrão) ou `Client`. Nesse caso, o `Cache-Control` cabeçalho é definido como o valor do local seguido o `max-age` da resposta.

> [!NOTE]
> `Location`da opções dos `Any` e `Client` traduzir `Cache-Control` valores de cabeçalho da `public` e `private`, respectivamente. Conforme observado anteriormente, definindo `Location` à `None` define ambas `Cache-Control` e `Pragma` cabeçalhos para `no-cache`.

Veja abaixo um exemplo que mostra os cabeçalhos é produzido pela configuração `Duration` e deixar o padrão `Location` valor:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

Isso produz o seguinte cabeçalho:

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a>Perfis de cache

Em vez de duplicar `ResponseCache` configurações de muitos atributos de ação do controlador, perfis de cache podem ser configuradas como opções ao configurar o MVC em de `ConfigureServices` método na `Startup`. Valores encontrados em um perfil de cache referenciada são usados como padrões pelo `ResponseCache` de atributos e são substituídas por qualquer propriedade especificada no atributo.

Como configurar um perfil de cache:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

Um perfil de cache de referência:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

O `ResponseCache` atributo pode ser aplicado a ações (métodos) e controladores (classes). Atributos de nível de método substituem as configurações especificadas no nível de classe de atributos.

No exemplo acima, um atributo de nível de classe especifica uma duração de 30 segundos, enquanto um atributo de nível de método faz referência a um perfil de cache com uma duração definida como 60 segundos.

O cabeçalho resultante:

```
Cache-Control: public,max-age=60
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
