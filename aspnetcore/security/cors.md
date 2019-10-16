---
title: Habilitar as solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como o CORS como um padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: security/cors
ms.openlocfilehash: 3a51d365626c858ad48298a1108e37eba9050fe7
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391304"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="82bad-103">Habilitar as solicitações entre origens (CORS) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82bad-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="82bad-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="82bad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="82bad-105">Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82bad-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="82bad-106">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="82bad-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="82bad-107">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="82bad-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="82bad-108">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="82bad-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="82bad-109">Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82bad-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="82bad-110">Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="82bad-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="82bad-111">CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="82bad-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="82bad-112">É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="82bad-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="82bad-113">**Não** é um recurso de segurança, o CORS libera a segurança.</span><span class="sxs-lookup"><span data-stu-id="82bad-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="82bad-114">Uma API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="82bad-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="82bad-115">Para obter mais informações, consulte [como o CORS funciona](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="82bad-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="82bad-116">Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="82bad-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="82bad-117">É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="82bad-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="82bad-118">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="82bad-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="82bad-119">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="82bad-119">Same origin</span></span>

<span data-ttu-id="82bad-120">Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="82bad-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="82bad-121">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="82bad-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="82bad-122">Essas URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="82bad-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="82bad-123">`https://example.net` &ndash; domínio diferente</span><span class="sxs-lookup"><span data-stu-id="82bad-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="82bad-124">`https://www.example.com/foo.html` &ndash; subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="82bad-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="82bad-125">`http://example.com/foo.html` &ndash; esquema diferente</span><span class="sxs-lookup"><span data-stu-id="82bad-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="82bad-126">`https://example.com:9000/foo.html` &ndash; porta diferente</span><span class="sxs-lookup"><span data-stu-id="82bad-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="82bad-127">O Internet Explorer não considera a porta ao comparar origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="82bad-128">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="82bad-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="82bad-129">O middleware CORS lida com solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="82bad-130">O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="82bad-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="82bad-131">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="82bad-131">The preceding code:</span></span>

* <span data-ttu-id="82bad-132">Define o nome da política como "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="82bad-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="82bad-133">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="82bad-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="82bad-134">Chama o método de extensão <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="82bad-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="82bad-135">Chama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="82bad-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="82bad-136">O lambda usa um objeto <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>.</span><span class="sxs-lookup"><span data-stu-id="82bad-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="82bad-137">[Opções de configuração](#cors-policy-options), como `WithOrigins`, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="82bad-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="82bad-138">A chamada de método <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="82bad-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="82bad-139">Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.</span><span class="sxs-lookup"><span data-stu-id="82bad-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="82bad-140">O método <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> pode encadear métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="82bad-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="82bad-141">Observação: a URL **não** deve conter uma barra à direita (`/`).</span><span class="sxs-lookup"><span data-stu-id="82bad-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="82bad-142">Se a URL for encerrada com `/`, a comparação retornará `false` e nenhum cabeçalho será retornado.</span><span class="sxs-lookup"><span data-stu-id="82bad-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="82bad-143">Aplicar políticas CORS a todos os pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="82bad-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="82bad-144">O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="82bad-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> <span data-ttu-id="82bad-145">Com o roteamento de ponto de extremidade, o middleware CORS deve ser configurado para ser executado entre as chamadas para `UseRouting` e `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="82bad-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="82bad-146">A configuração incorreta fará com que o middleware pare de funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="82bad-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="82bad-147">O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="82bad-147">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="82bad-148">Observação: `UseCors` deve ser chamado antes de `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="82bad-148">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="82bad-149">Consulte [habilitar CORS em Razor pages, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="82bad-149">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="82bad-150">Consulte [testar CORS](#test) para obter instruções sobre como testar o código anterior.</span><span class="sxs-lookup"><span data-stu-id="82bad-150">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="82bad-151">Habilitar CORS com roteamento de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="82bad-151">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="82bad-152">Com o roteamento de ponto de extremidade, o CORS pode ser habilitado em uma base por ponto de extremidade usando o conjunto `RequireCors` de métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="82bad-152">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="82bad-153">Da mesma forma, o CORS também pode ser habilitado para todos os controladores:</span><span class="sxs-lookup"><span data-stu-id="82bad-153">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="82bad-154">Habilitar CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="82bad-154">Enable CORS with attributes</span></span>

<span data-ttu-id="82bad-155">O atributo [&lbrack;EnableCors @ no__t-2](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="82bad-155">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="82bad-156">O atributo `[EnableCors]` habilita o CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="82bad-156">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="82bad-157">Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.</span><span class="sxs-lookup"><span data-stu-id="82bad-157">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="82bad-158">O atributo `[EnableCors]` pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="82bad-158">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="82bad-159">@No__t de página Razor-0</span><span class="sxs-lookup"><span data-stu-id="82bad-159">Razor Page `PageModel`</span></span>
* <span data-ttu-id="82bad-160">Controlador</span><span class="sxs-lookup"><span data-stu-id="82bad-160">Controller</span></span>
* <span data-ttu-id="82bad-161">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="82bad-161">Controller action method</span></span>

<span data-ttu-id="82bad-162">Você pode aplicar políticas diferentes ao controlador/página-modelo/ação com o atributo `[EnableCors]`.</span><span class="sxs-lookup"><span data-stu-id="82bad-162">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="82bad-163">Quando o atributo `[EnableCors]` é aplicado a um método de controladores/página-modelo/ação, e o CORS é habilitado no middleware, ambas as políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="82bad-163">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="82bad-164">Recomendamos a combinação de políticas.</span><span class="sxs-lookup"><span data-stu-id="82bad-164">We recommend against combining policies.</span></span> <span data-ttu-id="82bad-165">Use o atributo `[EnableCors]` ou o middleware, não ambos no mesmo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82bad-165">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="82bad-166">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="82bad-166">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="82bad-167">O código a seguir cria uma política padrão CORS e uma política chamada `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="82bad-167">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="82bad-168">Desabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="82bad-168">Disable CORS</span></span>

<span data-ttu-id="82bad-169">O atributo [&lbrack;DisableCors @ no__t-2](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) DESABILITA o CORS para o controlador/página-modelo/ação.</span><span class="sxs-lookup"><span data-stu-id="82bad-169">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="82bad-170">Opções de política de CORS</span><span class="sxs-lookup"><span data-stu-id="82bad-170">CORS policy options</span></span>

<span data-ttu-id="82bad-171">Esta seção descreve as várias opções que podem ser definidas em uma política CORS:</span><span class="sxs-lookup"><span data-stu-id="82bad-171">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="82bad-172">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="82bad-172">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="82bad-173">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="82bad-173">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="82bad-174">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="82bad-174">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="82bad-175">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="82bad-175">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="82bad-176">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="82bad-176">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="82bad-177">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="82bad-177">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="82bad-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> é chamado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="82bad-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="82bad-179">Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="82bad-179">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="82bad-180">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="82bad-180">Set the allowed origins</span></span>

<span data-ttu-id="82bad-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; permite solicitações CORS de todas as origens com qualquer esquema (`http` ou `https`).</span><span class="sxs-lookup"><span data-stu-id="82bad-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="82bad-182">`AllowAnyOrigin` não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82bad-182">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="82bad-183">Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="82bad-183">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="82bad-184">O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.</span><span class="sxs-lookup"><span data-stu-id="82bad-184">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="82bad-185">Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="82bad-185">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="82bad-186">Para um aplicativo seguro, especifique uma lista exata de origens se o cliente precisar se autorizar para acessar os recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="82bad-186">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="82bad-187">`AllowAnyOrigin` afeta as solicitações de simulação e o cabeçalho `Access-Control-Allow-Origin`.</span><span class="sxs-lookup"><span data-stu-id="82bad-187">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="82bad-188">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="82bad-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="82bad-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; define a propriedade <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="82bad-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="82bad-190">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="82bad-190">Set the allowed HTTP methods</span></span>

<span data-ttu-id="82bad-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="82bad-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="82bad-192">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="82bad-192">Allows any HTTP method:</span></span>
* <span data-ttu-id="82bad-193">Afeta as solicitações de simulação e o cabeçalho `Access-Control-Allow-Methods`.</span><span class="sxs-lookup"><span data-stu-id="82bad-193">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="82bad-194">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="82bad-194">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="82bad-195">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="82bad-195">Set the allowed request headers</span></span>

<span data-ttu-id="82bad-196">Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada de *cabeçalhos de solicitação de autor*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="82bad-196">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="82bad-197">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="82bad-197">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="82bad-198">Essa configuração afeta as solicitações de simulação e o cabeçalho `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="82bad-198">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="82bad-199">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="82bad-199">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="82bad-200">Uma política de middleware CORS corresponde a cabeçalhos específicos especificados por `WithHeaders` só é possível quando os cabeçalhos enviados em `Access-Control-Request-Headers` correspondem exatamente aos cabeçalhos indicados em `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="82bad-200">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="82bad-201">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="82bad-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="82bad-202">O middleware CORS recusa uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` ([headernames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado em `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="82bad-202">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="82bad-203">O aplicativo retorna uma resposta de *200 OK* , mas não envia os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="82bad-203">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="82bad-204">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-204">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="82bad-205">O middleware CORS sempre permite que quatro cabeçalhos no `Access-Control-Request-Headers` sejam enviados, independentemente dos valores configurados em CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="82bad-205">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="82bad-206">Essa lista de cabeçalhos inclui:</span><span class="sxs-lookup"><span data-stu-id="82bad-206">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="82bad-207">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="82bad-207">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="82bad-208">O middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` está sempre na lista de permissões:</span><span class="sxs-lookup"><span data-stu-id="82bad-208">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="82bad-209">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="82bad-209">Set the exposed response headers</span></span>

<span data-ttu-id="82bad-210">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82bad-210">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="82bad-211">Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="82bad-211">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="82bad-212">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="82bad-212">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="82bad-213">A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="82bad-213">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="82bad-214">Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="82bad-214">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="82bad-215">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="82bad-215">Credentials in cross-origin requests</span></span>

<span data-ttu-id="82bad-216">As credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="82bad-216">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="82bad-217">Por padrão, o navegador não envia credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-217">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="82bad-218">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="82bad-218">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="82bad-219">Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true`.</span><span class="sxs-lookup"><span data-stu-id="82bad-219">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="82bad-220">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="82bad-220">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="82bad-221">Usando o jQuery:</span><span class="sxs-lookup"><span data-stu-id="82bad-221">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="82bad-222">Usando a [API de busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="82bad-222">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="82bad-223">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="82bad-223">The server must allow the credentials.</span></span> <span data-ttu-id="82bad-224">Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="82bad-224">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="82bad-225">A resposta HTTP inclui um cabeçalho `Access-Control-Allow-Credentials`, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-225">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="82bad-226">Se o navegador enviar credenciais, mas a resposta não incluir um cabeçalho `Access-Control-Allow-Credentials` válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="82bad-226">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="82bad-227">Permitir credenciais entre origens é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="82bad-227">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="82bad-228">Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="82bad-228">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="82bad-229">A especificação CORS também indica que a definição de origens como `"*"` (todas as origens) é inválida se o cabeçalho `Access-Control-Allow-Credentials` estiver presente.</span><span class="sxs-lookup"><span data-stu-id="82bad-229">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="82bad-230">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="82bad-230">Preflight requests</span></span>

<span data-ttu-id="82bad-231">Para algumas solicitações de CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="82bad-231">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="82bad-232">Essa solicitação é chamada de uma *solicitação de simulação*.</span><span class="sxs-lookup"><span data-stu-id="82bad-232">This request is called a *preflight request*.</span></span> <span data-ttu-id="82bad-233">O navegador poderá ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="82bad-233">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="82bad-234">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="82bad-234">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="82bad-235">O aplicativo não define cabeçalhos de solicitação diferentes de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type` ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="82bad-235">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="82bad-236">O cabeçalho `Content-Type`, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="82bad-236">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="82bad-237">A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente aplica-se aos cabeçalhos que o aplicativo define chamando `setRequestHeader` no objeto `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="82bad-237">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="82bad-238">A especificação CORS chama esses cabeçalhos de *solicitação de autor*de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="82bad-238">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="82bad-239">A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent`, `Host` ou `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="82bad-239">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="82bad-240">Veja a seguir um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="82bad-240">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="82bad-241">A solicitação de simulação usa o método de opções HTTP.</span><span class="sxs-lookup"><span data-stu-id="82bad-241">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="82bad-242">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="82bad-242">It includes two special headers:</span></span>

* <span data-ttu-id="82bad-243">`Access-Control-Request-Method`: o método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="82bad-243">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="82bad-244">`Access-Control-Request-Headers`: uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="82bad-244">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="82bad-245">Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="82bad-245">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="82bad-246">Uma solicitação de simulação de CORS pode incluir um cabeçalho `Access-Control-Request-Headers`, que indica ao servidor os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="82bad-246">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="82bad-247">Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="82bad-247">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="82bad-248">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="82bad-248">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="82bad-249">Os navegadores não são totalmente consistentes em como eles definem `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="82bad-249">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="82bad-250">Se você definir cabeçalhos para algo diferente de `"*"` (ou usar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), deverá incluir pelo menos `Accept`, `Content-Type` e `Origin`, além de todos os cabeçalhos personalizados aos quais você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="82bad-250">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="82bad-251">Veja a seguir um exemplo de resposta para a solicitação de simulação (supondo que o servidor permita a solicitação):</span><span class="sxs-lookup"><span data-stu-id="82bad-251">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="82bad-252">A resposta inclui um cabeçalho `Access-Control-Allow-Methods` que lista os métodos permitidos e, opcionalmente, um cabeçalho `Access-Control-Allow-Headers`, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="82bad-252">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="82bad-253">Se a solicitação de simulação for realizada com sucesso, o navegador enviará a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="82bad-253">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="82bad-254">Se a solicitação de simulação for negada, o aplicativo retornará uma resposta *200 OK* , mas não enviará os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="82bad-254">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="82bad-255">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-255">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="82bad-256">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="82bad-256">Set the preflight expiration time</span></span>

<span data-ttu-id="82bad-257">O cabeçalho `Access-Control-Max-Age` especifica por quanto tempo a resposta à solicitação de simulação pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="82bad-257">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="82bad-258">Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="82bad-258">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="82bad-259">Como o CORS funciona</span><span class="sxs-lookup"><span data-stu-id="82bad-259">How CORS works</span></span>

<span data-ttu-id="82bad-260">Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) no nível das mensagens http.</span><span class="sxs-lookup"><span data-stu-id="82bad-260">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="82bad-261">O CORS **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="82bad-261">CORS is **not** a security feature.</span></span> <span data-ttu-id="82bad-262">O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="82bad-262">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="82bad-263">Por exemplo, um ator mal-intencionado poderia usar a [prevenção de scripts entre sites (XSS)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites para o site habilitado para CORS para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="82bad-263">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="82bad-264">Sua API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="82bad-264">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="82bad-265">Cabe ao cliente (navegador) impor o CORS.</span><span class="sxs-lookup"><span data-stu-id="82bad-265">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="82bad-266">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="82bad-266">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="82bad-267">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="82bad-267">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="82bad-268">Fiddler</span><span class="sxs-lookup"><span data-stu-id="82bad-268">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="82bad-269">Postman</span><span class="sxs-lookup"><span data-stu-id="82bad-269">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="82bad-270">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="82bad-270">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="82bad-271">Um navegador da Web digitando a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="82bad-271">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="82bad-272">É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.</span><span class="sxs-lookup"><span data-stu-id="82bad-272">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="82bad-273">Os navegadores (sem CORS) não podem fazer solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-273">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="82bad-274">Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="82bad-274">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="82bad-275">JSONP não usa XHR, ele usa a marca `<script>` para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="82bad-275">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="82bad-276">Os scripts podem ser carregados entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-276">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="82bad-277">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-277">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="82bad-278">Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-278">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="82bad-279">O código JavaScript personalizado não é necessário para habilitar o CORS.</span><span class="sxs-lookup"><span data-stu-id="82bad-279">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="82bad-280">Veja a seguir um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="82bad-280">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="82bad-281">O cabeçalho `Origin` fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="82bad-281">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="82bad-282">O cabeçalho `Origin` é necessário e deve ser diferente do host.</span><span class="sxs-lookup"><span data-stu-id="82bad-282">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="82bad-283">Se o servidor permitir a solicitação, ele definirá o cabeçalho `Access-Control-Allow-Origin` na resposta.</span><span class="sxs-lookup"><span data-stu-id="82bad-283">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="82bad-284">O valor desse cabeçalho corresponde ao cabeçalho `Origin` da solicitação ou é o valor curinga `"*"`, o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="82bad-284">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="82bad-285">Se a resposta não incluir o cabeçalho `Access-Control-Allow-Origin`, a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="82bad-285">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="82bad-286">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="82bad-286">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="82bad-287">Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="82bad-287">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="82bad-288">Testar CORS</span><span class="sxs-lookup"><span data-stu-id="82bad-288">Test CORS</span></span>

<span data-ttu-id="82bad-289">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="82bad-289">To test CORS:</span></span>

1. <span data-ttu-id="82bad-290">[Criar um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="82bad-290">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="82bad-291">Como alternativa, você pode [baixar o exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="82bad-291">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="82bad-292">Habilite o CORS usando uma das abordagens deste documento.</span><span class="sxs-lookup"><span data-stu-id="82bad-292">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="82bad-293">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="82bad-293">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="82bad-294">`WithOrigins("https://localhost:<port>");` só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="82bad-294">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="82bad-295">Criar um projeto de aplicativo Web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="82bad-295">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="82bad-296">O exemplo usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="82bad-296">The sample uses Razor Pages.</span></span> <span data-ttu-id="82bad-297">Você pode criar o aplicativo Web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="82bad-297">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="82bad-298">Adicione o seguinte código realçado ao arquivo *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="82bad-298">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="82bad-299">No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` pela URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="82bad-299">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="82bad-300">Implante o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="82bad-300">Deploy the API project.</span></span> <span data-ttu-id="82bad-301">Por exemplo, [implante no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="82bad-301">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="82bad-302">Execute o Razor Pages ou o aplicativo MVC na área de trabalho e clique no botão **testar** .</span><span class="sxs-lookup"><span data-stu-id="82bad-302">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="82bad-303">Use as ferramentas F12 para examinar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="82bad-303">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="82bad-304">Remova a origem do localhost de `WithOrigins` e implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="82bad-304">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="82bad-305">Como alternativa, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="82bad-305">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="82bad-306">Por exemplo, execute do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="82bad-306">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="82bad-307">Teste com o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="82bad-307">Test with the client app.</span></span> <span data-ttu-id="82bad-308">As falhas de CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="82bad-308">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="82bad-309">Use a guia Console nas Ferramentas F12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="82bad-309">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="82bad-310">Dependendo do navegador, você receberá um erro (no console de ferramentas F12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="82bad-310">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="82bad-311">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="82bad-311">Using Microsoft Edge:</span></span>

     <span data-ttu-id="82bad-312">**SEC7120: [CORS] a origem `https://localhost:44375` não encontrou `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para o recurso entre origens em `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="82bad-312">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="82bad-313">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="82bad-313">Using Chrome:</span></span>

     <span data-ttu-id="82bad-314">**O acesso a XMLHttpRequest na `https://webapi.azurewebsites.net/api/values/1` da origem `https://localhost:44375` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="82bad-314">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="82bad-315">Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="82bad-315">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="82bad-316">Ao usar uma ferramenta, a origem da solicitação especificada pelo cabeçalho `Origin` deve ser diferente do host que está recebendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="82bad-316">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="82bad-317">Se a solicitação não for de *origem cruzada* com base no valor do cabeçalho `Origin`:</span><span class="sxs-lookup"><span data-stu-id="82bad-317">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="82bad-318">Não há necessidade de middleware de CORS para processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="82bad-318">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="82bad-319">Cabeçalhos CORS não são retornados na resposta.</span><span class="sxs-lookup"><span data-stu-id="82bad-319">CORS headers aren't returned in the response.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82bad-320">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="82bad-320">Additional resources</span></span>

* [<span data-ttu-id="82bad-321">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="82bad-321">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
