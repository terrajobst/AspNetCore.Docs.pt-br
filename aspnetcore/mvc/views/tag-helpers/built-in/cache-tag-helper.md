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
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="c90d3-103">Auxiliar de Marca de Cache no ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c90d3-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="c90d3-104">Por [Peter Kellner](http://peterkellner.net) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c90d3-104">By [Peter Kellner](http://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span> 

<span data-ttu-id="c90d3-105">O Auxiliar de Marca de Cache possibilita melhorar o desempenho de seu aplicativo ASP.NET Core armazenando seu conteúdo em cache no provedor de cache interno do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c90d3-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="c90d3-106">Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="c90d3-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="c90d3-107">A seguinte marcação Razor armazena em cache a data atual:</span><span class="sxs-lookup"><span data-stu-id="c90d3-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="c90d3-108">A primeira solicitação para a página que contém o Auxiliar de Marca exibe a data atual.</span><span class="sxs-lookup"><span data-stu-id="c90d3-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="c90d3-109">Solicitações adicionais mostram o valor armazenado em cache até que o cache expire (padrão de 20 minutos) ou até que a data em cache seja removida do cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="c90d3-110">Atributos do Auxiliar de Marca de Cache</span><span class="sxs-lookup"><span data-stu-id="c90d3-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="c90d3-111">habilitado</span><span class="sxs-lookup"><span data-stu-id="c90d3-111">enabled</span></span>

| <span data-ttu-id="c90d3-112">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-112">Attribute Type</span></span>  | <span data-ttu-id="c90d3-113">Exemplos</span><span class="sxs-lookup"><span data-stu-id="c90d3-113">Examples</span></span>        | <span data-ttu-id="c90d3-114">Padrão</span><span class="sxs-lookup"><span data-stu-id="c90d3-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="c90d3-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="c90d3-115">Boolean</span></span>         | <span data-ttu-id="c90d3-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="c90d3-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="c90d3-117">`enabled` determina se o conteúdo envolto pelo Auxiliar de Marca de Cache é armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="c90d3-118">O padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="c90d3-118">The default is `true`.</span></span> <span data-ttu-id="c90d3-119">Se definido como `false`, a saída renderizada **não** é armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="c90d3-120">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="c90d3-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="c90d3-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="c90d3-121">expires-on</span></span>

| <span data-ttu-id="c90d3-122">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-122">Attribute Type</span></span>   | <span data-ttu-id="c90d3-123">Exemplo</span><span class="sxs-lookup"><span data-stu-id="c90d3-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="c90d3-124">`expires-on` define uma data do término absoluta para o item em cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="c90d3-125">O exemplo a seguir armazena em cache o conteúdo do Auxiliar de Marca de Cache até às 17:02 de 29 de janeiro de 2025:</span><span class="sxs-lookup"><span data-stu-id="c90d3-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="c90d3-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="c90d3-126">expires-after</span></span>

| <span data-ttu-id="c90d3-127">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-127">Attribute Type</span></span> | <span data-ttu-id="c90d3-128">Exemplo</span><span class="sxs-lookup"><span data-stu-id="c90d3-128">Example</span></span>                      | <span data-ttu-id="c90d3-129">Padrão</span><span class="sxs-lookup"><span data-stu-id="c90d3-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="c90d3-130">20 minutos</span><span class="sxs-lookup"><span data-stu-id="c90d3-130">20 minutes</span></span> |

<span data-ttu-id="c90d3-131">`expires-after` define o tempo decorrido desde a primeira solicitação para armazenar o conteúdo em cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="c90d3-132">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="c90d3-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="c90d3-133">O Mecanismo de Exibição do Razor define o valor `expires-after` padrão como vinte minutos.</span><span class="sxs-lookup"><span data-stu-id="c90d3-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="c90d3-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="c90d3-134">expires-sliding</span></span>

| <span data-ttu-id="c90d3-135">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-135">Attribute Type</span></span> | <span data-ttu-id="c90d3-136">Exemplo</span><span class="sxs-lookup"><span data-stu-id="c90d3-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="c90d3-137">Define a hora em que uma entrada de cache deverá ser removida se o seu valor não tiver sido acessado.</span><span class="sxs-lookup"><span data-stu-id="c90d3-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="c90d3-138">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="c90d3-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="c90d3-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="c90d3-139">vary-by-header</span></span>

| <span data-ttu-id="c90d3-140">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-140">Attribute Type</span></span> | <span data-ttu-id="c90d3-141">Exemplos</span><span class="sxs-lookup"><span data-stu-id="c90d3-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="c90d3-142">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="c90d3-142">String</span></span>         | <span data-ttu-id="c90d3-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="c90d3-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="c90d3-144">`vary-by-header` aceita uma lista delimitada por vírgulas de valores de cabeçalho que disparam uma atualização do cache quando eles mudam.</span><span class="sxs-lookup"><span data-stu-id="c90d3-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="c90d3-145">O exemplo a seguir monitora o valor do cabeçalho `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="c90d3-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="c90d3-146">O exemplo armazena em cache o conteúdo para cada `User-Agent` diferente apresentado ao servidor Web:</span><span class="sxs-lookup"><span data-stu-id="c90d3-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="c90d3-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="c90d3-147">vary-by-query</span></span>

| <span data-ttu-id="c90d3-148">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-148">Attribute Type</span></span> | <span data-ttu-id="c90d3-149">Exemplos</span><span class="sxs-lookup"><span data-stu-id="c90d3-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="c90d3-150">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="c90d3-150">String</span></span>         | <span data-ttu-id="c90d3-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="c90d3-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="c90d3-152">`vary-by-query` aceita uma lista delimitada por vírgula de <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> em uma cadeia de consulta (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) que dispara uma atualização do cache quando o valor de qualquer chave listada é alterado.</span><span class="sxs-lookup"><span data-stu-id="c90d3-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="c90d3-153">O exemplo a seguir monitora os valores de `Make` e `Model`.</span><span class="sxs-lookup"><span data-stu-id="c90d3-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="c90d3-154">O exemplo armazena em cache o conteúdo para todos os diferentes `Make` e `Model` apresentados ao servidor Web:</span><span class="sxs-lookup"><span data-stu-id="c90d3-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="c90d3-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="c90d3-155">vary-by-route</span></span>

| <span data-ttu-id="c90d3-156">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-156">Attribute Type</span></span> | <span data-ttu-id="c90d3-157">Exemplos</span><span class="sxs-lookup"><span data-stu-id="c90d3-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="c90d3-158">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="c90d3-158">String</span></span>         | <span data-ttu-id="c90d3-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="c90d3-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="c90d3-160">`vary-by-route` aceita uma lista delimitada por vírgulas de nomes de parâmetros de rota que disparam uma atualização do cache quando o valor de parâmetro de dados de rota muda.</span><span class="sxs-lookup"><span data-stu-id="c90d3-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="c90d3-161">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="c90d3-161">Example:</span></span>

<span data-ttu-id="c90d3-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c90d3-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="c90d3-163">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c90d3-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="c90d3-164">vary-by-cookie</span><span class="sxs-lookup"><span data-stu-id="c90d3-164">vary-by-cookie</span></span>

| <span data-ttu-id="c90d3-165">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-165">Attribute Type</span></span> | <span data-ttu-id="c90d3-166">Exemplos</span><span class="sxs-lookup"><span data-stu-id="c90d3-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="c90d3-167">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="c90d3-167">String</span></span>         | <span data-ttu-id="c90d3-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="c90d3-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="c90d3-169">`vary-by-cookie` aceita uma lista delimitada por vírgulas de nomes de cookie que disparam uma atualização do cache quando os valores de cookie mudam.</span><span class="sxs-lookup"><span data-stu-id="c90d3-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="c90d3-170">O exemplo a seguir monitora o cookie associado à identidade do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c90d3-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="c90d3-171">Quando um usuário é autenticado, uma alteração ao cookie de Identidade dispara uma atualização do cache:</span><span class="sxs-lookup"><span data-stu-id="c90d3-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="c90d3-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="c90d3-172">vary-by-user</span></span>

| <span data-ttu-id="c90d3-173">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-173">Attribute Type</span></span>  | <span data-ttu-id="c90d3-174">Exemplos</span><span class="sxs-lookup"><span data-stu-id="c90d3-174">Examples</span></span>        | <span data-ttu-id="c90d3-175">Padrão</span><span class="sxs-lookup"><span data-stu-id="c90d3-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="c90d3-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="c90d3-176">Boolean</span></span>         | <span data-ttu-id="c90d3-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="c90d3-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="c90d3-178">`vary-by-user` especifica se o cache é redefinido ou não quando o usuário conectado (ou a Entidade de Contexto) muda.</span><span class="sxs-lookup"><span data-stu-id="c90d3-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="c90d3-179">O usuário atual também é conhecido como a Entidade do contexto de solicitação e pode ser exibido em um modo de exibição do Razor referenciando `@User.Identity.Name`.</span><span class="sxs-lookup"><span data-stu-id="c90d3-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="c90d3-180">O exemplo a seguir monitora o usuário conectado atual para disparar uma atualização de cache:</span><span class="sxs-lookup"><span data-stu-id="c90d3-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="c90d3-181">Usar esse atributo mantém o conteúdo no cache durante um ciclo de entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="c90d3-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="c90d3-182">Quando o valor é definido como `true`, um ciclo de autenticação invalida o cache para o usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="c90d3-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="c90d3-183">O cache é invalidado porque um novo valor de cookie exclusivo é gerado quando um usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="c90d3-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="c90d3-184">O cache é mantido para o estado anônimo quando nenhum cookie está presente ou quando o cookie expirou.</span><span class="sxs-lookup"><span data-stu-id="c90d3-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="c90d3-185">Se o usuário **não** estiver autenticado, o cache será mantido.</span><span class="sxs-lookup"><span data-stu-id="c90d3-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="c90d3-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="c90d3-186">vary-by</span></span>

| <span data-ttu-id="c90d3-187">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-187">Attribute Type</span></span> | <span data-ttu-id="c90d3-188">Exemplo</span><span class="sxs-lookup"><span data-stu-id="c90d3-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="c90d3-189">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="c90d3-189">String</span></span>         | `@Model` |

<span data-ttu-id="c90d3-190">`vary-by` permite a personalização de quais dados são armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="c90d3-191">Quando o objeto referenciado pelo valor de cadeia de caracteres do atributo é alterado, o conteúdo do Auxiliar de Marca de Cache é atualizado.</span><span class="sxs-lookup"><span data-stu-id="c90d3-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="c90d3-192">Frequentemente, uma concatenação de cadeia de caracteres de valores do modelo é atribuída a este atributo.</span><span class="sxs-lookup"><span data-stu-id="c90d3-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="c90d3-193">Na verdade, isso resulta em um cenário em que uma atualização de qualquer um dos valores concatenados invalida o cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="c90d3-194">O exemplo a seguir supõe que o método do controlador que renderiza a exibição somas o valor inteiro dos dois parâmetros de rota, `myParam1` e `myParam2`, e os retorna a soma como a propriedade de modelo única.</span><span class="sxs-lookup"><span data-stu-id="c90d3-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="c90d3-195">Quando essa soma é alterada, o conteúdo do Auxiliar de Marca de Cache é renderizado e armazenado em cache novamente.</span><span class="sxs-lookup"><span data-stu-id="c90d3-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="c90d3-196">Ação:</span><span class="sxs-lookup"><span data-stu-id="c90d3-196">Action:</span></span>

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

<span data-ttu-id="c90d3-197">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c90d3-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="c90d3-198">priority</span><span class="sxs-lookup"><span data-stu-id="c90d3-198">priority</span></span>

| <span data-ttu-id="c90d3-199">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="c90d3-199">Attribute Type</span></span>      | <span data-ttu-id="c90d3-200">Exemplos</span><span class="sxs-lookup"><span data-stu-id="c90d3-200">Examples</span></span>                               | <span data-ttu-id="c90d3-201">Padrão</span><span class="sxs-lookup"><span data-stu-id="c90d3-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="c90d3-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="c90d3-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="c90d3-203">`priority` fornece diretrizes de remoção do cache para o provedor de cache interno.</span><span class="sxs-lookup"><span data-stu-id="c90d3-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="c90d3-204">O servidor Web remove entradas de cache `Low` primeiro quando está sob demanda de memória.</span><span class="sxs-lookup"><span data-stu-id="c90d3-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="c90d3-205">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="c90d3-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="c90d3-206">O atributo `priority` não assegura um nível específico de retenção de cache.</span><span class="sxs-lookup"><span data-stu-id="c90d3-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="c90d3-207">`CacheItemPriority` é apenas uma sugestão.</span><span class="sxs-lookup"><span data-stu-id="c90d3-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="c90d3-208">Configurar esse atributo como `NeverRemove` não assegura que os itens armazenados em cache sempre sejam retidos.</span><span class="sxs-lookup"><span data-stu-id="c90d3-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="c90d3-209">Veja os tópicos na seção [Recursos Adicionais](#additional-resources) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="c90d3-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="c90d3-210">O Auxiliar de Marca de Cache é dependente do [serviço de cache de memória](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="c90d3-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="c90d3-211">O Auxiliar de Marca de Cache adicionará o serviço se ele não tiver sido adicionado.</span><span class="sxs-lookup"><span data-stu-id="c90d3-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c90d3-212">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c90d3-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
