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
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="ccb2a-103">Cache de resposta no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ccb2a-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="ccb2a-104">Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ccb2a-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

> [!NOTE]
> <span data-ttu-id="ccb2a-105">Cache de resposta nas páginas do Razor está disponível no ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-105">Response caching in Razor Pages is available in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="ccb2a-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ccb2a-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ccb2a-107">O cache das respostas reduz o número de solicitações de que um cliente ou proxy faz a um servidor web.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-107">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="ccb2a-108">Cache de resposta também reduz a quantidade de trabalho do servidor web executa para gerar uma resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-108">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="ccb2a-109">Cache de resposta é controlado por cabeçalhos que especificam como deseja cliente, o proxy e middleware para respostas em cache.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-109">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="ccb2a-110">O [do atributo ResponseCache](#responsecache-attribute) participa na configuração de cabeçalhos, quais clientes podem honrar ao armazenar em cache as respostas do cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-110">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers, which clients may honor when caching responses.</span></span> <span data-ttu-id="ccb2a-111">[Middleware de cache de resposta](xref:performance/caching/middleware) pode ser usada para respostas em cache no servidor.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-111">[Response Caching Middleware](xref:performance/caching/middleware) can be used to cache responses on the server.</span></span> <span data-ttu-id="ccb2a-112">O middleware pode usar `ResponseCache` propriedades para influenciar o comportamento de cache do lado do servidor do atributo.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-112">The middleware can use `ResponseCache` attribute properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="ccb2a-113">Cache de resposta baseado em HTTP</span><span class="sxs-lookup"><span data-stu-id="ccb2a-113">HTTP-based response caching</span></span>

<span data-ttu-id="ccb2a-114">O [especificação de cache do HTTP 1.1](https://tools.ietf.org/html/rfc7234) descreve como caches de Internet devem se comportar.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-114">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="ccb2a-115">É o cabeçalho HTTP principal usado para armazenar em cache [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), que é usada para especificar o cache *diretivas*.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-115">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="ccb2a-116">As diretivas de controlam o comportamento de cache conforme as solicitações cheguem de clientes para servidores e respostas passarão de servidores de volta aos clientes.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-116">The directives control caching behavior as requests make their way from clients to servers and as reponses make their way from servers back to clients.</span></span> <span data-ttu-id="ccb2a-117">Solicitações e respostas movem através de servidores proxy e servidores proxy devem também estar em conformidade com a especificação de cache do HTTP 1.1.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-117">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="ccb2a-118">Common `Cache-Control` diretivas são mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-118">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="ccb2a-119">Diretiva</span><span class="sxs-lookup"><span data-stu-id="ccb2a-119">Directive</span></span>                                                       | <span data-ttu-id="ccb2a-120">Ação</span><span class="sxs-lookup"><span data-stu-id="ccb2a-120">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="ccb2a-121">public</span><span class="sxs-lookup"><span data-stu-id="ccb2a-121">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="ccb2a-122">Um cache pode armazenar a resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-122">A cache may store the response.</span></span> |
| [<span data-ttu-id="ccb2a-123">private</span><span class="sxs-lookup"><span data-stu-id="ccb2a-123">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="ccb2a-124">A resposta não deve ser armazenada por um cache compartilhado.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-124">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="ccb2a-125">Um cache privado pode armazenar e reutilizar a resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-125">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="ccb2a-126">max-age</span><span class="sxs-lookup"><span data-stu-id="ccb2a-126">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="ccb2a-127">O cliente não aceitará uma resposta cuja idade é maior que o número especificado de segundos.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-127">The client won't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="ccb2a-128">Exemplos: `max-age=60` (60 segundos), `max-age=2592000` (1 mês)</span><span class="sxs-lookup"><span data-stu-id="ccb2a-128">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="ccb2a-129">no-cache</span><span class="sxs-lookup"><span data-stu-id="ccb2a-129">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="ccb2a-130">**Em solicitações**: Um cache não deve usar uma resposta armazenada para atender à solicitação.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-130">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="ccb2a-131">Observação: O servidor de origem gera novamente a resposta para o cliente e o middleware atualiza a resposta armazenada em seu cache.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-131">Note: The origin server re-generates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="ccb2a-132">**Nas respostas**: A resposta não deve ser usada para uma solicitação subsequente sem validação no servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-132">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="ccb2a-133">no-store</span><span class="sxs-lookup"><span data-stu-id="ccb2a-133">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="ccb2a-134">**Em solicitações**: Um cache não deve armazenar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-134">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="ccb2a-135">**Nas respostas**: Um cache não deve armazenar qualquer parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-135">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="ccb2a-136">Outros cabeçalhos de cache que desempenham uma função em cache são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-136">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="ccb2a-137">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="ccb2a-137">Header</span></span>                                                     | <span data-ttu-id="ccb2a-138">Função</span><span class="sxs-lookup"><span data-stu-id="ccb2a-138">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="ccb2a-139">Idade</span><span class="sxs-lookup"><span data-stu-id="ccb2a-139">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="ccb2a-140">Uma estimativa da quantidade de tempo em segundos desde a resposta foi gerada ou validada com êxito no servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-140">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="ccb2a-141">Expira</span><span class="sxs-lookup"><span data-stu-id="ccb2a-141">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="ccb2a-142">A data/hora após o qual a resposta é considerada obsoleta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-142">The date/time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="ccb2a-143">Pragma</span><span class="sxs-lookup"><span data-stu-id="ccb2a-143">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="ccb2a-144">Existe para com versões anteriores a compatibilidade com o HTTP/1.0 armazena em cache para a configuração `no-cache` comportamento.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-144">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="ccb2a-145">Se o `Cache-Control` cabeçalho estiver presente, o `Pragma` cabeçalho será ignorado.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-145">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="ccb2a-146">Variar</span><span class="sxs-lookup"><span data-stu-id="ccb2a-146">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="ccb2a-147">Especifica que uma resposta em cache não deve ser enviada, a menos que todos os do `Vary` correspondem de campos de cabeçalho na solicitação original da resposta em cache e a nova solicitação.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-147">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="ccb2a-148">As diretivas de controle de Cache de solicitação de aspectos de cache baseada em HTTP</span><span class="sxs-lookup"><span data-stu-id="ccb2a-148">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="ccb2a-149">O [especificação de cache do HTTP 1.1 para o cabeçalho Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requer um cache para honrar válido `Cache-Control` cabeçalho enviado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-149">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="ccb2a-150">Um cliente pode fazer solicitações com um `no-cache` força o servidor gere uma nova resposta para cada solicitação e o valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-150">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="ccb2a-151">Respeitando sempre cliente `Cache-Control` cabeçalhos de solicitação faz sentido se você considerar o objetivo do cache de HTTP.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-151">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="ccb2a-152">Sob a especificação oficial, cache destina-se para reduzir a sobrecarga de rede e latência de satisfazer as solicitações em uma rede de clientes, proxies e servidores.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-152">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="ccb2a-153">Ele não é necessariamente uma maneira de controlar a carga em um servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-153">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="ccb2a-154">Não é possível controlar de desenvolvedor sobre o comportamento de cache ao usar o [Middleware de cache de resposta](xref:performance/caching/middleware) porque o middleware adere à especificação de cache oficial.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-154">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="ccb2a-155">[Planejado aprimoramentos para o middleware](https://github.com/aspnet/AspNetCore/issues/2612) são uma oportunidade para configurar o middleware para ignorar uma solicitação `Cache-Control` cabeçalho ao decidir servir uma resposta em cache.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-155">[Planned enhancements to the middleware](https://github.com/aspnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="ccb2a-156">Aprimoramentos planejados oferecem uma oportunidade de melhor carga do servidor de controle.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-156">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="ccb2a-157">Outra tecnologia de armazenamento em cache no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ccb2a-157">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="ccb2a-158">Cache na memória</span><span class="sxs-lookup"><span data-stu-id="ccb2a-158">In-memory caching</span></span>

<span data-ttu-id="ccb2a-159">Cache na memória usa a memória do servidor para armazenar dados armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-159">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="ccb2a-160">Esse tipo de cache é adequado para um único servidor ou vários servidores usando *sessões adesivas*.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-160">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="ccb2a-161">Sessões adesivas significa que as solicitações de um cliente sejam sempre roteadas para o mesmo servidor para processamento.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-161">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="ccb2a-162">Para obter mais informações, consulte [armazenar em Cache na memória](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="ccb2a-162">For more information, see [Cache in-memory](xref:performance/caching/memory).</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="ccb2a-163">Cache distribuído</span><span class="sxs-lookup"><span data-stu-id="ccb2a-163">Distributed Cache</span></span>

<span data-ttu-id="ccb2a-164">Use um cache distribuído para armazenar dados na memória quando o aplicativo é hospedado em um farm de servidor ou de nuvem.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-164">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="ccb2a-165">O cache é compartilhado entre os servidores que processam solicitações.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-165">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="ccb2a-166">Um cliente pode enviar uma solicitação que é tratada por qualquer servidor no grupo se os dados armazenados em cache para o cliente estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-166">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="ccb2a-167">ASP.NET Core oferece o SQL Server e os caches distribuído do Redis.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-167">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="ccb2a-168">Para obter mais informações, consulte <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-168">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="ccb2a-169">Auxiliar de marca de cache</span><span class="sxs-lookup"><span data-stu-id="ccb2a-169">Cache Tag Helper</span></span>

<span data-ttu-id="ccb2a-170">Você pode armazenar em cache o conteúdo de uma exibição MVC ou a página do Razor com o auxiliar de marca de Cache.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-170">You can cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="ccb2a-171">O auxiliar de marca de Cache usa o cache na memória para armazenar dados.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-171">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="ccb2a-172">Para obter mais informações, consulte [auxiliar de marca de Cache no ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ccb2a-172">For more information, see [Cache Tag Helper in ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="ccb2a-173">Auxiliar de Marca de Cache Distribuído</span><span class="sxs-lookup"><span data-stu-id="ccb2a-173">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="ccb2a-174">Você pode armazenar em cache o conteúdo de uma exibição MVC ou a página do Razor em nuvem distribuído ou cenários de web farm com o auxiliar de marca de Cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-174">You can cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="ccb2a-175">O auxiliar de marca de Cache distribuído usa o SQL Server ou do Redis para armazenar dados.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-175">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="ccb2a-176">Para obter mais informações, consulte [auxiliar de marca de Cache distribuído](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ccb2a-176">For more information, see [Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="ccb2a-177">Atributo ResponseCache</span><span class="sxs-lookup"><span data-stu-id="ccb2a-177">ResponseCache attribute</span></span>

<span data-ttu-id="ccb2a-178">O [ResponseCacheAttribute](/dotnet/api/Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Especifica os parâmetros necessários para a configuração de cabeçalhos apropriados no cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-178">The [ResponseCacheAttribute](/dotnet/api/Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="ccb2a-179">Desabilite o cache para o conteúdo que contém informações para clientes autenticados.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-179">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="ccb2a-180">Armazenamento em cache deve ser habilitado apenas para conteúdo que não são alteradas com base na identidade do usuário ou se um usuário está conectado.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-180">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="ccb2a-181">[VaryByQueryKeys](/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute.varybyquerykeys) a resposta armazenada de varia de acordo com os valores de determinada lista de chaves de consulta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-181">[VaryByQueryKeys](/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute.varybyquerykeys) varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="ccb2a-182">Quando um valor único de `*` é fornecido, o middleware varia as respostas de todos os parâmetros de cadeia de caracteres de consulta de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-182">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span> <span data-ttu-id="ccb2a-183">`VaryByQueryKeys` exige o ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-183">`VaryByQueryKeys` requires ASP.NET Core 1.1 or later.</span></span>

<span data-ttu-id="ccb2a-184">[Middleware de cache de resposta](xref:performance/caching/middleware) deve ser habilitado para definir o `VaryByQueryKeys` propriedade; caso contrário, uma exceção de tempo de execução é gerada.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-184">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the `VaryByQueryKeys` property; otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="ccb2a-185">Não há um cabeçalho HTTP correspondente para o `VaryByQueryKeys` propriedade.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-185">There isn't a corresponding HTTP header for the `VaryByQueryKeys` property.</span></span> <span data-ttu-id="ccb2a-186">A propriedade é um recurso HTTP tratado pelo Middleware de cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-186">The property is an HTTP feature handled by the Response Caching Middleware.</span></span> <span data-ttu-id="ccb2a-187">Para o middleware servir uma resposta em cache, a cadeia de caracteres de consulta e o valor de cadeia de caracteres de consulta devem corresponder com uma solicitação anterior.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-187">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="ccb2a-188">Por exemplo, considere a sequência de solicitações e os resultados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-188">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="ccb2a-189">Solicitação</span><span class="sxs-lookup"><span data-stu-id="ccb2a-189">Request</span></span>                          | <span data-ttu-id="ccb2a-190">Resultado</span><span class="sxs-lookup"><span data-stu-id="ccb2a-190">Result</span></span>                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | <span data-ttu-id="ccb2a-191">Retornado do servidor</span><span class="sxs-lookup"><span data-stu-id="ccb2a-191">Returned from server</span></span>     |
| `http://example.com?key1=value1` | <span data-ttu-id="ccb2a-192">Retornado de middleware</span><span class="sxs-lookup"><span data-stu-id="ccb2a-192">Returned from middleware</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="ccb2a-193">Retornado do servidor</span><span class="sxs-lookup"><span data-stu-id="ccb2a-193">Returned from server</span></span>     |

<span data-ttu-id="ccb2a-194">A primeira solicitação é retornada pelo servidor e armazenados em cache no middleware.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-194">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="ccb2a-195">A segunda solicitação é retornada pelo middleware, como a cadeia de caracteres de consulta corresponde a solicitação anterior.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-195">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="ccb2a-196">A terceira solicitação não está no cache do middleware porque o valor de cadeia de caracteres de consulta não corresponde a uma solicitação anterior.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-196">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span> 

<span data-ttu-id="ccb2a-197">O `ResponseCacheAttribute` é usado para configurar e criar (via `IFilterFactory`) uma [ResponseCacheFilter](/dotnet/api/microsoft.aspnetcore.mvc.internal.responsecachefilter).</span><span class="sxs-lookup"><span data-stu-id="ccb2a-197">The `ResponseCacheAttribute` is used to configure and create (via `IFilterFactory`) a [ResponseCacheFilter](/dotnet/api/microsoft.aspnetcore.mvc.internal.responsecachefilter).</span></span> <span data-ttu-id="ccb2a-198">O `ResponseCacheFilter` realiza o trabalho de atualização de cabeçalhos HTTP apropriados e recursos da resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-198">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="ccb2a-199">O filtro:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-199">The filter:</span></span>

* <span data-ttu-id="ccb2a-200">Remove qualquer cabeçalho existente para `Vary`, `Cache-Control`, e `Pragma`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-200">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span> 
* <span data-ttu-id="ccb2a-201">Grava os cabeçalhos apropriados com base nas propriedades definidas `ResponseCacheAttribute`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-201">Writes out the appropriate headers based on the properties set in the `ResponseCacheAttribute`.</span></span> 
* <span data-ttu-id="ccb2a-202">Atualiza a resposta de armazenamento em cache o recurso HTTP se `VaryByQueryKeys` está definido.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-202">Updates the response caching HTTP feature if `VaryByQueryKeys` is set.</span></span>

### <a name="vary"></a><span data-ttu-id="ccb2a-203">Variar</span><span class="sxs-lookup"><span data-stu-id="ccb2a-203">Vary</span></span>

<span data-ttu-id="ccb2a-204">Esse cabeçalho só será gravado quando o `VaryByHeader` propriedade está definida.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-204">This header is only written when the `VaryByHeader` property is set.</span></span> <span data-ttu-id="ccb2a-205">Ele é definido como o `Vary` valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-205">It's set to the `Vary` property's value.</span></span> <span data-ttu-id="ccb2a-206">O exemplo a seguir usa o `VaryByHeader` propriedade:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-206">The following sample uses the `VaryByHeader` property:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

<span data-ttu-id="ccb2a-207">Você pode exibir os cabeçalhos de resposta com as ferramentas de rede do seu navegador.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-207">You can view the response headers with your browser's network tools.</span></span> <span data-ttu-id="ccb2a-208">A imagem a seguir mostra F12 borda de saída na **rede** guia quando o `About2` método de ação é atualizado:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-208">The following image shows the Edge F12 output on the **Network** tab when the `About2` action method is refreshed:</span></span>

![Na guia rede de borda F12 saída quando o método de ação About2 é chamado](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a><span data-ttu-id="ccb2a-210">NoStore e Location.None</span><span class="sxs-lookup"><span data-stu-id="ccb2a-210">NoStore and Location.None</span></span>

<span data-ttu-id="ccb2a-211">`NoStore` substitui a maioria das outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-211">`NoStore` overrides most of the other properties.</span></span> <span data-ttu-id="ccb2a-212">Quando essa propriedade é definida como `true`, o `Cache-Control` cabeçalho é definido como `no-store`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-212">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="ccb2a-213">Se `Location` é definido como `None`:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-213">If `Location` is set to `None`:</span></span>

* <span data-ttu-id="ccb2a-214">`Cache-Control` é definido como `no-store,no-cache`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-214">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="ccb2a-215">`Pragma` é definido como `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-215">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="ccb2a-216">Se `NoStore` está `false` e `Location` é `None`, `Cache-Control` e `Pragma` são definidos como `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-216">If `NoStore` is `false` and `Location` is `None`, `Cache-Control` and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="ccb2a-217">Você normalmente define `NoStore` para `true` nas páginas de erro.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-217">You typically set `NoStore` to `true` on error pages.</span></span> <span data-ttu-id="ccb2a-218">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-218">For example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

<span data-ttu-id="ccb2a-219">Isso resulta nos seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-219">This results in the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="ccb2a-220">Local e a duração</span><span class="sxs-lookup"><span data-stu-id="ccb2a-220">Location and Duration</span></span>

<span data-ttu-id="ccb2a-221">Para habilitar o cache, `Duration` deve ser definida como um valor positivo e `Location` deve ser `Any` (o padrão) ou `Client`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-221">To enable caching, `Duration` must be set to a positive value and `Location` must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="ccb2a-222">Nesse caso, o `Cache-Control` cabeçalho é definido como o valor do local seguido o `max-age` da resposta.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-222">In this case, the `Cache-Control` header is set to the location value followed by the `max-age` of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="ccb2a-223">`Location`da opções dos `Any` e `Client` traduzir `Cache-Control` valores de cabeçalho da `public` e `private`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-223">`Location`'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="ccb2a-224">Conforme observado anteriormente, definindo `Location` à `None` define ambas `Cache-Control` e `Pragma` cabeçalhos para `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-224">As noted previously, setting `Location` to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="ccb2a-225">Veja abaixo um exemplo que mostra os cabeçalhos é produzido pela configuração `Duration` e deixar o padrão `Location` valor:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-225">Below is an example showing the headers produced by setting `Duration` and leaving the default `Location` value:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

<span data-ttu-id="ccb2a-226">Isso produz o seguinte cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-226">This produces the following header:</span></span>

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a><span data-ttu-id="ccb2a-227">Perfis de cache</span><span class="sxs-lookup"><span data-stu-id="ccb2a-227">Cache profiles</span></span>

<span data-ttu-id="ccb2a-228">Em vez de duplicar `ResponseCache` configurações de muitos atributos de ação do controlador, perfis de cache podem ser configuradas como opções ao configurar o MVC em de `ConfigureServices` método na `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-228">Instead of duplicating `ResponseCache` settings on many controller action attributes, cache profiles can be configured as options when setting up MVC in the `ConfigureServices` method in `Startup`.</span></span> <span data-ttu-id="ccb2a-229">Valores encontrados em um perfil de cache referenciada são usados como padrões pelo `ResponseCache` de atributos e são substituídas por qualquer propriedade especificada no atributo.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-229">Values found in a referenced cache profile are used as the defaults by the `ResponseCache` attribute and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="ccb2a-230">Como configurar um perfil de cache:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-230">Setting up a cache profile:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="ccb2a-231">Um perfil de cache de referência:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-231">Referencing a cache profile:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

<span data-ttu-id="ccb2a-232">O `ResponseCache` atributo pode ser aplicado a ações (métodos) e controladores (classes).</span><span class="sxs-lookup"><span data-stu-id="ccb2a-232">The `ResponseCache` attribute can be applied both to actions (methods) and controllers (classes).</span></span> <span data-ttu-id="ccb2a-233">Atributos de nível de método substituem as configurações especificadas no nível de classe de atributos.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-233">Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="ccb2a-234">No exemplo acima, um atributo de nível de classe especifica uma duração de 30 segundos, enquanto um atributo de nível de método faz referência a um perfil de cache com uma duração definida como 60 segundos.</span><span class="sxs-lookup"><span data-stu-id="ccb2a-234">In the above example, a class-level attribute specifies a duration of 30 seconds, while a method-level attribute references a cache profile with a duration set to 60 seconds.</span></span>

<span data-ttu-id="ccb2a-235">O cabeçalho resultante:</span><span class="sxs-lookup"><span data-stu-id="ccb2a-235">The resulting header:</span></span>

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a><span data-ttu-id="ccb2a-236">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ccb2a-236">Additional resources</span></span>

* [<span data-ttu-id="ccb2a-237">Armazena as respostas em Caches</span><span class="sxs-lookup"><span data-stu-id="ccb2a-237">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="ccb2a-238">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="ccb2a-238">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
