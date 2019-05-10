---
title: Auxiliar de Marca de Cache no ASP.NET Core MVC
author: pkellner
description: Saiba como usar o Auxiliar de marca de cache.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: fb69584f6e9d4756e175bbd6f3deb1f413b80fc5
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890461"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Auxiliar de Marca de Cache no ASP.NET Core MVC

Por [Peter Kellner](http://peterkellner.net) e [Luke Latham](https://github.com/guardrex) 

O Auxiliar de Marca de Cache possibilita melhorar o desempenho de seu aplicativo ASP.NET Core armazenando seu conteúdo em cache no provedor de cache interno do ASP.NET Core.

Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.

A seguinte marcação Razor armazena em cache a data atual:

```cshtml
<cache>@DateTime.Now</cache>
```

A primeira solicitação para a página que contém o Auxiliar de Marca exibe a data atual. Solicitações adicionais mostram o valor armazenado em cache até que o cache expire (padrão de 20 minutos) ou até que a data em cache seja removida do cache.

## <a name="cache-tag-helper-attributes"></a>Atributos do Auxiliar de Marca de Cache

### <a name="enabled"></a>habilitado

| Tipo de atributo  | Exemplos        | Padrão |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`enabled` determina se o conteúdo envolto pelo Auxiliar de Marca de Cache é armazenado em cache. O padrão é `true`. Se definido como `false`, a saída renderizada **não** é armazenada em cache.

Exemplo:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| Tipo de atributo   | Exemplo                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` define uma data do término absoluta para o item em cache.

O exemplo a seguir armazena em cache o conteúdo do Auxiliar de Marca de Cache até às 17:02 de 29 de janeiro de 2025:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| Tipo de atributo | Exemplo                      | Padrão    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 minutos |

`expires-after` define o tempo decorrido desde a primeira solicitação para armazenar o conteúdo em cache.

Exemplo:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

O Mecanismo de Exibição do Razor define o valor `expires-after` padrão como vinte minutos.

### <a name="expires-sliding"></a>expires-sliding

| Tipo de atributo | Exemplo                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Define a hora em que uma entrada de cache deverá ser removida se o seu valor não tiver sido acessado.

Exemplo:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>vary-by-header

| Tipo de atributo | Exemplos                                    |
| -------------- | ------------------------------------------- |
| Cadeia de Caracteres         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` aceita uma lista delimitada por vírgulas de valores de cabeçalho que disparam uma atualização do cache quando eles mudam.

O exemplo a seguir monitora o valor do cabeçalho `User-Agent`. O exemplo armazena em cache o conteúdo para cada `User-Agent` diferente apresentado ao servidor Web:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>vary-by-query

| Tipo de atributo | Exemplos             |
| -------------- | -------------------- |
| Cadeia de Caracteres         | `Make`, `Make,Model` |

`vary-by-query` aceita uma lista delimitada por vírgula de <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> em uma cadeia de consulta (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) que dispara uma atualização do cache quando o valor de qualquer chave listada é alterado.

O exemplo a seguir monitora os valores de `Make` e `Model`. O exemplo armazena em cache o conteúdo para todos os diferentes `Make` e `Model` apresentados ao servidor Web:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>vary-by-route

| Tipo de atributo | Exemplos             |
| -------------- | -------------------- |
| Cadeia de Caracteres         | `Make`, `Make,Model` |

`vary-by-route` aceita uma lista delimitada por vírgulas de nomes de parâmetros de rota que disparam uma atualização do cache quando o valor de parâmetro de dados de rota muda.

Exemplo:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index.cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a>vary-by-cookie

| Tipo de atributo | Exemplos                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| Cadeia de Caracteres         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` aceita uma lista delimitada por vírgulas de nomes de cookie que disparam uma atualização do cache quando os valores de cookie mudam.

O exemplo a seguir monitora o cookie associado à identidade do ASP.NET Core. Quando um usuário é autenticado, uma alteração ao cookie de Identidade dispara uma atualização do cache:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>vary-by-user

| Tipo de atributo  | Exemplos        | Padrão |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`vary-by-user` especifica se o cache é redefinido ou não quando o usuário conectado (ou a Entidade de Contexto) muda. O usuário atual também é conhecido como a Entidade do contexto de solicitação e pode ser exibido em um modo de exibição do Razor referenciando `@User.Identity.Name`.

O exemplo a seguir monitora o usuário conectado atual para disparar uma atualização de cache:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Usar esse atributo mantém o conteúdo no cache durante um ciclo de entrada e saída. Quando o valor é definido como `true`, um ciclo de autenticação invalida o cache para o usuário autenticado. O cache é invalidado porque um novo valor de cookie exclusivo é gerado quando um usuário é autenticado. O cache é mantido para o estado anônimo quando nenhum cookie está presente ou quando o cookie expirou. Se o usuário **não** estiver autenticado, o cache será mantido.

### <a name="vary-by"></a>vary-by

| Tipo de atributo | Exemplo  |
| -------------- | -------- |
| Cadeia de Caracteres         | `@Model` |

`vary-by` permite a personalização de quais dados são armazenados em cache. Quando o objeto referenciado pelo valor de cadeia de caracteres do atributo é alterado, o conteúdo do Auxiliar de Marca de Cache é atualizado. Frequentemente, uma concatenação de cadeia de caracteres de valores do modelo é atribuída a este atributo. Na verdade, isso resulta em um cenário em que uma atualização de qualquer um dos valores concatenados invalida o cache.

O exemplo a seguir supõe que o método do controlador que renderiza a exibição somas o valor inteiro dos dois parâmetros de rota, `myParam1` e `myParam2`, e os retorna a soma como a propriedade de modelo única. Quando essa soma é alterada, o conteúdo do Auxiliar de Marca de Cache é renderizado e armazenado em cache novamente.  

Ação:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index.cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| Tipo de atributo      | Exemplos                               | Padrão  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` fornece diretrizes de remoção do cache para o provedor de cache interno. O servidor Web remove entradas de cache `Low` primeiro quando está sob demanda de memória.

Exemplo:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

O atributo `priority` não assegura um nível específico de retenção de cache. `CacheItemPriority` é apenas uma sugestão. Configurar esse atributo como `NeverRemove` não assegura que os itens armazenados em cache sempre sejam retidos. Veja os tópicos na seção [Recursos Adicionais](#additional-resources) para obter mais informações.

O Auxiliar de Marca de Cache é dependente do [serviço de cache de memória](xref:performance/caching/memory). O Auxiliar de Marca de Cache adicionará o serviço se ele não tiver sido adicionado.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
