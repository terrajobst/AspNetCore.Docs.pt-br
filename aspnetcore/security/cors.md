---
title: Habilitar solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como CORS como padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: fe5b750c44e5fad9ba80efb2cc8116d0a64b1a17
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068291"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="f7622-103">Habilitar solicitações entre origens (CORS) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7622-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="f7622-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7622-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7622-105">Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7622-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="f7622-106">Segurança do navegador impede que uma página da web fazendo solicitações para um domínio diferente daquele que atendia a página da web.</span><span class="sxs-lookup"><span data-stu-id="f7622-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="f7622-107">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="f7622-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="f7622-108">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="f7622-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="f7622-109">Às vezes, você talvez queira permitir que outros sites fazem solicitações entre origens em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7622-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="f7622-110">Para obter mais informações, consulte o [artigo Mozilla CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="f7622-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="f7622-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="f7622-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="f7622-112">É um W3C padrão que permite que um servidor relaxar a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="f7622-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="f7622-113">Está **não** um recurso de segurança, o CORS alivia a segurança.</span><span class="sxs-lookup"><span data-stu-id="f7622-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="f7622-114">Uma API não é mais segura, permitindo que o CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="f7622-115">Para obter mais informações, consulte [funciona como o CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="f7622-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="f7622-116">Permite que um servidor explicitamente permitir algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="f7622-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="f7622-117">É mais seguro e mais flexível do que técnicas anteriores, tais como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="f7622-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="f7622-118">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7622-118">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="f7622-119">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="f7622-119">Same origin</span></span>

<span data-ttu-id="f7622-120">Duas URLs têm a mesma origem se eles têm esquemas idênticas, hosts e portas ([6454 RFC](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="f7622-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="f7622-121">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="f7622-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="f7622-122">Essas URLs têm diferentes origens que as duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="f7622-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="f7622-123">`https://example.net` &ndash; Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="f7622-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="f7622-124">`https://www.example.com/foo.html` &ndash; Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="f7622-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="f7622-125">`http://example.com/foo.html` &ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="f7622-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="f7622-126">`https://example.com:9000/foo.html` &ndash; Porta diferente</span><span class="sxs-lookup"><span data-stu-id="f7622-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="f7622-127">Internet Explorer não considera a porta ao comparar as origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="f7622-128">CORS com middleware e nomeado de política</span><span class="sxs-lookup"><span data-stu-id="f7622-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="f7622-129">Middleware CORS manipula solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="f7622-130">O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="f7622-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="f7622-131">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="f7622-131">The preceding code:</span></span>

* <span data-ttu-id="f7622-132">Define o nome da política para "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="f7622-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="f7622-133">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="f7622-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="f7622-134">Chama o <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensão, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="f7622-135">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com um [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="f7622-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f7622-136">O lambda utiliza um <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto.</span><span class="sxs-lookup"><span data-stu-id="f7622-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="f7622-137">[Opções de configuração](#cors-policy-options), tais como `WithOrigins`, são descritos neste artigo.</span><span class="sxs-lookup"><span data-stu-id="f7622-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="f7622-138">O <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada de método adiciona serviços do CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f7622-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="f7622-139">Para obter mais informações, consulte [opções de política CORS](#cpo) neste documento.</span><span class="sxs-lookup"><span data-stu-id="f7622-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="f7622-140">O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode encadear os métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f7622-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="f7622-141">O seguinte código realçado se aplica a políticas CORS para todos os pontos de extremidade aplicativos por meio do Middleware do CORS:</span><span class="sxs-lookup"><span data-stu-id="f7622-141">The following highlighted code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```

<span data-ttu-id="f7622-142">Ver [habilitar CORS em páginas do Razor, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="f7622-142">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="f7622-143">Observação:</span><span class="sxs-lookup"><span data-stu-id="f7622-143">Note:</span></span>

* <span data-ttu-id="f7622-144">`UseCors` deve ser chamado antes de `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="f7622-144">`UseCors` must be called before `UseMvc`.</span></span>
* <span data-ttu-id="f7622-145">A URL deve **não** contêm uma barra à direita (`/`).</span><span class="sxs-lookup"><span data-stu-id="f7622-145">The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="f7622-146">Se a URL termina com `/`, a comparação retorna `false` e nenhum cabeçalho é retornado.</span><span class="sxs-lookup"><span data-stu-id="f7622-146">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="f7622-147">Ver [CORS teste](#test) para obter instruções sobre como testar o código anterior.</span><span class="sxs-lookup"><span data-stu-id="f7622-147">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="f7622-148">Habilitar o CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="f7622-148">Enable CORS with attributes</span></span>

<span data-ttu-id="f7622-149">O [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atributo fornece uma alternativa de aplicação CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="f7622-149">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="f7622-150">O `[EnableCors]` atributo habilita o CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="f7622-150">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="f7622-151">Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.</span><span class="sxs-lookup"><span data-stu-id="f7622-151">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="f7622-152">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="f7622-152">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="f7622-153">Página do Razor `PageModel`</span><span class="sxs-lookup"><span data-stu-id="f7622-153">Razor Page `PageModel`</span></span>
* <span data-ttu-id="f7622-154">Controlador</span><span class="sxs-lookup"><span data-stu-id="f7622-154">Controller</span></span>
* <span data-ttu-id="f7622-155">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="f7622-155">Controller action method</span></span>

<span data-ttu-id="f7622-156">Você pode aplicar políticas diferentes para o controlador/página-modelo/ação com o `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="f7622-156">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="f7622-157">Quando o `[EnableCors]` atributo é aplicado a um método de controladores/página-modelo/ação e CORS estiver habilitado no middleware, ambas as políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="f7622-157">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="f7622-158">É recomendável combinar as políticas.</span><span class="sxs-lookup"><span data-stu-id="f7622-158">We recommend against combining policies.</span></span> <span data-ttu-id="f7622-159">Use o `[EnableCors]` atributo ou middleware, não ambos no mesmo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7622-159">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="f7622-160">O código a seguir se aplica a uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="f7622-160">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="f7622-161">O código a seguir cria uma política de CORS padrão e uma política chamada `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="f7622-161">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="f7622-162">Desabilitar o CORS</span><span class="sxs-lookup"><span data-stu-id="f7622-162">Disable CORS</span></span>

<span data-ttu-id="f7622-163">O [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) atributo desabilita o CORS para o controlador/página modelo/ação.</span><span class="sxs-lookup"><span data-stu-id="f7622-163">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="f7622-164">Opções de política de CORS</span><span class="sxs-lookup"><span data-stu-id="f7622-164">CORS policy options</span></span>

<span data-ttu-id="f7622-165">Esta seção descreve as várias opções que podem ser definidas em uma política CORS:</span><span class="sxs-lookup"><span data-stu-id="f7622-165">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="f7622-166">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="f7622-166">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="f7622-167">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="f7622-167">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="f7622-168">Definir os cabeçalhos de solicitação permitido</span><span class="sxs-lookup"><span data-stu-id="f7622-168">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="f7622-169">Definir os cabeçalhos de resposta exposto</span><span class="sxs-lookup"><span data-stu-id="f7622-169">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="f7622-170">Credenciais nas solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="f7622-170">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="f7622-171">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="f7622-171">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="f7622-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> é chamado no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f7622-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f7622-173">Para algumas opções, pode ser útil ler o [funciona como o CORS](#how-cors) seção pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="f7622-173">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="f7622-174">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="f7622-174">Set the allowed origins</span></span>

<span data-ttu-id="f7622-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Permite que as solicitações CORS de todas as origens com qualquer esquema (`http` ou `https`).</span><span class="sxs-lookup"><span data-stu-id="f7622-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="f7622-176">`AllowAnyOrigin` não é seguro porque *de qualquer site* pode fazer solicitações entre origens para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7622-176">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="f7622-177">Especificando `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e podem resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="f7622-177">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="f7622-178">O serviço CORS retorna uma resposta inválida do CORS, quando um aplicativo é configurado com os dois métodos.</span><span class="sxs-lookup"><span data-stu-id="f7622-178">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="f7622-179">Especificando `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e podem resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="f7622-179">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="f7622-180">Para um aplicativo seguro, especifique uma lista exata de origens, se o cliente deve autorizar a mesmo para acessar recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="f7622-180">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**.
-->

<span data-ttu-id="f7622-181">`AllowAnyOrigin` solicitações de simulação afeta e o `Access-Control-Allow-Origin` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="f7622-181">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="f7622-182">Para obter mais informações, consulte o [solicitações de simulação](#preflight-requests) seção.</span><span class="sxs-lookup"><span data-stu-id="f7622-182">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f7622-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Conjuntos de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriedade da política para ser uma função que permite origens corresponder a um domínio de curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="f7622-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="f7622-184">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="f7622-184">Set the allowed HTTP methods</span></span>

<span data-ttu-id="f7622-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="f7622-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="f7622-186">Permite que qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="f7622-186">Allows any HTTP method:</span></span>
* <span data-ttu-id="f7622-187">Solicitações de simulação afeta e o `Access-Control-Allow-Methods` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="f7622-187">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="f7622-188">Para obter mais informações, consulte o [solicitações de simulação](#preflight-requests) seção.</span><span class="sxs-lookup"><span data-stu-id="f7622-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="f7622-189">Definir os cabeçalhos de solicitação permitido</span><span class="sxs-lookup"><span data-stu-id="f7622-189">Set the allowed request headers</span></span>

<span data-ttu-id="f7622-190">Para permitir que os cabeçalhos específicos a serem enviados em uma solicitação CORS, chamado *criar cabeçalhos de solicitação*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especificar os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="f7622-190">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="f7622-191">Para permitir que todos os cabeçalhos de solicitação do autor chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="f7622-191">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="f7622-192">Essa configuração afeta as solicitações de simulação e o `Access-Control-Request-Headers` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="f7622-192">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="f7622-193">Para obter mais informações, consulte o [solicitações de simulação](#preflight-requests) seção.</span><span class="sxs-lookup"><span data-stu-id="f7622-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f7622-194">Uma correspondência de política de Middleware do CORS para cabeçalhos específicos especificado por `WithHeaders` só é possível quando os cabeçalhos enviados `Access-Control-Request-Headers` coincidir exatamente com os cabeçalhos indicados na `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="f7622-194">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="f7622-195">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="f7622-195">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="f7622-196">Middleware CORS recusar uma solicitação de simulação com o seguinte cabeçalho de solicitação, pois `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado no `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="f7622-196">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="f7622-197">O aplicativo retorna um *200 Okey* resposta, mas não envia de volta os cabeçalhos de CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-197">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="f7622-198">Portanto, o navegador não tente a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-198">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f7622-199">Middleware CORS sempre permite que os cabeçalhos de quatro no `Access-Control-Request-Headers` a ser enviado, independentemente dos valores configurados no CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="f7622-199">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="f7622-200">Esta lista de cabeçalhos inclui:</span><span class="sxs-lookup"><span data-stu-id="f7622-200">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="f7622-201">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="f7622-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="f7622-202">Middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` é sempre na lista de permissões:</span><span class="sxs-lookup"><span data-stu-id="f7622-202">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="f7622-203">Definir os cabeçalhos de resposta exposto</span><span class="sxs-lookup"><span data-stu-id="f7622-203">Set the exposed response headers</span></span>

<span data-ttu-id="f7622-204">Por padrão, o navegador não expõe todos os cabeçalhos de resposta para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7622-204">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="f7622-205">Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (terminologia): Cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="f7622-205">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="f7622-206">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="f7622-206">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="f7622-207">A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="f7622-207">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="f7622-208">Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="f7622-208">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="f7622-209">Credenciais nas solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="f7622-209">Credentials in cross-origin requests</span></span>

<span data-ttu-id="f7622-210">Credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-210">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="f7622-211">Por padrão, o navegador não envia as credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-211">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="f7622-212">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7622-212">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="f7622-213">Para enviar as credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` para `true`.</span><span class="sxs-lookup"><span data-stu-id="f7622-213">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="f7622-214">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="f7622-214">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="f7622-215">Usando o jQuery:</span><span class="sxs-lookup"><span data-stu-id="f7622-215">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="f7622-216">Usando o [buscar API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="f7622-216">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="f7622-217">O servidor deve permitir que as credenciais.</span><span class="sxs-lookup"><span data-stu-id="f7622-217">The server must allow the credentials.</span></span> <span data-ttu-id="f7622-218">Para permitir credenciais entre origens, chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="f7622-218">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="f7622-219">A resposta HTTP inclui um `Access-Control-Allow-Credentials` cabeçalho, que informa ao navegador que o servidor permite que as credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-219">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="f7622-220">Se o navegador envia as credenciais, mas a resposta não inclui um válido `Access-Control-Allow-Credentials` cabeçalho, o navegador não expõe a resposta para o aplicativo e a solicitação entre origens falhar.</span><span class="sxs-lookup"><span data-stu-id="f7622-220">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="f7622-221">Permitindo credenciais entre origens é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="f7622-221">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="f7622-222">Um site em outro domínio pode enviar credenciais do usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="f7622-222">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="f7622-223">A especificação CORS também declara que a configuração origens `"*"` (todas as origens) é inválida se o `Access-Control-Allow-Credentials` cabeçalho está presente.</span><span class="sxs-lookup"><span data-stu-id="f7622-223">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="f7622-224">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="f7622-224">Preflight requests</span></span>

<span data-ttu-id="f7622-225">Para algumas solicitações CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="f7622-225">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="f7622-226">Essa solicitação é chamada de um *solicitação de simulação*.</span><span class="sxs-lookup"><span data-stu-id="f7622-226">This request is called a *preflight request*.</span></span> <span data-ttu-id="f7622-227">O navegador pode ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="f7622-227">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="f7622-228">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="f7622-228">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="f7622-229">O aplicativo não define cabeçalhos de solicitação diferente de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="f7622-229">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="f7622-230">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="f7622-230">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="f7622-231">A regra em cabeçalhos de solicitação definido para a solicitação do cliente se aplica aos cabeçalhos que o aplicativo define chamando `setRequestHeader` sobre o `XMLHttpRequest` objeto.</span><span class="sxs-lookup"><span data-stu-id="f7622-231">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="f7622-232">A especificação CORS chama esses cabeçalhos *criar cabeçalhos de solicitação*.</span><span class="sxs-lookup"><span data-stu-id="f7622-232">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="f7622-233">A regra não se aplica aos cabeçalhos, o navegador pode definir, tais como `User-Agent`, `Host`, ou `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="f7622-233">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="f7622-234">Este é um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="f7622-234">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="f7622-235">A solicitação de simulação usa o método HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="f7622-235">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="f7622-236">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="f7622-236">It includes two special headers:</span></span>

* <span data-ttu-id="f7622-237">`Access-Control-Request-Method`: O método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="f7622-237">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="f7622-238">`Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="f7622-238">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="f7622-239">Conforme mencionado anteriormente, isso não inclui os cabeçalhos que define o navegador, tais como `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="f7622-239">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="f7622-240">Uma solicitação de simulação de CORS pode incluir um `Access-Control-Request-Headers` cabeçalho, que indica ao servidor, os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="f7622-240">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="f7622-241">Para permitir que os cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="f7622-241">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="f7622-242">Para permitir que todos os cabeçalhos de solicitação do autor chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="f7622-242">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="f7622-243">Navegadores não estão totalmente consistentes em como eles definir `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="f7622-243">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="f7622-244">Se você definir os cabeçalhos para algo diferente de `"*"` (ou use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), você deve incluir pelo menos `Accept`, `Content-Type`, e `Origin`, além de quaisquer cabeçalhos personalizados que você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="f7622-244">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="f7622-245">Este é um exemplo de resposta à solicitação de simulação (supondo que o servidor permite que a solicitação):</span><span class="sxs-lookup"><span data-stu-id="f7622-245">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="f7622-246">A resposta inclui um `Access-Control-Allow-Methods` cabeçalho que lista os métodos permitidos e, opcionalmente, um `Access-Control-Allow-Headers` cabeçalho, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="f7622-246">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="f7622-247">Se a solicitação de simulação for bem-sucedida, o navegador envia a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="f7622-247">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="f7622-248">Se a solicitação de simulação for negada, o aplicativo retornar um *200 Okey* resposta, mas não envia de volta os cabeçalhos de CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-248">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="f7622-249">Portanto, o navegador não tente a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-249">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="f7622-250">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="f7622-250">Set the preflight expiration time</span></span>

<span data-ttu-id="f7622-251">O `Access-Control-Max-Age` cabeçalho Especifica quanto tempo a resposta à solicitação de simulação pode ser armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="f7622-251">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="f7622-252">Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="f7622-252">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="f7622-253">Como funciona o CORS</span><span class="sxs-lookup"><span data-stu-id="f7622-253">How CORS works</span></span>

<span data-ttu-id="f7622-254">Esta seção descreve o que acontece em um [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) solicitação no nível das mensagens HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7622-254">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="f7622-255">O CORS é **não** um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="f7622-255">CORS is **not** a security feature.</span></span> <span data-ttu-id="f7622-256">O CORS é um padrão W3C que permite que um servidor relaxar a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="f7622-256">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="f7622-257">Por exemplo, um ator mal-intencionado pode usar [impedir Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) em relação a seu site e executar uma solicitação entre sites para o respectivo site CORS habilitado para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="f7622-257">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="f7622-258">Sua API não é mais segura, permitindo que o CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-258">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="f7622-259">Cabe ao cliente (navegador) para impor o CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-259">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="f7622-260">O servidor executa a solicitação e retorna a resposta, ele é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="f7622-260">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="f7622-261">Por exemplo, qualquer uma das ferramentas a seguir exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="f7622-261">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="f7622-262">Fiddler</span><span class="sxs-lookup"><span data-stu-id="f7622-262">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="f7622-263">Postman</span><span class="sxs-lookup"><span data-stu-id="f7622-263">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="f7622-264">HttpClient do .NET</span><span class="sxs-lookup"><span data-stu-id="f7622-264">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="f7622-265">Um navegador da web inserindo a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="f7622-265">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="f7622-266">É uma maneira para um servidor permitir que os navegadores para executar uma entre origens [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) ou [API buscar](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) solicitação caso contrário, seria proibida.</span><span class="sxs-lookup"><span data-stu-id="f7622-266">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="f7622-267">Navegadores (sem CORS) não podem fazer solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-267">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="f7622-268">Antes de CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para evitar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="f7622-268">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="f7622-269">JSONP não usa XHR, ele usa o `<script>` marca receba a resposta.</span><span class="sxs-lookup"><span data-stu-id="f7622-269">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="f7622-270">Scripts podem ser carregados entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-270">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="f7622-271">O [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem que solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-271">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="f7622-272">Se um navegador oferece suporte a CORS, ele define esses cabeçalhos automaticamente para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-272">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="f7622-273">Código JavaScript personalizado não é necessário para habilitar o CORS.</span><span class="sxs-lookup"><span data-stu-id="f7622-273">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="f7622-274">O exemplo a seguir é um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-274">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="f7622-275">O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação:</span><span class="sxs-lookup"><span data-stu-id="f7622-275">The `Origin` header provides the domain of the site that's making the request:</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="f7622-276">Se o servidor permite que a solicitação, ele define o `Access-Control-Allow-Origin` cabeçalho na resposta.</span><span class="sxs-lookup"><span data-stu-id="f7622-276">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="f7622-277">O valor desse cabeçalho também corresponde a `Origin` cabeçalho da solicitação ou é o valor de curinga `"*"`, o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="f7622-277">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="f7622-278">Se a resposta não inclui o `Access-Control-Allow-Origin` falha do cabeçalho, a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="f7622-278">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="f7622-279">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="f7622-279">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="f7622-280">Mesmo se o servidor retorna uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="f7622-280">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="f7622-281">Testar CORS</span><span class="sxs-lookup"><span data-stu-id="f7622-281">Test CORS</span></span>

<span data-ttu-id="f7622-282">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="f7622-282">To test CORS:</span></span>

1. <span data-ttu-id="f7622-283">[Criar um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="f7622-283">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="f7622-284">Como alternativa, você pode [baixar o exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="f7622-284">Alternatively, you can [download the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="f7622-285">Habilite CORS usando uma das abordagens neste documento.</span><span class="sxs-lookup"><span data-stu-id="f7622-285">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="f7622-286">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f7622-286">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="f7622-287">`WithOrigins("https://localhost:<port>");` só deve ser usado para testar um aplicativo de exemplo semelhante para o [baixar o código de exemplo](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="f7622-287">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="f7622-288">Crie um projeto de aplicativo web (páginas do Razor ou MVC).</span><span class="sxs-lookup"><span data-stu-id="f7622-288">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="f7622-289">O exemplo usa as páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="f7622-289">The sample uses Razor Pages.</span></span> <span data-ttu-id="f7622-290">Você pode criar o aplicativo web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="f7622-290">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="f7622-291">Adicione o seguinte código realçado para o *index. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="f7622-291">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="f7622-292">No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` com a URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="f7622-292">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="f7622-293">Implante o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="f7622-293">Deploy the API project.</span></span> <span data-ttu-id="f7622-294">Por exemplo, [implantar no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="f7622-294">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="f7622-295">Execute o aplicativo de páginas do Razor ou MVC da área de trabalho e clique no **teste** botão.</span><span class="sxs-lookup"><span data-stu-id="f7622-295">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="f7622-296">Use as ferramentas de F12 para examinar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="f7622-296">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="f7622-297">Remover a origem do localhost de `WithOrigins` e implantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7622-297">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="f7622-298">Como alternativa, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="f7622-298">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="f7622-299">Por exemplo, execute do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f7622-299">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="f7622-300">Teste com o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="f7622-300">Test with the client app.</span></span> <span data-ttu-id="f7622-301">Falhas CORS retornar um erro, mas a mensagem de erro não está disponível para o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f7622-301">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="f7622-302">Use a guia console nas ferramentas F12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="f7622-302">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="f7622-303">Dependendo do navegador, você receberá um erro (no console de ferramentas de F12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="f7622-303">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="f7622-304">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="f7622-304">Using Microsoft Edge:</span></span>

     <span data-ttu-id="f7622-305">**SEC7120: [CORS] a origem `https://localhost:44375` não foi possível localizar `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para recursos entre origens em `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="f7622-305">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="f7622-306">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="f7622-306">Using Chrome:</span></span>

     <span data-ttu-id="f7622-307">**Acesso ao XMLHttpRequest em `https://webapi.azurewebsites.net/api/values/1` de origem `https://localhost:44375` foi bloqueada pela política CORS: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="f7622-307">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7622-308">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7622-308">Additional resources</span></span>

* [<span data-ttu-id="f7622-309">Cross-Origin Resource Sharing (CORS)</span><span class="sxs-lookup"><span data-stu-id="f7622-309">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
