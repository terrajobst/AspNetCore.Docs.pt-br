---
title: Habilitar as solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como o CORS como um padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 09cc296ebf605907371619124cac00883beb6abb
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511568"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="0c568-103">Habilitar as solicitações entre origens (CORS) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c568-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c568-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0c568-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0c568-105">Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c568-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="0c568-106">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="0c568-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="0c568-107">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="0c568-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="0c568-108">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="0c568-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="0c568-109">Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="0c568-110">Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="0c568-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="0c568-111">CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="0c568-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="0c568-112">É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="0c568-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="0c568-113">**Não** é um recurso de segurança, o CORS libera a segurança.</span><span class="sxs-lookup"><span data-stu-id="0c568-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="0c568-114">Uma API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="0c568-115">Para obter mais informações, consulte [como o CORS funciona](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="0c568-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="0c568-116">Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="0c568-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="0c568-117">É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="0c568-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="0c568-118">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0c568-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="0c568-119">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="0c568-119">Same origin</span></span>

<span data-ttu-id="0c568-120">Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="0c568-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="0c568-121">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="0c568-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="0c568-122">Essas URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="0c568-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="0c568-123">`https://example.net` &ndash; domínio diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="0c568-124">`https://www.example.com/foo.html` &ndash; subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="0c568-125">`http://example.com/foo.html` &ndash; esquema diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="0c568-126">`https://example.com:9000/foo.html` &ndash; porta diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="0c568-127">O Internet Explorer não considera a porta ao comparar origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="0c568-128">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="0c568-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="0c568-129">O middleware CORS lida com solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="0c568-130">O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="0c568-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="0c568-131">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="0c568-131">The preceding code:</span></span>

* <span data-ttu-id="0c568-132">Define o nome da política como "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="0c568-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="0c568-133">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="0c568-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="0c568-134">Chama o método de extensão <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="0c568-135">Chama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="0c568-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0c568-136">O lambda usa um objeto <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0c568-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="0c568-137">[Opções de configuração](#cors-policy-options), como `WithOrigins`, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="0c568-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="0c568-138">A chamada do método <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0c568-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="0c568-139">Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.</span><span class="sxs-lookup"><span data-stu-id="0c568-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="0c568-140">O método <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> pode encadear métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0c568-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="0c568-141">Observação: a URL **não** deve conter uma barra à direita (`/`).</span><span class="sxs-lookup"><span data-stu-id="0c568-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="0c568-142">Se a URL for encerrada com `/`, a comparação retornará `false` e nenhum cabeçalho será retornado.</span><span class="sxs-lookup"><span data-stu-id="0c568-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="0c568-143">Aplicar políticas CORS a todos os pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="0c568-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="0c568-144">O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="0c568-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code omitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code omitted.
}
```

> [!WARNING]
> <span data-ttu-id="0c568-145">Com o roteamento de ponto de extremidade, o middleware CORS deve ser configurado para ser executado entre as chamadas para `UseRouting` e `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="0c568-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="0c568-146">A configuração incorreta fará com que o middleware pare de funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="0c568-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

<span data-ttu-id="0c568-147">Consulte [habilitar CORS em Razor pages, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="0c568-147">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="0c568-148">Consulte [testar CORS](#test) para obter instruções sobre como testar o código anterior.</span><span class="sxs-lookup"><span data-stu-id="0c568-148">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="0c568-149">Habilitar CORS com roteamento de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="0c568-149">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="0c568-150">Com o roteamento de ponto de extremidade, o CORS pode ser habilitado em uma base por ponto de extremidade usando o conjunto de `RequireCors` de métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="0c568-150">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="0c568-151">Da mesma forma, o CORS também pode ser habilitado para todos os controladores:</span><span class="sxs-lookup"><span data-stu-id="0c568-151">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="0c568-152">Habilitar CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="0c568-152">Enable CORS with attributes</span></span>

<span data-ttu-id="0c568-153">O atributo [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="0c568-153">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="0c568-154">O atributo `[EnableCors]` habilita CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="0c568-154">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="0c568-155">Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.</span><span class="sxs-lookup"><span data-stu-id="0c568-155">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="0c568-156">O atributo `[EnableCors]` pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="0c568-156">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="0c568-157">`PageModel` de página Razor</span><span class="sxs-lookup"><span data-stu-id="0c568-157">Razor Page `PageModel`</span></span>
* <span data-ttu-id="0c568-158">Controlador</span><span class="sxs-lookup"><span data-stu-id="0c568-158">Controller</span></span>
* <span data-ttu-id="0c568-159">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="0c568-159">Controller action method</span></span>

<span data-ttu-id="0c568-160">Você pode aplicar políticas diferentes ao controlador/página-modelo/ação com o atributo `[EnableCors]`.</span><span class="sxs-lookup"><span data-stu-id="0c568-160">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="0c568-161">Quando o atributo `[EnableCors]` é aplicado a um método de controladores/página-modelo/ação, e o CORS é habilitado no middleware, ambas as políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="0c568-161">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="0c568-162">Recomendamos a combinação de políticas.</span><span class="sxs-lookup"><span data-stu-id="0c568-162">We recommend against combining policies.</span></span> <span data-ttu-id="0c568-163">Use o atributo `[EnableCors]` ou o middleware, não ambos no mesmo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-163">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="0c568-164">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="0c568-164">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="0c568-165">O código a seguir cria uma política padrão CORS e uma política chamada `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="0c568-165">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="0c568-166">Desabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="0c568-166">Disable CORS</span></span>

<span data-ttu-id="0c568-167">O atributo [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) DESABILITA o CORS para o controlador/página-modelo/ação.</span><span class="sxs-lookup"><span data-stu-id="0c568-167">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="0c568-168">Opções de política de CORS</span><span class="sxs-lookup"><span data-stu-id="0c568-168">CORS policy options</span></span>

<span data-ttu-id="0c568-169">Esta seção descreve as várias opções que podem ser definidas em uma política CORS:</span><span class="sxs-lookup"><span data-stu-id="0c568-169">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="0c568-170">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="0c568-170">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="0c568-171">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-171">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="0c568-172">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-172">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="0c568-173">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="0c568-173">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="0c568-174">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="0c568-174">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="0c568-175">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="0c568-175">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="0c568-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> é chamado no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0c568-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c568-177">Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="0c568-177">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="0c568-178">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="0c568-178">Set the allowed origins</span></span>

<span data-ttu-id="0c568-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; permite solicitações CORS de todas as origens com qualquer esquema (`http` ou `https`).</span><span class="sxs-lookup"><span data-stu-id="0c568-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="0c568-180">`AllowAnyOrigin` não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-180">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0c568-181">Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="0c568-181">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="0c568-182">O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.</span><span class="sxs-lookup"><span data-stu-id="0c568-182">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="0c568-183">`AllowAnyOrigin` afeta as solicitações de simulação e o cabeçalho de `Access-Control-Allow-Origin`.</span><span class="sxs-lookup"><span data-stu-id="0c568-183">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="0c568-184">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0c568-184">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0c568-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; define a propriedade <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="0c568-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="0c568-186">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-186">Set the allowed HTTP methods</span></span>

<span data-ttu-id="0c568-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="0c568-188">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="0c568-188">Allows any HTTP method:</span></span>
* <span data-ttu-id="0c568-189">Afeta as solicitações de simulação e o cabeçalho de `Access-Control-Allow-Methods`.</span><span class="sxs-lookup"><span data-stu-id="0c568-189">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="0c568-190">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0c568-190">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="0c568-191">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-191">Set the allowed request headers</span></span>

<span data-ttu-id="0c568-192">Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada de *cabeçalhos de solicitação de autor*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="0c568-192">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0c568-193">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-193">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0c568-194">Essa configuração afeta as solicitações de simulação e o cabeçalho `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="0c568-194">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="0c568-195">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0c568-195">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>


<span data-ttu-id="0c568-196">Uma política de middleware CORS correspondente a cabeçalhos específicos especificados por `WithHeaders` só é possível quando os cabeçalhos enviados em `Access-Control-Request-Headers` correspondem exatamente aos cabeçalhos indicados no `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="0c568-196">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="0c568-197">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0c568-197">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="0c568-198">O middleware CORS recusa uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` ([headernames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado em `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="0c568-198">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="0c568-199">O aplicativo retorna uma resposta de *200 OK* , mas não envia os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="0c568-199">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="0c568-200">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-200">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="0c568-201">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="0c568-201">Set the exposed response headers</span></span>

<span data-ttu-id="0c568-202">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-202">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="0c568-203">Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="0c568-203">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="0c568-204">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="0c568-204">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="0c568-205">A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="0c568-205">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="0c568-206">Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-206">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="0c568-207">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="0c568-207">Credentials in cross-origin requests</span></span>

<span data-ttu-id="0c568-208">As credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-208">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="0c568-209">Por padrão, o navegador não envia credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-209">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="0c568-210">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c568-210">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="0c568-211">Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true`.</span><span class="sxs-lookup"><span data-stu-id="0c568-211">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="0c568-212">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="0c568-212">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="0c568-213">Usando o jQuery:</span><span class="sxs-lookup"><span data-stu-id="0c568-213">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="0c568-214">Usando a [API de busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="0c568-214">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="0c568-215">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="0c568-215">The server must allow the credentials.</span></span> <span data-ttu-id="0c568-216">Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-216">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="0c568-217">A resposta HTTP inclui um cabeçalho `Access-Control-Allow-Credentials`, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-217">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="0c568-218">Se o navegador enviar credenciais, mas a resposta não incluir um cabeçalho de `Access-Control-Allow-Credentials` válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="0c568-218">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="0c568-219">Permitir credenciais entre origens é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="0c568-219">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="0c568-220">Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="0c568-220">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="0c568-221">A especificação CORS também indica que a definição de origens para `"*"` (todas as origens) é inválida se o cabeçalho `Access-Control-Allow-Credentials` estiver presente.</span><span class="sxs-lookup"><span data-stu-id="0c568-221">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="0c568-222">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="0c568-222">Preflight requests</span></span>

<span data-ttu-id="0c568-223">Para algumas solicitações de CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-223">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="0c568-224">Essa solicitação é chamada de uma *solicitação de simulação*.</span><span class="sxs-lookup"><span data-stu-id="0c568-224">This request is called a *preflight request*.</span></span> <span data-ttu-id="0c568-225">O navegador poderá ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="0c568-225">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="0c568-226">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="0c568-226">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="0c568-227">O aplicativo não define cabeçalhos de solicitação diferentes de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="0c568-227">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="0c568-228">O cabeçalho `Content-Type`, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="0c568-228">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="0c568-229">A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente aplica-se aos cabeçalhos que o aplicativo define ao chamar `setRequestHeader` no objeto `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="0c568-229">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="0c568-230">A especificação CORS chama esses cabeçalhos de *solicitação de autor*de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="0c568-230">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="0c568-231">A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent`, `Host`ou `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="0c568-231">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="0c568-232">Veja a seguir um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="0c568-232">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="0c568-233">A solicitação de simulação usa o método de opções HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c568-233">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="0c568-234">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="0c568-234">It includes two special headers:</span></span>

* <span data-ttu-id="0c568-235">`Access-Control-Request-Method`: o método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-235">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="0c568-236">`Access-Control-Request-Headers`: uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-236">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="0c568-237">Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="0c568-237">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="0c568-238">Uma solicitação de simulação de CORS pode incluir um cabeçalho `Access-Control-Request-Headers`, que indica ao servidor os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-238">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="0c568-239">Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-239">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0c568-240">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-240">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0c568-241">Os navegadores não são totalmente consistentes em como eles definem `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="0c568-241">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="0c568-242">Se você definir cabeçalhos para algo diferente de `"*"` (ou usar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), deverá incluir pelo menos `Accept`, `Content-Type`e `Origin`, além de todos os cabeçalhos personalizados aos quais você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="0c568-242">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="0c568-243">Veja a seguir um exemplo de resposta para a solicitação de simulação (supondo que o servidor permita a solicitação):</span><span class="sxs-lookup"><span data-stu-id="0c568-243">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="0c568-244">A resposta inclui um cabeçalho `Access-Control-Allow-Methods` que lista os métodos permitidos e, opcionalmente, um cabeçalho `Access-Control-Allow-Headers`, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="0c568-244">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="0c568-245">Se a solicitação de simulação for realizada com sucesso, o navegador enviará a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-245">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="0c568-246">Se a solicitação de simulação for negada, o aplicativo retornará uma resposta *200 OK* , mas não enviará os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="0c568-246">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="0c568-247">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-247">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="0c568-248">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="0c568-248">Set the preflight expiration time</span></span>

<span data-ttu-id="0c568-249">O cabeçalho `Access-Control-Max-Age` especifica por quanto tempo a resposta à solicitação de simulação pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="0c568-249">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="0c568-250">Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-250">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="0c568-251">Como o CORS funciona</span><span class="sxs-lookup"><span data-stu-id="0c568-251">How CORS works</span></span>

<span data-ttu-id="0c568-252">Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) no nível das mensagens http.</span><span class="sxs-lookup"><span data-stu-id="0c568-252">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="0c568-253">O CORS **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="0c568-253">CORS is **not** a security feature.</span></span> <span data-ttu-id="0c568-254">O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="0c568-254">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="0c568-255">Por exemplo, um ator mal-intencionado poderia usar a [prevenção de scripts entre sites (XSS)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites para o site habilitado para CORS para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="0c568-255">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="0c568-256">Sua API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-256">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="0c568-257">Cabe ao cliente (navegador) impor o CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-257">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="0c568-258">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-258">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="0c568-259">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="0c568-259">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="0c568-260">Fiddler</span><span class="sxs-lookup"><span data-stu-id="0c568-260">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="0c568-261">Postman</span><span class="sxs-lookup"><span data-stu-id="0c568-261">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="0c568-262">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="0c568-262">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="0c568-263">Um navegador da Web digitando a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="0c568-263">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="0c568-264">É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.</span><span class="sxs-lookup"><span data-stu-id="0c568-264">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="0c568-265">Os navegadores (sem CORS) não podem fazer solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-265">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="0c568-266">Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="0c568-266">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="0c568-267">JSONP não usa XHR, ele usa a marca `<script>` para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-267">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="0c568-268">Os scripts podem ser carregados entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-268">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="0c568-269">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-269">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="0c568-270">Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-270">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="0c568-271">O código JavaScript personalizado não é necessário para habilitar o CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-271">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="0c568-272">Veja a seguir um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-272">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="0c568-273">O cabeçalho `Origin` fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-273">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="0c568-274">O cabeçalho de `Origin` é necessário e deve ser diferente do host.</span><span class="sxs-lookup"><span data-stu-id="0c568-274">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="0c568-275">Se o servidor permitir a solicitação, ele definirá o cabeçalho `Access-Control-Allow-Origin` na resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-275">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="0c568-276">O valor desse cabeçalho corresponde ao cabeçalho de `Origin` da solicitação ou é o valor curinga `"*"`, o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="0c568-276">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="0c568-277">Se a resposta não incluir o cabeçalho `Access-Control-Allow-Origin`, a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="0c568-277">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="0c568-278">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-278">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="0c568-279">Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="0c568-279">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="0c568-280">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="0c568-280">Test CORS</span></span>

<span data-ttu-id="0c568-281">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="0c568-281">To test CORS:</span></span>

1. <span data-ttu-id="0c568-282">[Criar um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="0c568-282">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="0c568-283">Como alternativa, você pode [baixar o exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="0c568-283">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="0c568-284">Habilite o CORS usando uma das abordagens deste documento.</span><span class="sxs-lookup"><span data-stu-id="0c568-284">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="0c568-285">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0c568-285">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="0c568-286">`WithOrigins("https://localhost:<port>");` só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="0c568-286">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="0c568-287">Criar um projeto de aplicativo Web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="0c568-287">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="0c568-288">O exemplo usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0c568-288">The sample uses Razor Pages.</span></span> <span data-ttu-id="0c568-289">Você pode criar o aplicativo Web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="0c568-289">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="0c568-290">Adicione o seguinte código realçado ao arquivo *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0c568-290">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="0c568-291">No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` pela URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="0c568-291">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="0c568-292">Implante o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="0c568-292">Deploy the API project.</span></span> <span data-ttu-id="0c568-293">Por exemplo, [implante no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="0c568-293">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="0c568-294">Execute o Razor Pages ou o aplicativo MVC na área de trabalho e clique no botão **testar** .</span><span class="sxs-lookup"><span data-stu-id="0c568-294">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="0c568-295">Use as ferramentas F12 para examinar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="0c568-295">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="0c568-296">Remova a origem do localhost de `WithOrigins` e implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-296">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="0c568-297">Como alternativa, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="0c568-297">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="0c568-298">Por exemplo, execute do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c568-298">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="0c568-299">Teste com o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="0c568-299">Test with the client app.</span></span> <span data-ttu-id="0c568-300">As falhas de CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c568-300">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="0c568-301">Use a guia Console nas Ferramentas F12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="0c568-301">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="0c568-302">Dependendo do navegador, você receberá um erro (no console de ferramentas F12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="0c568-302">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="0c568-303">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="0c568-303">Using Microsoft Edge:</span></span>

     <span data-ttu-id="0c568-304">**SEC7120: [CORS] a origem `https://localhost:44375` não encontrou `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para recursos entre origens em `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="0c568-304">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="0c568-305">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="0c568-305">Using Chrome:</span></span>

     <span data-ttu-id="0c568-306">**O acesso a XMLHttpRequest em `https://webapi.azurewebsites.net/api/values/1` da origem `https://localhost:44375` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="0c568-306">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="0c568-307">Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="0c568-307">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="0c568-308">Ao usar uma ferramenta, a origem da solicitação especificada pelo cabeçalho de `Origin` deve ser diferente do host que está recebendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-308">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="0c568-309">Se a solicitação não for de *origem cruzada* com base no valor do cabeçalho de `Origin`:</span><span class="sxs-lookup"><span data-stu-id="0c568-309">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="0c568-310">Não há necessidade de middleware de CORS para processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-310">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="0c568-311">Cabeçalhos CORS não são retornados na resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-311">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="0c568-312">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="0c568-312">CORS in IIS</span></span>

<span data-ttu-id="0c568-313">Ao implantar no IIS, o CORS precisará ser executado antes da autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="0c568-313">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="0c568-314">Para dar suporte a esse cenário, o [módulo CORS do IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-314">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c568-315">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0c568-315">Additional resources</span></span>

* [<span data-ttu-id="0c568-316">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="0c568-316">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="0c568-317">Introdução ao módulo CORS do IIS</span><span class="sxs-lookup"><span data-stu-id="0c568-317">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c568-318">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0c568-318">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0c568-319">Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c568-319">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="0c568-320">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="0c568-320">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="0c568-321">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="0c568-321">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="0c568-322">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="0c568-322">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="0c568-323">Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-323">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="0c568-324">Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="0c568-324">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="0c568-325">CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="0c568-325">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="0c568-326">É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="0c568-326">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="0c568-327">**Não** é um recurso de segurança, o CORS libera a segurança.</span><span class="sxs-lookup"><span data-stu-id="0c568-327">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="0c568-328">Uma API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-328">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="0c568-329">Para obter mais informações, consulte [como o CORS funciona](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="0c568-329">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="0c568-330">Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="0c568-330">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="0c568-331">É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="0c568-331">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="0c568-332">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0c568-332">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="0c568-333">Mesma origem</span><span class="sxs-lookup"><span data-stu-id="0c568-333">Same origin</span></span>

<span data-ttu-id="0c568-334">Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="0c568-334">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="0c568-335">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="0c568-335">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="0c568-336">Essas URLs têm origens diferentes das duas URLs anteriores:</span><span class="sxs-lookup"><span data-stu-id="0c568-336">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="0c568-337">`https://example.net` &ndash; domínio diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-337">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="0c568-338">`https://www.example.com/foo.html` &ndash; subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-338">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="0c568-339">`http://example.com/foo.html` &ndash; esquema diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-339">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="0c568-340">`https://example.com:9000/foo.html` &ndash; porta diferente</span><span class="sxs-lookup"><span data-stu-id="0c568-340">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="0c568-341">O Internet Explorer não considera a porta ao comparar origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-341">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="0c568-342">CORS com política nomeada e middleware</span><span class="sxs-lookup"><span data-stu-id="0c568-342">CORS with named policy and middleware</span></span>

<span data-ttu-id="0c568-343">O middleware CORS lida com solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-343">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="0c568-344">O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:</span><span class="sxs-lookup"><span data-stu-id="0c568-344">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="0c568-345">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="0c568-345">The preceding code:</span></span>

* <span data-ttu-id="0c568-346">Define o nome da política como "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="0c568-346">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="0c568-347">O nome da política é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="0c568-347">The policy name is arbitrary.</span></span>
* <span data-ttu-id="0c568-348">Chama o método de extensão <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>, que habilita o CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-348">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="0c568-349">Chama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="0c568-349">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0c568-350">O lambda usa um objeto <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0c568-350">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="0c568-351">[Opções de configuração](#cors-policy-options), como `WithOrigins`, são descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="0c568-351">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="0c568-352">A chamada do método <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> adiciona serviços CORS ao contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0c568-352">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="0c568-353">Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.</span><span class="sxs-lookup"><span data-stu-id="0c568-353">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="0c568-354">O método <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> pode encadear métodos, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0c568-354">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="0c568-355">Observação: a URL **não** deve conter uma barra à direita (`/`).</span><span class="sxs-lookup"><span data-stu-id="0c568-355">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="0c568-356">Se a URL for encerrada com `/`, a comparação retornará `false` e nenhum cabeçalho será retornado.</span><span class="sxs-lookup"><span data-stu-id="0c568-356">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="0c568-357">O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="0c568-357">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="0c568-358">Observação: `UseCors` deve ser chamado antes de `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="0c568-358">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="0c568-359">Consulte [habilitar CORS em Razor pages, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.</span><span class="sxs-lookup"><span data-stu-id="0c568-359">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="0c568-360">Consulte [testar CORS](#test) para obter instruções sobre como testar o código anterior.</span><span class="sxs-lookup"><span data-stu-id="0c568-360">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="0c568-361">Habilitar CORS com atributos</span><span class="sxs-lookup"><span data-stu-id="0c568-361">Enable CORS with attributes</span></span>

<span data-ttu-id="0c568-362">O atributo [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="0c568-362">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="0c568-363">O atributo `[EnableCors]` habilita CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="0c568-363">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="0c568-364">Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.</span><span class="sxs-lookup"><span data-stu-id="0c568-364">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="0c568-365">O atributo `[EnableCors]` pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="0c568-365">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="0c568-366">`PageModel` de página Razor</span><span class="sxs-lookup"><span data-stu-id="0c568-366">Razor Page `PageModel`</span></span>
* <span data-ttu-id="0c568-367">Controlador</span><span class="sxs-lookup"><span data-stu-id="0c568-367">Controller</span></span>
* <span data-ttu-id="0c568-368">Método de ação do controlador</span><span class="sxs-lookup"><span data-stu-id="0c568-368">Controller action method</span></span>

<span data-ttu-id="0c568-369">Você pode aplicar políticas diferentes ao controlador/página-modelo/ação com o atributo `[EnableCors]`.</span><span class="sxs-lookup"><span data-stu-id="0c568-369">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="0c568-370">Quando o atributo `[EnableCors]` é aplicado a um método de controladores/página-modelo/ação, e o CORS é habilitado no middleware, ambas as políticas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="0c568-370">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="0c568-371">Recomendamos a combinação de políticas.</span><span class="sxs-lookup"><span data-stu-id="0c568-371">We recommend against combining policies.</span></span> <span data-ttu-id="0c568-372">Use o atributo `[EnableCors]` ou o middleware, não ambos no mesmo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-372">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="0c568-373">O código a seguir aplica uma política diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="0c568-373">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="0c568-374">O código a seguir cria uma política padrão CORS e uma política chamada `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="0c568-374">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="0c568-375">Desabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="0c568-375">Disable CORS</span></span>

<span data-ttu-id="0c568-376">O atributo [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) DESABILITA o CORS para o controlador/página-modelo/ação.</span><span class="sxs-lookup"><span data-stu-id="0c568-376">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="0c568-377">Opções de política de CORS</span><span class="sxs-lookup"><span data-stu-id="0c568-377">CORS policy options</span></span>

<span data-ttu-id="0c568-378">Esta seção descreve as várias opções que podem ser definidas em uma política CORS:</span><span class="sxs-lookup"><span data-stu-id="0c568-378">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="0c568-379">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="0c568-379">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="0c568-380">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-380">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="0c568-381">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-381">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="0c568-382">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="0c568-382">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="0c568-383">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="0c568-383">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="0c568-384">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="0c568-384">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="0c568-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> é chamado no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0c568-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0c568-386">Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.</span><span class="sxs-lookup"><span data-stu-id="0c568-386">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="0c568-387">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="0c568-387">Set the allowed origins</span></span>

<span data-ttu-id="0c568-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; permite solicitações CORS de todas as origens com qualquer esquema (`http` ou `https`).</span><span class="sxs-lookup"><span data-stu-id="0c568-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="0c568-389">`AllowAnyOrigin` não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-389">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0c568-390">Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites.</span><span class="sxs-lookup"><span data-stu-id="0c568-390">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="0c568-391">Para um aplicativo seguro, especifique uma lista exata de origens se o cliente precisar se autorizar para acessar os recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="0c568-391">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="0c568-392">`AllowAnyOrigin` afeta as solicitações de simulação e o cabeçalho de `Access-Control-Allow-Origin`.</span><span class="sxs-lookup"><span data-stu-id="0c568-392">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="0c568-393">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0c568-393">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0c568-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; define a propriedade <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="0c568-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="0c568-395">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-395">Set the allowed HTTP methods</span></span>

<span data-ttu-id="0c568-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="0c568-397">Permite qualquer método HTTP:</span><span class="sxs-lookup"><span data-stu-id="0c568-397">Allows any HTTP method:</span></span>
* <span data-ttu-id="0c568-398">Afeta as solicitações de simulação e o cabeçalho de `Access-Control-Allow-Methods`.</span><span class="sxs-lookup"><span data-stu-id="0c568-398">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="0c568-399">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0c568-399">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="0c568-400">Definir os cabeçalhos de solicitação permitidos</span><span class="sxs-lookup"><span data-stu-id="0c568-400">Set the allowed request headers</span></span>

<span data-ttu-id="0c568-401">Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada de *cabeçalhos de solicitação de autor*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="0c568-401">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0c568-402">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-402">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0c568-403">Essa configuração afeta as solicitações de simulação e o cabeçalho `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="0c568-403">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="0c568-404">Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0c568-404">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0c568-405">O middleware CORS sempre permite que quatro cabeçalhos na `Access-Control-Request-Headers` sejam enviados, independentemente dos valores configurados em CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="0c568-405">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="0c568-406">Essa lista de cabeçalhos inclui:</span><span class="sxs-lookup"><span data-stu-id="0c568-406">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="0c568-407">Por exemplo, considere um aplicativo configurado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="0c568-407">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="0c568-408">O middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` sempre está na lista de permissões:</span><span class="sxs-lookup"><span data-stu-id="0c568-408">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="0c568-409">Definir os cabeçalhos de resposta expostos</span><span class="sxs-lookup"><span data-stu-id="0c568-409">Set the exposed response headers</span></span>

<span data-ttu-id="0c568-410">Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-410">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="0c568-411">Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="0c568-411">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="0c568-412">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="0c568-412">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="0c568-413">A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="0c568-413">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="0c568-414">Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-414">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="0c568-415">Credenciais em solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="0c568-415">Credentials in cross-origin requests</span></span>

<span data-ttu-id="0c568-416">As credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-416">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="0c568-417">Por padrão, o navegador não envia credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-417">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="0c568-418">As credenciais incluem cookies e esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c568-418">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="0c568-419">Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true`.</span><span class="sxs-lookup"><span data-stu-id="0c568-419">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="0c568-420">Usando `XMLHttpRequest` diretamente:</span><span class="sxs-lookup"><span data-stu-id="0c568-420">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="0c568-421">Usando o jQuery:</span><span class="sxs-lookup"><span data-stu-id="0c568-421">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="0c568-422">Usando a [API de busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="0c568-422">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="0c568-423">O servidor deve permitir as credenciais.</span><span class="sxs-lookup"><span data-stu-id="0c568-423">The server must allow the credentials.</span></span> <span data-ttu-id="0c568-424">Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-424">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="0c568-425">A resposta HTTP inclui um cabeçalho `Access-Control-Allow-Credentials`, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-425">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="0c568-426">Se o navegador enviar credenciais, mas a resposta não incluir um cabeçalho de `Access-Control-Allow-Credentials` válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="0c568-426">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="0c568-427">Permitir credenciais entre origens é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="0c568-427">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="0c568-428">Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="0c568-428">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="0c568-429">A especificação CORS também indica que a definição de origens para `"*"` (todas as origens) é inválida se o cabeçalho `Access-Control-Allow-Credentials` estiver presente.</span><span class="sxs-lookup"><span data-stu-id="0c568-429">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="0c568-430">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="0c568-430">Preflight requests</span></span>

<span data-ttu-id="0c568-431">Para algumas solicitações de CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-431">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="0c568-432">Essa solicitação é chamada de uma *solicitação de simulação*.</span><span class="sxs-lookup"><span data-stu-id="0c568-432">This request is called a *preflight request*.</span></span> <span data-ttu-id="0c568-433">O navegador poderá ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="0c568-433">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="0c568-434">O método de solicitação é GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="0c568-434">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="0c568-435">O aplicativo não define cabeçalhos de solicitação diferentes de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="0c568-435">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="0c568-436">O cabeçalho `Content-Type`, se definido, tem um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="0c568-436">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="0c568-437">A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente aplica-se aos cabeçalhos que o aplicativo define ao chamar `setRequestHeader` no objeto `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="0c568-437">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="0c568-438">A especificação CORS chama esses cabeçalhos de *solicitação de autor*de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="0c568-438">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="0c568-439">A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent`, `Host`ou `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="0c568-439">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="0c568-440">Veja a seguir um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="0c568-440">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="0c568-441">A solicitação de simulação usa o método de opções HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c568-441">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="0c568-442">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="0c568-442">It includes two special headers:</span></span>

* <span data-ttu-id="0c568-443">`Access-Control-Request-Method`: o método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-443">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="0c568-444">`Access-Control-Request-Headers`: uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-444">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="0c568-445">Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="0c568-445">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="0c568-446">Uma solicitação de simulação de CORS pode incluir um cabeçalho `Access-Control-Request-Headers`, que indica ao servidor os cabeçalhos que são enviados com a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-446">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="0c568-447">Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-447">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0c568-448">Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-448">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0c568-449">Os navegadores não são totalmente consistentes em como eles definem `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="0c568-449">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="0c568-450">Se você definir cabeçalhos para algo diferente de `"*"` (ou usar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), deverá incluir pelo menos `Accept`, `Content-Type`e `Origin`, além de todos os cabeçalhos personalizados aos quais você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="0c568-450">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="0c568-451">Veja a seguir um exemplo de resposta para a solicitação de simulação (supondo que o servidor permita a solicitação):</span><span class="sxs-lookup"><span data-stu-id="0c568-451">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="0c568-452">A resposta inclui um cabeçalho `Access-Control-Allow-Methods` que lista os métodos permitidos e, opcionalmente, um cabeçalho `Access-Control-Allow-Headers`, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="0c568-452">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="0c568-453">Se a solicitação de simulação for realizada com sucesso, o navegador enviará a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="0c568-453">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="0c568-454">Se a solicitação de simulação for negada, o aplicativo retornará uma resposta *200 OK* , mas não enviará os cabeçalhos CORS de volta.</span><span class="sxs-lookup"><span data-stu-id="0c568-454">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="0c568-455">Portanto, o navegador não tenta a solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-455">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="0c568-456">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="0c568-456">Set the preflight expiration time</span></span>

<span data-ttu-id="0c568-457">O cabeçalho `Access-Control-Max-Age` especifica por quanto tempo a resposta à solicitação de simulação pode ser armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="0c568-457">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="0c568-458">Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="0c568-458">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="0c568-459">Como o CORS funciona</span><span class="sxs-lookup"><span data-stu-id="0c568-459">How CORS works</span></span>

<span data-ttu-id="0c568-460">Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) no nível das mensagens http.</span><span class="sxs-lookup"><span data-stu-id="0c568-460">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="0c568-461">O CORS **não** é um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="0c568-461">CORS is **not** a security feature.</span></span> <span data-ttu-id="0c568-462">O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="0c568-462">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="0c568-463">Por exemplo, um ator mal-intencionado poderia usar a [prevenção de scripts entre sites (XSS)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites para o site habilitado para CORS para roubar informações.</span><span class="sxs-lookup"><span data-stu-id="0c568-463">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="0c568-464">Sua API não é mais segura, permitindo CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-464">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="0c568-465">Cabe ao cliente (navegador) impor o CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-465">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="0c568-466">O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-466">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="0c568-467">Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:</span><span class="sxs-lookup"><span data-stu-id="0c568-467">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="0c568-468">Fiddler</span><span class="sxs-lookup"><span data-stu-id="0c568-468">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="0c568-469">Postman</span><span class="sxs-lookup"><span data-stu-id="0c568-469">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="0c568-470">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="0c568-470">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="0c568-471">Um navegador da Web digitando a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="0c568-471">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="0c568-472">É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.</span><span class="sxs-lookup"><span data-stu-id="0c568-472">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="0c568-473">Os navegadores (sem CORS) não podem fazer solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-473">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="0c568-474">Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="0c568-474">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="0c568-475">JSONP não usa XHR, ele usa a marca `<script>` para receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-475">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="0c568-476">Os scripts podem ser carregados entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-476">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="0c568-477">A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-477">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="0c568-478">Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-478">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="0c568-479">O código JavaScript personalizado não é necessário para habilitar o CORS.</span><span class="sxs-lookup"><span data-stu-id="0c568-479">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="0c568-480">Veja a seguir um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="0c568-480">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="0c568-481">O cabeçalho `Origin` fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-481">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="0c568-482">O cabeçalho de `Origin` é necessário e deve ser diferente do host.</span><span class="sxs-lookup"><span data-stu-id="0c568-482">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="0c568-483">Se o servidor permitir a solicitação, ele definirá o cabeçalho `Access-Control-Allow-Origin` na resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-483">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="0c568-484">O valor desse cabeçalho corresponde ao cabeçalho de `Origin` da solicitação ou é o valor curinga `"*"`, o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="0c568-484">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="0c568-485">Se a resposta não incluir o cabeçalho `Access-Control-Allow-Origin`, a solicitação entre origens falhará.</span><span class="sxs-lookup"><span data-stu-id="0c568-485">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="0c568-486">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-486">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="0c568-487">Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="0c568-487">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="0c568-488">Testar o CORS</span><span class="sxs-lookup"><span data-stu-id="0c568-488">Test CORS</span></span>

<span data-ttu-id="0c568-489">Para testar o CORS:</span><span class="sxs-lookup"><span data-stu-id="0c568-489">To test CORS:</span></span>

1. <span data-ttu-id="0c568-490">[Criar um projeto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="0c568-490">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="0c568-491">Como alternativa, você pode [baixar o exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="0c568-491">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="0c568-492">Habilite o CORS usando uma das abordagens deste documento.</span><span class="sxs-lookup"><span data-stu-id="0c568-492">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="0c568-493">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0c568-493">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="0c568-494">`WithOrigins("https://localhost:<port>");` só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="0c568-494">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="0c568-495">Criar um projeto de aplicativo Web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="0c568-495">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="0c568-496">O exemplo usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0c568-496">The sample uses Razor Pages.</span></span> <span data-ttu-id="0c568-497">Você pode criar o aplicativo Web na mesma solução que o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="0c568-497">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="0c568-498">Adicione o seguinte código realçado ao arquivo *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0c568-498">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="0c568-499">No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` pela URL para o aplicativo implantado.</span><span class="sxs-lookup"><span data-stu-id="0c568-499">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="0c568-500">Implante o projeto de API.</span><span class="sxs-lookup"><span data-stu-id="0c568-500">Deploy the API project.</span></span> <span data-ttu-id="0c568-501">Por exemplo, [implante no Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="0c568-501">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="0c568-502">Execute o Razor Pages ou o aplicativo MVC na área de trabalho e clique no botão **testar** .</span><span class="sxs-lookup"><span data-stu-id="0c568-502">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="0c568-503">Use as ferramentas F12 para examinar mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="0c568-503">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="0c568-504">Remova a origem do localhost de `WithOrigins` e implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-504">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="0c568-505">Como alternativa, execute o aplicativo cliente com uma porta diferente.</span><span class="sxs-lookup"><span data-stu-id="0c568-505">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="0c568-506">Por exemplo, execute do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c568-506">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="0c568-507">Teste com o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="0c568-507">Test with the client app.</span></span> <span data-ttu-id="0c568-508">As falhas de CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c568-508">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="0c568-509">Use a guia Console nas Ferramentas F12 para ver o erro.</span><span class="sxs-lookup"><span data-stu-id="0c568-509">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="0c568-510">Dependendo do navegador, você receberá um erro (no console de ferramentas F12) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="0c568-510">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="0c568-511">Usando o Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="0c568-511">Using Microsoft Edge:</span></span>

     <span data-ttu-id="0c568-512">**SEC7120: [CORS] a origem `https://localhost:44375` não encontrou `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para recursos entre origens em `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="0c568-512">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="0c568-513">Usando o Chrome:</span><span class="sxs-lookup"><span data-stu-id="0c568-513">Using Chrome:</span></span>

     <span data-ttu-id="0c568-514">**O acesso a XMLHttpRequest em `https://webapi.azurewebsites.net/api/values/1` da origem `https://localhost:44375` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="0c568-514">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="0c568-515">Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="0c568-515">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="0c568-516">Ao usar uma ferramenta, a origem da solicitação especificada pelo cabeçalho de `Origin` deve ser diferente do host que está recebendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-516">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="0c568-517">Se a solicitação não for de *origem cruzada* com base no valor do cabeçalho de `Origin`:</span><span class="sxs-lookup"><span data-stu-id="0c568-517">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="0c568-518">Não há necessidade de middleware de CORS para processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0c568-518">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="0c568-519">Cabeçalhos CORS não são retornados na resposta.</span><span class="sxs-lookup"><span data-stu-id="0c568-519">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="0c568-520">CORS no IIS</span><span class="sxs-lookup"><span data-stu-id="0c568-520">CORS in IIS</span></span>

<span data-ttu-id="0c568-521">Ao implantar no IIS, o CORS precisará ser executado antes da autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="0c568-521">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="0c568-522">Para dar suporte a esse cenário, o [módulo CORS do IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0c568-522">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c568-523">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0c568-523">Additional resources</span></span>

* [<span data-ttu-id="0c568-524">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="0c568-524">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="0c568-525">Introdução ao módulo CORS do IIS</span><span class="sxs-lookup"><span data-stu-id="0c568-525">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end