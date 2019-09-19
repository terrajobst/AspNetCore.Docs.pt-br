---
title: Habilitar as solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como o CORS como um padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: a34b77ad799a00707048c923b82b48774ce91682
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108068"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="c3f2e-103">Habilitar as solicitações entre origens (CORS) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3f2e-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="c3f2e-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c3f2e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c3f2e-105">Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="c3f2e-106">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="c3f2e-107">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c3f2e-108">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c3f2e-109">Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="c3f2e-110">Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="c3f2e-111">[Compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="c3f2e-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="c3f2e-112">É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="c3f2e-113">**Não** é um recurso de segurança, o CORS libera a segurança.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="c3f2e-114">Uma API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="c3f2e-115">Para obter mais informações, consulte [como o CORS funciona](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="c3f2e-116">Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="c3f2e-117">É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="c3f2e-118">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3f2e-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="c3f2e-119">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="c3f2e-119">Same origin</span></span>

<span data-ttu-id="c3f2e-120">Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="c3f2e-121">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="c3f2e-122">Essas URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="c3f2e-123">`https://example.net`&ndash; Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="c3f2e-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="c3f2e-124">`https://www.example.com/foo.html`&ndash; Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="c3f2e-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="c3f2e-125">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="c3f2e-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="c3f2e-126">`https://example.com:9000/foo.html`&ndash; Porta diferente</span><span class="sxs-lookup"><span data-stu-id="c3f2e-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="c3f2e-127">O Internet Explorer não considera a porta ao comparar origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="c3f2e-128">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="c3f2e-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="c3f2e-129">O middleware CORS lida com solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="c3f2e-130">O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="c3f2e-131">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-131">The preceding code:</span></span>

* <span data-ttu-id="c3f2e-132">Define o nome da política como\_"myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="c3f2e-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="c3f2e-133">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="c3f2e-134">Chama o <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensão, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="c3f2e-135">Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="c3f2e-136">O lambda pega um <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="c3f2e-137">[Opções de configuração](#cors-policy-options), `WithOrigins`como, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="c3f2e-138">A <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada de método adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="c3f2e-139">Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="c3f2e-140">O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode encadear métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="c3f2e-141">Observação: A URL **não** deve conter uma barra à direita (`/`).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="c3f2e-142">Se a URL for encerrada `/`com, a comparação `false` retornará e nenhum cabeçalho será retornado.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c3f2e-143">O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-143">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
> <span data-ttu-id="c3f2e-144">Com o roteamento de ponto de extremidade, o middleware CORS deve ser configurado para ser executado `UseRouting` entre `UseEndpoints`as chamadas para e.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-144">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="c3f2e-145">A configuração incorreta fará com que o middleware pare de funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-145">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="c3f2e-146">O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-146">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="c3f2e-147">Observação: `UseCors` deve ser chamado antes `UseMvc`de.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-147">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="c3f2e-148">Consulte [habilitar CORS em Razor pages, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-148">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="c3f2e-149">Consulte [testar CORS](#test) para obter instruções sobre como testar o código anterior.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-149">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="c3f2e-150">Habilitar CORS com roteamento de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="c3f2e-150">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="c3f2e-151">Com o roteamento de ponto de extremidade, o CORS pode ser habilitado em uma base `RequireCors` por ponto de extremidade usando o conjunto de métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-151">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="c3f2e-152">Da mesma forma, o CORS também pode ser habilitado para todos os controladores:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-152">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="c3f2e-153">Habilitar CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="c3f2e-153">Enable CORS with attributes</span></span>

<span data-ttu-id="c3f2e-154">[ O&lbrack;atributoEnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-154">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="c3f2e-155">O `[EnableCors]` atributo habilita o CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-155">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="c3f2e-156">Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-156">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="c3f2e-157">O `[EnableCors]` atributo pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-157">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="c3f2e-158">Página Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="c3f2e-158">Razor Page `PageModel`</span></span>
* <span data-ttu-id="c3f2e-159">Controlador</span><span class="sxs-lookup"><span data-stu-id="c3f2e-159">Controller</span></span>
* <span data-ttu-id="c3f2e-160">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="c3f2e-160">Controller action method</span></span>

<span data-ttu-id="c3f2e-161">Você pode aplicar políticas diferentes ao controlador/página-modelo/ação com o `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-161">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="c3f2e-162">Quando o `[EnableCors]` atributo é aplicado a um método de controladores/página-modelo/ação, e o CORS é habilitado no middleware, ambas as políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-162">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="c3f2e-163">Recomendamos a combinação de políticas.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-163">We recommend against combining policies.</span></span> <span data-ttu-id="c3f2e-164">Use o `[EnableCors]` atributo ou middleware, não ambos no mesmo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-164">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="c3f2e-165">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-165">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="c3f2e-166">O código a seguir cria uma política padrão CORS e uma política `"AnotherPolicy"`chamada:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-166">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="c3f2e-167">Desabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="c3f2e-167">Disable CORS</span></span>

<span data-ttu-id="c3f2e-168">[ O&lbrack;atributoDisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) desabilita o CORS para o controlador/página-modelo/ação.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-168">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="c3f2e-169">Opções de política de CORS</span><span class="sxs-lookup"><span data-stu-id="c3f2e-169">CORS policy options</span></span>

<span data-ttu-id="c3f2e-170">Esta seção descreve as várias opções que podem ser definidas em uma política CORS:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-170">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="c3f2e-171">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="c3f2e-171">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="c3f2e-172">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="c3f2e-172">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="c3f2e-173">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="c3f2e-173">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="c3f2e-174">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="c3f2e-174">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="c3f2e-175">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="c3f2e-175">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="c3f2e-176">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="c3f2e-176">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="c3f2e-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>é chamado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c3f2e-178">Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-178">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c3f2e-179">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="c3f2e-179">Set the allowed origins</span></span>

<span data-ttu-id="c3f2e-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>Permite solicitações CORS de todas as origens com qualquer esquema`http` ( `https`ou). &ndash;</span><span class="sxs-lookup"><span data-stu-id="c3f2e-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="c3f2e-181">`AllowAnyOrigin`o não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-181">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="c3f2e-182">Especificar `AllowAnyOrigin` e`AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-182">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c3f2e-183">O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-183">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="c3f2e-184">Especificar `AllowAnyOrigin` e`AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-184">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c3f2e-185">Para um aplicativo seguro, especifique uma lista exata de origens se o cliente precisar se autorizar para acessar os recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-185">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="c3f2e-186">`AllowAnyOrigin`afeta as solicitações de simulação e `Access-Control-Allow-Origin` o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-186">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="c3f2e-187">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="c3f2e-187">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c3f2e-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Define a<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriedade da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c3f2e-189">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="c3f2e-189">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c3f2e-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="c3f2e-191">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-191">Allows any HTTP method:</span></span>
* <span data-ttu-id="c3f2e-192">Afeta as solicitações de simulação e `Access-Control-Allow-Methods` o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-192">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="c3f2e-193">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="c3f2e-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c3f2e-194">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="c3f2e-194">Set the allowed request headers</span></span>

<span data-ttu-id="c3f2e-195">Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada *criar cabeçalhos de solicitação*de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> autor, chame e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-195">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c3f2e-196">Para permitir todos os cabeçalhos de solicitação de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>autor, chame:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-196">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c3f2e-197">Essa configuração afeta as solicitações de simulação e `Access-Control-Request-Headers` o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-197">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="c3f2e-198">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="c3f2e-198">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c3f2e-199">Uma política de middleware CORS correspondente a cabeçalhos específicos especificados por `WithHeaders` é possível somente quando os cabeçalhos `Access-Control-Request-Headers` enviados exatamente correspondem aos cabeçalhos indicados em `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-199">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="c3f2e-200">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-200">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="c3f2e-201">O middleware CORS recusa uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` ([headernames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado `WithHeaders`em:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-201">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="c3f2e-202">O aplicativo retorna uma resposta de *200 OK* , mas não envia os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-202">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c3f2e-203">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-203">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c3f2e-204">O middleware CORS sempre permite que quatro cabeçalhos no `Access-Control-Request-Headers` sejam enviados, independentemente dos valores configurados em CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-204">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="c3f2e-205">Essa lista de cabeçalhos inclui:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-205">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="c3f2e-206">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-206">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="c3f2e-207">O middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação `Content-Language` porque sempre está na lista de permissões:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-207">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="c3f2e-208">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="c3f2e-208">Set the exposed response headers</span></span>

<span data-ttu-id="c3f2e-209">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-209">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="c3f2e-210">Para obter mais informações, [consulte compartilhamento de recursos entre origens do W3C (terminologia): Cabeçalho](https://www.w3.org/TR/cors/#simple-response-header)de resposta simples.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-210">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="c3f2e-211">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-211">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="c3f2e-212">A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-212">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="c3f2e-213">Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-213">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="c3f2e-214">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="c3f2e-214">Credentials in cross-origin requests</span></span>

<span data-ttu-id="c3f2e-215">As credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-215">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c3f2e-216">Por padrão, o navegador não envia credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-216">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="c3f2e-217">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-217">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="c3f2e-218">Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como. `true`</span><span class="sxs-lookup"><span data-stu-id="c3f2e-218">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="c3f2e-219">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-219">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="c3f2e-220">Usando o jQuery:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-220">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="c3f2e-221">Usando a [API de busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="c3f2e-221">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="c3f2e-222">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-222">The server must allow the credentials.</span></span> <span data-ttu-id="c3f2e-223">Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-223">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="c3f2e-224">A resposta http inclui um `Access-Control-Allow-Credentials` cabeçalho, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-224">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c3f2e-225">Se o navegador enviar credenciais, mas a resposta não incluir um `Access-Control-Allow-Credentials` cabeçalho válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-225">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="c3f2e-226">Permitir credenciais entre origens é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-226">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="c3f2e-227">Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-227">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="c3f2e-228">A especificação CORS também indica que a definição de `"*"` origens para (todas as origens) é `Access-Control-Allow-Credentials` inválida se o cabeçalho estiver presente.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-228">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="c3f2e-229">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="c3f2e-229">Preflight requests</span></span>

<span data-ttu-id="c3f2e-230">Para algumas solicitações de CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-230">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="c3f2e-231">Essa solicitação é chamada de uma *solicitação de simulação*.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-231">This request is called a *preflight request*.</span></span> <span data-ttu-id="c3f2e-232">O navegador poderá ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-232">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="c3f2e-233">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-233">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="c3f2e-234">O aplicativo não define cabeçalhos de solicitação diferentes `Accept`de `Accept-Language`, `Content-Language`, `Content-Type`, ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-234">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="c3f2e-235">O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-235">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="c3f2e-236">A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente se aplica aos cabeçalhos que o aplicativo `setRequestHeader` define chamando `XMLHttpRequest` no objeto.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-236">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="c3f2e-237">A especificação CORS chama esses cabeçalhos de *solicitação de autor*de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-237">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="c3f2e-238">A regra não se aplica aos cabeçalhos que o navegador pode definir, `User-Agent`como `Host`, ou `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-238">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="c3f2e-239">Veja a seguir um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-239">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="c3f2e-240">A solicitação de simulação usa o método de opções HTTP.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-240">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="c3f2e-241">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-241">It includes two special headers:</span></span>

* <span data-ttu-id="c3f2e-242">`Access-Control-Request-Method`: O método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-242">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="c3f2e-243">`Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-243">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="c3f2e-244">Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-244">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="c3f2e-245">Uma solicitação de simulação de CORS pode incluir `Access-Control-Request-Headers` um cabeçalho, que indica ao servidor os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-245">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="c3f2e-246">Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-246">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c3f2e-247">Para permitir todos os cabeçalhos de solicitação de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>autor, chame:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-247">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c3f2e-248">Os navegadores não são totalmente consistentes `Access-Control-Request-Headers`em como eles são definidos.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-248">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="c3f2e-249">Se você definir cabeçalhos para algo diferente de `"*"` (ou usar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), deverá incluir pelo menos `Accept`, `Content-Type`e `Origin`, além de todos os cabeçalhos personalizados aos quais deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-249">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="c3f2e-250">Veja a seguir um exemplo de resposta para a solicitação de simulação (supondo que o servidor permita a solicitação):</span><span class="sxs-lookup"><span data-stu-id="c3f2e-250">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="c3f2e-251">A resposta inclui um `Access-Control-Allow-Methods` cabeçalho que lista os métodos permitidos e, opcionalmente `Access-Control-Allow-Headers` , um cabeçalho, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-251">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="c3f2e-252">Se a solicitação de simulação for realizada com sucesso, o navegador enviará a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-252">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="c3f2e-253">Se a solicitação de simulação for negada, o aplicativo retornará uma resposta *200 OK* , mas não enviará os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-253">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c3f2e-254">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-254">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="c3f2e-255">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="c3f2e-255">Set the preflight expiration time</span></span>

<span data-ttu-id="c3f2e-256">O `Access-Control-Max-Age` cabeçalho especifica quanto tempo a resposta para a solicitação de simulação pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-256">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="c3f2e-257">Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-257">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="c3f2e-258">Como o CORS funciona</span><span class="sxs-lookup"><span data-stu-id="c3f2e-258">How CORS works</span></span>

<span data-ttu-id="c3f2e-259">Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) no nível das mensagens http.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-259">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="c3f2e-260">O CORS **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-260">CORS is **not** a security feature.</span></span> <span data-ttu-id="c3f2e-261">O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-261">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="c3f2e-262">Por exemplo, um ator mal-intencionado poderia usar a [prevenção de scripts entre sites (XSS)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites para o site habilitado para CORS para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-262">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="c3f2e-263">Sua API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-263">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="c3f2e-264">Cabe ao cliente (navegador) impor o CORS.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-264">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="c3f2e-265">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-265">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="c3f2e-266">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-266">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="c3f2e-267">Fiddler</span><span class="sxs-lookup"><span data-stu-id="c3f2e-267">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="c3f2e-268">Postman</span><span class="sxs-lookup"><span data-stu-id="c3f2e-268">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="c3f2e-269">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="c3f2e-269">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="c3f2e-270">Um navegador da Web digitando a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-270">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="c3f2e-271">É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-271">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="c3f2e-272">Os navegadores (sem CORS) não podem fazer solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-272">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="c3f2e-273">Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-273">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="c3f2e-274">JSONP não usa XHR, ele usa a `<script>` marca para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-274">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="c3f2e-275">Os scripts podem ser carregados entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-275">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="c3f2e-276">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-276">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c3f2e-277">Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-277">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="c3f2e-278">O código JavaScript personalizado não é necessário para habilitar o CORS.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-278">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="c3f2e-279">Veja a seguir um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-279">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="c3f2e-280">O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-280">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="c3f2e-281">Se o servidor permitir a solicitação, ele definirá `Access-Control-Allow-Origin` o cabeçalho na resposta.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-281">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="c3f2e-282">O valor desse cabeçalho corresponde ao `Origin` cabeçalho da solicitação ou é o valor `"*"`curinga, o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-282">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="c3f2e-283">Se a resposta não incluir o `Access-Control-Allow-Origin` cabeçalho, a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-283">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="c3f2e-284">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-284">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c3f2e-285">Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-285">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="c3f2e-286">Testar CORS</span><span class="sxs-lookup"><span data-stu-id="c3f2e-286">Test CORS</span></span>

<span data-ttu-id="c3f2e-287">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-287">To test CORS:</span></span>

1. <span data-ttu-id="c3f2e-288">[Criar um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-288">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="c3f2e-289">Como alternativa, você pode [baixar o exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-289">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="c3f2e-290">Habilite o CORS usando uma das abordagens deste documento.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-290">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="c3f2e-291">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-291">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="c3f2e-292">`WithOrigins("https://localhost:<port>");`Só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-292">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="c3f2e-293">Criar um projeto de aplicativo Web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-293">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="c3f2e-294">O exemplo usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-294">The sample uses Razor Pages.</span></span> <span data-ttu-id="c3f2e-295">Você pode criar o aplicativo Web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-295">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="c3f2e-296">Adicione o seguinte código realçado ao arquivo *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c3f2e-296">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="c3f2e-297">No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` pela URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-297">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="c3f2e-298">Implante o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-298">Deploy the API project.</span></span> <span data-ttu-id="c3f2e-299">Por exemplo, [implante no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="c3f2e-299">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="c3f2e-300">Execute o Razor Pages ou o aplicativo MVC na área de trabalho e clique no botão **testar** .</span><span class="sxs-lookup"><span data-stu-id="c3f2e-300">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="c3f2e-301">Use as ferramentas F12 para examinar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-301">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="c3f2e-302">Remova a origem do localhost `WithOrigins` de e implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-302">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="c3f2e-303">Como alternativa, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-303">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="c3f2e-304">Por exemplo, execute do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-304">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="c3f2e-305">Teste com o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-305">Test with the client app.</span></span> <span data-ttu-id="c3f2e-306">As falhas de CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-306">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="c3f2e-307">Use a guia Console nas Ferramentas F12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="c3f2e-307">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="c3f2e-308">Dependendo do navegador, você receberá um erro (no console de ferramentas F12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-308">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="c3f2e-309">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-309">Using Microsoft Edge:</span></span>

     <span data-ttu-id="c3f2e-310">**SEC7120: [CORS] a origem `https://localhost:44375` não foi encontrada `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para o recurso entre origens em`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="c3f2e-310">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="c3f2e-311">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="c3f2e-311">Using Chrome:</span></span>

     <span data-ttu-id="c3f2e-312">**O acesso a XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` de origem `https://localhost:44375` foi bloqueado pela política CORS: Nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="c3f2e-312">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3f2e-313">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c3f2e-313">Additional resources</span></span>

* [<span data-ttu-id="c3f2e-314">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="c3f2e-314">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
