---
title: Auxiliar de Marca de Cache Distribuído no ASP.NET Core
author: pkellner
description: Saiba como usar o Auxiliar de marca de cache distribuído.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: e5100d7244600358186b653073990985f48434a7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809049"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>Auxiliar de Marca de Cache Distribuído no ASP.NET Core

Por [Peter Kellner](https://peterkellner.net) e [Luke Latham](https://github.com/guardrex)

O Auxiliar de Marca de Cache Distribuído fornece a capacidade de melhorar consideravelmente o desempenho do aplicativo ASP.NET Core armazenando seu conteúdo em cache em uma fonte de cache distribuído.

Para obter uma visão geral de Auxiliares de marcação, consulte <xref:mvc/views/tag-helpers/intro>.

O Auxiliar de Marca de Cache Distribuído herda da mesma classe base do Auxiliar de Marca de Cache. Todos os atributos de [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) estão disponíveis ao Auxiliar de Marca Distribuído.

O Auxiliar de Marca de Cache distribuído usa [injeção de construtor](xref:fundamentals/dependency-injection#constructor-injection-behavior). A interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é passada para o construtor do Auxiliar de Marca de Cache Distribuído. Se nenhuma implementação concreta de `IDistributedCache` for criada em `Startup.ConfigureServices` (*Startup.cs*), o Auxiliar de Marca de Cache Distribuído usará o mesmo provedor na memória para armazenar dados em cache como o [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

## <a name="distributed-cache-tag-helper-attributes"></a>Atributos do auxiliar de marca de cache distribuído

### <a name="attributes-shared-with-the-cache-tag-helper"></a>Atributos compartilhados com o Auxiliar de Marca de Cache

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

O Auxiliar de Marca de Cache Distribuído herda da mesma classe que o Auxiliar de Marca de Cache. Para obter descrições desses atributos, confira [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="name"></a>{1&gt;name&lt;1}

| Tipo de atributo | Exemplo                               |
| -------------- | ------------------------------------- |
| Cadeia de caracteres         | `my-distributed-cache-unique-key-101` |

`name` é obrigatório. O atributo `name` é usado como uma chave para cada instância de cache armazenada. Ao contrário do Auxiliar de Marca de Cache que atribui uma chave de cache a cada instância com base no nome da página do Razor e na localização na página do Razor, o Auxiliar de Marca de Cache Distribuído somente localiza suas chaves no atributo `name`.

Exemplo:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>Implementações de IDistributedCache do Auxiliar de Marca de Cache Distribuído

Há duas implementações de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> internas do ASP.NET Core. Uma é baseada no SQL Server e a outra, no Redis. Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html). Os detalhes dessas implementações podem ser encontrados em <xref:performance/caching/distributed>. Ambas as implementações envolvem configurar de uma instância do `IDistributedCache` em `Startup`.

Não há nenhum atributo de marca especificamente associado ao uso de uma implementação específica de `IDistributedCache`.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
