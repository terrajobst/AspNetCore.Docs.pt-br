---
title: Middleware de cache de resposta em ASP.NET Core
author: guardrex
description: Saiba como configurar e usar o Middleware de cache de resposta no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: performance/caching/middleware
ms.openlocfilehash: 6371f42b100f70c6042064a6372c7b9e41fd5c73
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914986"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="f08ff-103">Middleware de cache de resposta em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f08ff-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="f08ff-104">De [Luke Latham](https://github.com/guardrex) e [John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="f08ff-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="f08ff-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f08ff-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f08ff-106">Este artigo explica como configurar o middleware de cache de resposta em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f08ff-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="f08ff-107">O middleware determina quando as respostas são armazenáveis em cache, armazena respostas e serve respostas do cache.</span><span class="sxs-lookup"><span data-stu-id="f08ff-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="f08ff-108">Para obter uma introdução ao cache HTTP e ao atributo [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) , consulte [cache de resposta](xref:performance/caching/response).</span><span class="sxs-lookup"><span data-stu-id="f08ff-108">For an introduction to HTTP caching and the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="configuration"></a><span data-ttu-id="f08ff-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="f08ff-109">Configuration</span></span>

<span data-ttu-id="f08ff-110">Use o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .</span><span class="sxs-lookup"><span data-stu-id="f08ff-110">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="f08ff-111">No `Startup.ConfigureServices`, adicione o middleware de cache de resposta à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="f08ff-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

::: moniker-end

<span data-ttu-id="f08ff-112">Configure o aplicativo para usar o middleware com o método <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> de extensão, que adiciona o middleware ao pipeline de processamento de solicitação no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f08ff-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

::: moniker-end

<span data-ttu-id="f08ff-113">O aplicativo de exemplo adiciona cabeçalhos ao cache de controle em solicitações subsequentes:</span><span class="sxs-lookup"><span data-stu-id="f08ff-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="f08ff-114">[Controle de cache](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Armazena em cache as respostas armazenáveis em cache por até 10 segundos.</span><span class="sxs-lookup"><span data-stu-id="f08ff-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="f08ff-115">[Variar](https://tools.ietf.org/html/rfc7231#section-7.1.4) Configura o middleware para servir uma resposta armazenada em cache somente se o [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) cabeçalho das solicitações subsequentes corresponder à da solicitação original. &ndash;</span><span class="sxs-lookup"><span data-stu-id="f08ff-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

::: moniker-end

<span data-ttu-id="f08ff-116">O middleware de cache de resposta só armazena em cache as respostas do servidor que resultam em um código de status 200 (OK).</span><span class="sxs-lookup"><span data-stu-id="f08ff-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="f08ff-117">Quaisquer outras respostas, incluindo [páginas de erro](xref:fundamentals/error-handling), são ignoradas pelo middleware.</span><span class="sxs-lookup"><span data-stu-id="f08ff-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="f08ff-118">As respostas que contêm o conteúdo para clientes autenticados devem ser marcadas como não armazenáveis em cache para impedir que o middleware armazene e atenda a essas respostas.</span><span class="sxs-lookup"><span data-stu-id="f08ff-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="f08ff-119">Consulte [condições para o cache](#conditions-for-caching) para obter detalhes sobre como o middleware determina se uma resposta é armazenável em cache.</span><span class="sxs-lookup"><span data-stu-id="f08ff-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="f08ff-120">Opções</span><span class="sxs-lookup"><span data-stu-id="f08ff-120">Options</span></span>

<span data-ttu-id="f08ff-121">As opções de cache de resposta são mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f08ff-121">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="f08ff-122">Opção</span><span class="sxs-lookup"><span data-stu-id="f08ff-122">Option</span></span> | <span data-ttu-id="f08ff-123">Descrição</span><span class="sxs-lookup"><span data-stu-id="f08ff-123">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="f08ff-124">O maior tamanho em cache para o corpo da resposta em bytes.</span><span class="sxs-lookup"><span data-stu-id="f08ff-124">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="f08ff-125">O valor padrão é `64 * 1024 * 1024` (64 MB).</span><span class="sxs-lookup"><span data-stu-id="f08ff-125">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="f08ff-126">O limite de tamanho do middleware do cache de resposta em bytes.</span><span class="sxs-lookup"><span data-stu-id="f08ff-126">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="f08ff-127">O valor padrão é `100 * 1024 * 1024` (100 MB).</span><span class="sxs-lookup"><span data-stu-id="f08ff-127">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="f08ff-128">Determina se as respostas são armazenadas em cache em caminhos que diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="f08ff-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="f08ff-129">O valor padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="f08ff-129">The default value is `false`.</span></span> |

<span data-ttu-id="f08ff-130">O exemplo a seguir configura o middleware para:</span><span class="sxs-lookup"><span data-stu-id="f08ff-130">The following example configures the middleware to:</span></span>

* <span data-ttu-id="f08ff-131">Armazenar em cache as respostas com um tamanho de corpo menor ou igual a 1.024 bytes.</span><span class="sxs-lookup"><span data-stu-id="f08ff-131">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="f08ff-132">Armazene as respostas por caminhos que diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="f08ff-132">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="f08ff-133">Por exemplo, `/page1` e `/Page1` são armazenados separadamente.</span><span class="sxs-lookup"><span data-stu-id="f08ff-133">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="f08ff-134">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="f08ff-134">VaryByQueryKeys</span></span>

<span data-ttu-id="f08ff-135">Ao usar controladores de API da Web ou MVC ou modelos de página Razor Pages, o atributo [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) especifica os parâmetros necessários para definir os cabeçalhos apropriados para o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="f08ff-135">When using MVC / web API controllers or Razor Pages page models, the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="f08ff-136">O único parâmetro do `[ResponseCache]` atributo que exige estritamente o middleware é <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, que não corresponde a um cabeçalho HTTP real.</span><span class="sxs-lookup"><span data-stu-id="f08ff-136">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="f08ff-137">Para obter mais informações, consulte <xref:performance/caching/response#responsecache-attribute>.</span><span class="sxs-lookup"><span data-stu-id="f08ff-137">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="f08ff-138">Quando não estiver usando `[ResponseCache]` o atributo, o cache de resposta poderá `VaryByQueryKeys`ser variado com.</span><span class="sxs-lookup"><span data-stu-id="f08ff-138">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="f08ff-139">Use o <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> diretamente do [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span><span class="sxs-lookup"><span data-stu-id="f08ff-139">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="f08ff-140">O uso de um único valor `*` igual `VaryByQueryKeys` a in varia o cache por todos os parâmetros de consulta de solicitação.</span><span class="sxs-lookup"><span data-stu-id="f08ff-140">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="f08ff-141">Cabeçalhos HTTP usados pelo middleware de cache de resposta</span><span class="sxs-lookup"><span data-stu-id="f08ff-141">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="f08ff-142">A tabela a seguir fornece informações sobre cabeçalhos HTTP que afetam o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="f08ff-142">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="f08ff-143">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="f08ff-143">Header</span></span> | <span data-ttu-id="f08ff-144">Detalhes</span><span class="sxs-lookup"><span data-stu-id="f08ff-144">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="f08ff-145">A resposta não será armazenada em cache se o cabeçalho existir.</span><span class="sxs-lookup"><span data-stu-id="f08ff-145">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="f08ff-146">O middleware só considera respostas de cache marcadas com `public` a diretiva de cache.</span><span class="sxs-lookup"><span data-stu-id="f08ff-146">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="f08ff-147">Controlar o cache com os seguintes parâmetros:</span><span class="sxs-lookup"><span data-stu-id="f08ff-147">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="f08ff-148">idade máxima</span><span class="sxs-lookup"><span data-stu-id="f08ff-148">max-age</span></span></li><li><span data-ttu-id="f08ff-149">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="f08ff-149">max-stale&#8224;</span></span></li><li><span data-ttu-id="f08ff-150">mín. de atualização</span><span class="sxs-lookup"><span data-stu-id="f08ff-150">min-fresh</span></span></li><li><span data-ttu-id="f08ff-151">deve ser revalidado</span><span class="sxs-lookup"><span data-stu-id="f08ff-151">must-revalidate</span></span></li><li><span data-ttu-id="f08ff-152">no-cache</span><span class="sxs-lookup"><span data-stu-id="f08ff-152">no-cache</span></span></li><li><span data-ttu-id="f08ff-153">sem armazenamento</span><span class="sxs-lookup"><span data-stu-id="f08ff-153">no-store</span></span></li><li><span data-ttu-id="f08ff-154">somente-se-em-cache</span><span class="sxs-lookup"><span data-stu-id="f08ff-154">only-if-cached</span></span></li><li><span data-ttu-id="f08ff-155">particulares</span><span class="sxs-lookup"><span data-stu-id="f08ff-155">private</span></span></li><li><span data-ttu-id="f08ff-156">públicos</span><span class="sxs-lookup"><span data-stu-id="f08ff-156">public</span></span></li><li><span data-ttu-id="f08ff-157">s-maxage</span><span class="sxs-lookup"><span data-stu-id="f08ff-157">s-maxage</span></span></li><li><span data-ttu-id="f08ff-158">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="f08ff-158">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="f08ff-159">&#8224;Se nenhum limite for especificado para `max-stale`, o middleware não executará nenhuma ação.</span><span class="sxs-lookup"><span data-stu-id="f08ff-159">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="f08ff-160">&#8225;`proxy-revalidate`tem o mesmo efeito que `must-revalidate`.</span><span class="sxs-lookup"><span data-stu-id="f08ff-160">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="f08ff-161">Para obter mais informações, [consulte RFC 7231: Solicitar diretivas](https://tools.ietf.org/html/rfc7234#section-5.2.1)de controle de cache.</span><span class="sxs-lookup"><span data-stu-id="f08ff-161">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="f08ff-162">Um `Pragma: no-cache` cabeçalho na solicitação produz o mesmo efeito que `Cache-Control: no-cache`.</span><span class="sxs-lookup"><span data-stu-id="f08ff-162">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="f08ff-163">Esse cabeçalho é substituído pelas diretivas relevantes no `Cache-Control` cabeçalho, se presente.</span><span class="sxs-lookup"><span data-stu-id="f08ff-163">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="f08ff-164">Considerado para compatibilidade com versões anteriores com HTTP/1.0.</span><span class="sxs-lookup"><span data-stu-id="f08ff-164">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="f08ff-165">A resposta não será armazenada em cache se o cabeçalho existir.</span><span class="sxs-lookup"><span data-stu-id="f08ff-165">The response isn't cached if the header exists.</span></span> <span data-ttu-id="f08ff-166">Qualquer middleware no pipeline de processamento de solicitação que define um ou mais cookies impede que o middleware de cache de resposta em cache a resposta (por exemplo, o [provedor TempData baseado em cookie](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="f08ff-166">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="f08ff-167">O `Vary` cabeçalho é usado para variar a resposta armazenada em cache por outro cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="f08ff-167">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="f08ff-168">Por exemplo, armazene respostas em cache por meio `Vary: Accept-Encoding` da codificação, incluindo o cabeçalho, que armazena em `Accept-Encoding: gzip` cache `Accept-Encoding: text/plain` as respostas para solicitações com cabeçalhos e separadamente.</span><span class="sxs-lookup"><span data-stu-id="f08ff-168">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="f08ff-169">Uma resposta com um valor de cabeçalho `*` de nunca é armazenada.</span><span class="sxs-lookup"><span data-stu-id="f08ff-169">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="f08ff-170">Uma resposta considerada obsoleta por esse cabeçalho não é armazenada ou recuperada, a `Cache-Control` menos que seja substituída por outros cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="f08ff-170">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="f08ff-171">A resposta completa será servida do cache se o valor `*` não for `ETag` e a da resposta não corresponder a nenhum dos valores fornecidos.</span><span class="sxs-lookup"><span data-stu-id="f08ff-171">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="f08ff-172">Caso contrário, uma resposta 304 (não modificada) é servida.</span><span class="sxs-lookup"><span data-stu-id="f08ff-172">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="f08ff-173">Se o `If-None-Match` cabeçalho não estiver presente, uma resposta completa será servida do cache se a data de resposta armazenada em cache for mais recente do que o valor fornecido.</span><span class="sxs-lookup"><span data-stu-id="f08ff-173">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="f08ff-174">Caso contrário, uma resposta *304-não modificada* é servida.</span><span class="sxs-lookup"><span data-stu-id="f08ff-174">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="f08ff-175">Ao servir do cache, o `Date` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original.</span><span class="sxs-lookup"><span data-stu-id="f08ff-175">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="f08ff-176">Ao servir do cache, o `Content-Length` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original.</span><span class="sxs-lookup"><span data-stu-id="f08ff-176">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="f08ff-177">O `Age` cabeçalho enviado na resposta original é ignorado.</span><span class="sxs-lookup"><span data-stu-id="f08ff-177">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="f08ff-178">O middleware computa um novo valor ao fornecer uma resposta armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="f08ff-178">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="f08ff-179">Aspectos de cache de solicitação de cache – diretivas de controle</span><span class="sxs-lookup"><span data-stu-id="f08ff-179">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="f08ff-180">O middleware respeita as regras da especificação de [cache HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="f08ff-180">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="f08ff-181">As regras exigem um cache para honrar um `Cache-Control` cabeçalho válido enviado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="f08ff-181">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="f08ff-182">Na especificação, um cliente pode fazer solicitações com um `no-cache` valor de cabeçalho e forçar o servidor a gerar uma nova resposta para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="f08ff-182">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="f08ff-183">Atualmente, não há nenhum controle de desenvolvedor sobre esse comportamento de cache ao usar o middleware porque o middleware segue a especificação de cache oficial.</span><span class="sxs-lookup"><span data-stu-id="f08ff-183">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="f08ff-184">Para obter mais controle sobre o comportamento de caching, explore outros recursos de cache do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f08ff-184">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="f08ff-185">Confira os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="f08ff-185">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="f08ff-186">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="f08ff-186">Troubleshooting</span></span>

<span data-ttu-id="f08ff-187">Se o comportamento de caching não for o esperado, confirme se as respostas são armazenáveis em cache e capazes de serem servidas do cache.</span><span class="sxs-lookup"><span data-stu-id="f08ff-187">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="f08ff-188">Examine os cabeçalhos de entrada da solicitação e os cabeçalhos de saída da resposta.</span><span class="sxs-lookup"><span data-stu-id="f08ff-188">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="f08ff-189">Habilite o [log](xref:fundamentals/logging/index) para ajudar com a depuração.</span><span class="sxs-lookup"><span data-stu-id="f08ff-189">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="f08ff-190">Ao testar e solucionar problemas de comportamento de cache, um navegador pode definir cabeçalhos de solicitação que afetam o armazenamento em cache de maneiras indesejáveis.</span><span class="sxs-lookup"><span data-stu-id="f08ff-190">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="f08ff-191">Por exemplo, um navegador pode definir o `Cache-Control` cabeçalho como `no-cache` ou `max-age=0` ao atualizar uma página.</span><span class="sxs-lookup"><span data-stu-id="f08ff-191">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="f08ff-192">As seguintes ferramentas podem definir explicitamente os cabeçalhos de solicitação e são preferenciais para o cache de teste:</span><span class="sxs-lookup"><span data-stu-id="f08ff-192">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="f08ff-193">Fiddler</span><span class="sxs-lookup"><span data-stu-id="f08ff-193">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="f08ff-194">Postman</span><span class="sxs-lookup"><span data-stu-id="f08ff-194">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="f08ff-195">Condições para cache</span><span class="sxs-lookup"><span data-stu-id="f08ff-195">Conditions for caching</span></span>

* <span data-ttu-id="f08ff-196">A solicitação deve resultar em uma resposta do servidor com um código de status 200 (OK).</span><span class="sxs-lookup"><span data-stu-id="f08ff-196">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="f08ff-197">O método de solicitação deve ser GET ou HEAD.</span><span class="sxs-lookup"><span data-stu-id="f08ff-197">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="f08ff-198">No `Startup.Configure`, o middleware de cache de resposta deve ser colocado antes do middleware que requer Caching.</span><span class="sxs-lookup"><span data-stu-id="f08ff-198">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="f08ff-199">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="f08ff-199">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="f08ff-200">O `Authorization` cabeçalho não deve estar presente.</span><span class="sxs-lookup"><span data-stu-id="f08ff-200">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="f08ff-201">`Cache-Control`os parâmetros de cabeçalho devem ser válidos e a resposta deve ser `public` marcada e não `private`marcada.</span><span class="sxs-lookup"><span data-stu-id="f08ff-201">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="f08ff-202">O `Pragma: no-cache` cabeçalho não deve estar presente se o `Cache-Control` cabeçalho não estiver presente, pois `Cache-Control` o cabeçalho substitui `Pragma` o cabeçalho quando estiver presente.</span><span class="sxs-lookup"><span data-stu-id="f08ff-202">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="f08ff-203">O `Set-Cookie` cabeçalho não deve estar presente.</span><span class="sxs-lookup"><span data-stu-id="f08ff-203">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="f08ff-204">`Vary`os parâmetros de cabeçalho devem ser válidos e não `*`iguais a.</span><span class="sxs-lookup"><span data-stu-id="f08ff-204">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="f08ff-205">O `Content-Length` valor do cabeçalho (se definido) deve corresponder ao tamanho do corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="f08ff-205">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="f08ff-206">O <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> não é usado.</span><span class="sxs-lookup"><span data-stu-id="f08ff-206">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="f08ff-207">A resposta não deve estar obsoleta conforme especificado pelo `Expires` cabeçalho e pelas diretivas de `s-maxage` `max-age` cache e.</span><span class="sxs-lookup"><span data-stu-id="f08ff-207">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="f08ff-208">O buffer de resposta deve ser bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="f08ff-208">Response buffering must be successful.</span></span> <span data-ttu-id="f08ff-209">O tamanho da resposta deve ser menor do que o configurado ou o <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>padrão.</span><span class="sxs-lookup"><span data-stu-id="f08ff-209">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="f08ff-210">O tamanho do corpo da resposta deve ser menor do que o configurado ou <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>o padrão.</span><span class="sxs-lookup"><span data-stu-id="f08ff-210">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="f08ff-211">A resposta deve ser armazenada em cache de acordo com as especificações [RFC 7234](https://tools.ietf.org/html/rfc7234) .</span><span class="sxs-lookup"><span data-stu-id="f08ff-211">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="f08ff-212">Por exemplo, a `no-store` diretiva não deve existir em campos de cabeçalho de solicitação ou de resposta.</span><span class="sxs-lookup"><span data-stu-id="f08ff-212">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="f08ff-213">Consulte *a seção 3: Armazenando respostas em caches* do [RFC 7234](https://tools.ietf.org/html/rfc7234) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="f08ff-213">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="f08ff-214">O sistema antifalsificação para gerar tokens seguros para impedir ataques de solicitação intersite forjada (CSRF) define `Cache-Control` os `Pragma` cabeçalhos e `no-cache` para que as respostas não sejam armazenadas em cache.</span><span class="sxs-lookup"><span data-stu-id="f08ff-214">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="f08ff-215">Para obter informações sobre como desabilitar tokens antifalsificação para elementos de formulário HTML <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>, consulte.</span><span class="sxs-lookup"><span data-stu-id="f08ff-215">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f08ff-216">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f08ff-216">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
