---
title: Middleware do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o middleware do ASP.NET Core e o pipeline de solicitação.
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: bac121441d6856ca79affe1a3130e5cbc76debd9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882331"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="173a6-103">Middleware do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="173a6-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="173a6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="173a6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="173a6-105">O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas.</span><span class="sxs-lookup"><span data-stu-id="173a6-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="173a6-106">Cada componente:</span><span class="sxs-lookup"><span data-stu-id="173a6-106">Each component:</span></span>

* <span data-ttu-id="173a6-107">Escolhe se deseja passar a solicitação para o próximo componente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="173a6-108">Pode executar o trabalho antes e depois do próximo componente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="173a6-109">Os delegados de solicitação são usados para criar o pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="173a6-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="173a6-110">Os delegados de solicitação manipulam cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="173a6-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="173a6-111">Os delegados de solicitação são configurados usando os métodos de extensão <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> e <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="173a6-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="173a6-112">Um delegado de solicitação individual pode ser especificado em linha como um método anônimo (chamado do middleware em linha) ou pode ser definido em uma classe reutilizável.</span><span class="sxs-lookup"><span data-stu-id="173a6-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="173a6-113">Essas classes reutilizáveis e os métodos anônimos em linha são o *middleware*, também chamado de *componentes do middleware*.</span><span class="sxs-lookup"><span data-stu-id="173a6-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="173a6-114">Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito do pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="173a6-115">Quando um middleware causa um curto-circuito, ele é chamado de *middleware terminal*, porque impede que outros middlewares processem a solicitação.</span><span class="sxs-lookup"><span data-stu-id="173a6-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="173a6-116"><xref:migration/http-modules> explica a diferença entre pipelines de solicitação no ASP.NET Core e no ASP.NET 4.x e fornece mais exemplos do middleware.</span><span class="sxs-lookup"><span data-stu-id="173a6-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="173a6-117">Criar um pipeline do middleware com o IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="173a6-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="173a6-118">O pipeline de solicitação do ASP.NET Core consiste em uma sequência de delegados de solicitação, chamados um após o outro.</span><span class="sxs-lookup"><span data-stu-id="173a6-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="173a6-119">O diagrama a seguir demonstra o conceito.</span><span class="sxs-lookup"><span data-stu-id="173a6-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="173a6-120">O thread de execução segue as setas pretas.</span><span class="sxs-lookup"><span data-stu-id="173a6-120">The thread of execution follows the black arrows.</span></span>

![Padrão de processamento de solicitação mostrando a chegada de uma solicitação, processada por meio de três middlewares e a resposta que sai do aplicativo.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="173a6-124">Cada delegado pode executar operações antes e depois do próximo delegado.</span><span class="sxs-lookup"><span data-stu-id="173a6-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="173a6-125">Os delegados de tratamento de exceção devem ser chamados no início do pipeline para que possam detectar exceções que ocorrem em etapas posteriores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="173a6-126">O aplicativo ASP.NET Core mais simples possível define um delegado de solicitação única que controla todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="173a6-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="173a6-127">Este caso não inclui um pipeline de solicitação real.</span><span class="sxs-lookup"><span data-stu-id="173a6-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="173a6-128">Em vez disso, uma única função anônima é chamada em resposta a cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="173a6-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

<span data-ttu-id="173a6-129">O primeiro delegado <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> encerra o pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="173a6-130">Encadeie vários delegados de solicitação junto com o <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="173a6-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="173a6-131">O parâmetro `next` representa o próximo delegado no pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="173a6-132">Lembre-se de que você pode causar um curto-circuito no pipeline ao *não* chamar o parâmetro *next*.</span><span class="sxs-lookup"><span data-stu-id="173a6-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="173a6-133">Normalmente, você pode executar ações antes e depois do próximo delegado, conforme o exemplo a seguir demonstra:</span><span class="sxs-lookup"><span data-stu-id="173a6-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

<span data-ttu-id="173a6-134">Quando um delegado não transmite uma solicitação ao próximo delegado, considera-se que ele esteja *causando um curto-circuito do pipeline de solicitação*.</span><span class="sxs-lookup"><span data-stu-id="173a6-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="173a6-135">O curto-circuito geralmente é desejável porque ele evita trabalho desnecessário.</span><span class="sxs-lookup"><span data-stu-id="173a6-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="173a6-136">Por exemplo, o [Middleware de Arquivo Estático](xref:fundamentals/static-files) pode agir com um *middleware terminal*, processando uma solicitação para um arquivo estático e causar curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="173a6-137">O middleware adicionado ao pipeline antes do middleware que finaliza o processamento adicional ainda processa o código após as instruções `next.Invoke`.</span><span class="sxs-lookup"><span data-stu-id="173a6-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="173a6-138">No entanto, confira o seguinte aviso sobre a tentativa de gravar em uma resposta que já foi enviada.</span><span class="sxs-lookup"><span data-stu-id="173a6-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="173a6-139">Não chame `next.Invoke` depois que a resposta tiver sido enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="173a6-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="173a6-140">Altera para <xref:Microsoft.AspNetCore.Http.HttpResponse> depois de a resposta ser iniciada e lança uma exceção.</span><span class="sxs-lookup"><span data-stu-id="173a6-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="173a6-141">Por exemplo, mudanças como a configuração de cabeçalhos e o código de status lançam uma exceção.</span><span class="sxs-lookup"><span data-stu-id="173a6-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="173a6-142">Gravar no corpo da resposta após a chamada `next`:</span><span class="sxs-lookup"><span data-stu-id="173a6-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="173a6-143">Pode causar uma violação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="173a6-143">May cause a protocol violation.</span></span> <span data-ttu-id="173a6-144">Por exemplo, gravar mais do que o `Content-Length` indicado.</span><span class="sxs-lookup"><span data-stu-id="173a6-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="173a6-145">Pode corromper o formato do corpo.</span><span class="sxs-lookup"><span data-stu-id="173a6-145">May corrupt the body format.</span></span> <span data-ttu-id="173a6-146">Por exemplo, gravar um rodapé HTML em um arquivo CSS.</span><span class="sxs-lookup"><span data-stu-id="173a6-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="173a6-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> é uma dica útil para indicar se os cabeçalhos foram enviados ou o corpo foi gravado.</span><span class="sxs-lookup"><span data-stu-id="173a6-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="173a6-148">Pedido</span><span class="sxs-lookup"><span data-stu-id="173a6-148">Order</span></span>

<span data-ttu-id="173a6-149">A ordem em que os componentes do middleware são adicionados ao método `Startup.Configure` define a ordem em que os componentes de middleware são invocados nas solicitações e a ordem inversa para a resposta.</span><span class="sxs-lookup"><span data-stu-id="173a6-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="173a6-150">A ordem é crítica para a segurança, o desempenho e a funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="173a6-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="173a6-151">O método `Startup.Configure` a seguir adiciona componentes de middleware para cenários de aplicativo comuns:</span><span class="sxs-lookup"><span data-stu-id="173a6-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-2.0"

1. <span data-ttu-id="173a6-152">Exceção/tratamento de erro</span><span class="sxs-lookup"><span data-stu-id="173a6-152">Exception/error handling</span></span>
1. <span data-ttu-id="173a6-153">Protocolo HTTP Strict Transport Security</span><span class="sxs-lookup"><span data-stu-id="173a6-153">HTTP Strict Transport Security Protocol</span></span>
1. <span data-ttu-id="173a6-154">Redirecionamento para HTTPS</span><span class="sxs-lookup"><span data-stu-id="173a6-154">HTTPS redirection</span></span>
1. <span data-ttu-id="173a6-155">Servidor de arquivos estático</span><span class="sxs-lookup"><span data-stu-id="173a6-155">Static file server</span></span>
1. <span data-ttu-id="173a6-156">Imposição de política de cookies</span><span class="sxs-lookup"><span data-stu-id="173a6-156">Cookie policy enforcement</span></span>
1. <span data-ttu-id="173a6-157">Autenticação</span><span class="sxs-lookup"><span data-stu-id="173a6-157">Authentication</span></span>
1. <span data-ttu-id="173a6-158">Session</span><span class="sxs-lookup"><span data-stu-id="173a6-158">Session</span></span>
1. <span data-ttu-id="173a6-159">MVC</span><span class="sxs-lookup"><span data-stu-id="173a6-159">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. <span data-ttu-id="173a6-160">Exceção/tratamento de erro</span><span class="sxs-lookup"><span data-stu-id="173a6-160">Exception/error handling</span></span>
1. <span data-ttu-id="173a6-161">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="173a6-161">Static files</span></span>
1. <span data-ttu-id="173a6-162">Autenticação</span><span class="sxs-lookup"><span data-stu-id="173a6-162">Authentication</span></span>
1. <span data-ttu-id="173a6-163">Session</span><span class="sxs-lookup"><span data-stu-id="173a6-163">Session</span></span>
1. <span data-ttu-id="173a6-164">MVC</span><span class="sxs-lookup"><span data-stu-id="173a6-164">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

<span data-ttu-id="173a6-165">No código de exemplo anterior, cada método de extensão de middleware é exposto em <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> por meio do namespace <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="173a6-165">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="173a6-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> é o primeiro componente de middleware adicionado ao pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="173a6-167">Portanto, o middleware de manipulador de exceção captura todas as exceções que ocorrem em chamadas posteriores.</span><span class="sxs-lookup"><span data-stu-id="173a6-167">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="173a6-168">O Middleware de Arquivo Estático é chamado no início do pipeline para que possa controlar as solicitações e causar o curto-circuito sem passar pelos componentes restantes.</span><span class="sxs-lookup"><span data-stu-id="173a6-168">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="173a6-169">O Middleware de Arquivo Estático não fornece **nenhuma** verificação de autorização.</span><span class="sxs-lookup"><span data-stu-id="173a6-169">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="173a6-170">Todos os arquivos atendidos, incluindo aqueles em *wwwroot*, estão disponíveis publicamente.</span><span class="sxs-lookup"><span data-stu-id="173a6-170">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="173a6-171">Para conhecer uma abordagem para proteger arquivos estáticos, veja <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="173a6-171">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="173a6-172">Se a solicitação não for controlada pelo Middleware de Arquivo Estático, ela será transmitida para o Middleware de Autenticação (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), que executa a autenticação.</span><span class="sxs-lookup"><span data-stu-id="173a6-172">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="173a6-173">A autenticação causa curto-circuito em solicitações não autenticadas.</span><span class="sxs-lookup"><span data-stu-id="173a6-173">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="173a6-174">Embora o middleware de autenticação autentique as solicitações, a autorização (e a rejeição) ocorre somente depois que o MVC seleciona uma Página Razor específica ou um controlador MVC e uma ação.</span><span class="sxs-lookup"><span data-stu-id="173a6-174">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="173a6-175">Se a solicitação não for controlada pelo Middleware de Arquivo Estático, ela será transmitida para o Middleware de Identidade (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), que executará a autenticação.</span><span class="sxs-lookup"><span data-stu-id="173a6-175">If the request isn't handled by Static File Middleware, it's passed on to the Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), which performs authentication.</span></span> <span data-ttu-id="173a6-176">A identidade não liga as solicitações não autenticadas em curto-circuito.</span><span class="sxs-lookup"><span data-stu-id="173a6-176">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="173a6-177">Embora a identidade autentique as solicitações, a autorização (e a rejeição) ocorre somente depois que o MVC seleciona um controlador específico e uma ação.</span><span class="sxs-lookup"><span data-stu-id="173a6-177">Although Identity authenticates requests, authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

::: moniker-end

<span data-ttu-id="173a6-178">O exemplo a seguir demonstra uma solicitação de middleware cujas solicitações de arquivos estáticos são manipuladas pelo Middleware de Arquivo Estático antes do Middleware de Compactação de Resposta.</span><span class="sxs-lookup"><span data-stu-id="173a6-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="173a6-179">Arquivos estáticos não são compactados com este pedido de middleware.</span><span class="sxs-lookup"><span data-stu-id="173a6-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="173a6-180">As respostas do MVC de <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> podem ser compactadas.</span><span class="sxs-lookup"><span data-stu-id="173a6-180">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a><span data-ttu-id="173a6-181">Use, Run e Map</span><span class="sxs-lookup"><span data-stu-id="173a6-181">Use, Run, and Map</span></span>

<span data-ttu-id="173a6-182">Configure o pipeline de HTTP usando `Use`, `Run` e `Map`.</span><span class="sxs-lookup"><span data-stu-id="173a6-182">Configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="173a6-183">O método `Use` pode ligar o pipeline em curto-circuito (ou seja, se ele não chamar um delegado de solicitação `next`).</span><span class="sxs-lookup"><span data-stu-id="173a6-183">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="173a6-184">`Run` é uma convenção e alguns componentes de middleware podem expor os métodos `Run[Middleware]` que são executados no final do pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-184">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="173a6-185">As extensões <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> são usadas como uma convenção de ramificação do pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="173a6-186">`Map*` ramifica o pipeline de solicitação com base na correspondência do caminho da solicitação em questão.</span><span class="sxs-lookup"><span data-stu-id="173a6-186">`Map*` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="173a6-187">Se o caminho da solicitação iniciar com o caminho especificado, o branch será executado.</span><span class="sxs-lookup"><span data-stu-id="173a6-187">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="173a6-188">A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior.</span><span class="sxs-lookup"><span data-stu-id="173a6-188">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="173a6-189">Solicitação</span><span class="sxs-lookup"><span data-stu-id="173a6-189">Request</span></span>             | <span data-ttu-id="173a6-190">Resposta</span><span class="sxs-lookup"><span data-stu-id="173a6-190">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="173a6-191">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="173a6-191">localhost:1234</span></span>      | <span data-ttu-id="173a6-192">Saudação do delegado diferente de Map.</span><span class="sxs-lookup"><span data-stu-id="173a6-192">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="173a6-193">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="173a6-193">localhost:1234/map1</span></span> | <span data-ttu-id="173a6-194">Teste de Map 1</span><span class="sxs-lookup"><span data-stu-id="173a6-194">Map Test 1</span></span>                   |
| <span data-ttu-id="173a6-195">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="173a6-195">localhost:1234/map2</span></span> | <span data-ttu-id="173a6-196">Teste de Map 2</span><span class="sxs-lookup"><span data-stu-id="173a6-196">Map Test 2</span></span>                   |
| <span data-ttu-id="173a6-197">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="173a6-197">localhost:1234/map3</span></span> | <span data-ttu-id="173a6-198">Saudação do delegado diferente de Map.</span><span class="sxs-lookup"><span data-stu-id="173a6-198">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="173a6-199">Quando `Map` é usado, os segmentos de caminho correspondentes são removidos do `HttpRequest.Path` e anexados em `HttpRequest.PathBase` para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="173a6-199">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="173a6-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) ramifica o pipeline de solicitação com base no resultado do predicado em questão.</span><span class="sxs-lookup"><span data-stu-id="173a6-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="173a6-201">Qualquer predicado do tipo `Func<HttpContext, bool>` pode ser usado para mapear as solicitações para um novo branch do pipeline.</span><span class="sxs-lookup"><span data-stu-id="173a6-201">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="173a6-202">No exemplo a seguir, um predicado é usado para detectar a presença de uma variável de cadeia de caracteres de consulta `branch`:</span><span class="sxs-lookup"><span data-stu-id="173a6-202">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="173a6-203">A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior.</span><span class="sxs-lookup"><span data-stu-id="173a6-203">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="173a6-204">Solicitação</span><span class="sxs-lookup"><span data-stu-id="173a6-204">Request</span></span>                       | <span data-ttu-id="173a6-205">Resposta</span><span class="sxs-lookup"><span data-stu-id="173a6-205">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="173a6-206">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="173a6-206">localhost:1234</span></span>                | <span data-ttu-id="173a6-207">Saudação do delegado diferente de Map.</span><span class="sxs-lookup"><span data-stu-id="173a6-207">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="173a6-208">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="173a6-208">localhost:1234/?branch=master</span></span> | <span data-ttu-id="173a6-209">Branch usado = mestre</span><span class="sxs-lookup"><span data-stu-id="173a6-209">Branch used = master</span></span>         |

<span data-ttu-id="173a6-210">`Map` é compatível com aninhamento, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="173a6-210">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

<span data-ttu-id="173a6-211">`Map` também pode ser correspondido com vários segmentos de uma vez:</span><span class="sxs-lookup"><span data-stu-id="173a6-211">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="173a6-212">Middleware interno</span><span class="sxs-lookup"><span data-stu-id="173a6-212">Built-in middleware</span></span>

<span data-ttu-id="173a6-213">O ASP.NET Core é fornecido com os seguintes componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="173a6-213">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="173a6-214">A coluna *Ordem* fornece observações sobre o posicionamento do middleware no pipeline de processamento da solicitação e sob quais condições o middleware podem encerrar o processamento da solicitação.</span><span class="sxs-lookup"><span data-stu-id="173a6-214">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="173a6-215">Quando um middleware causa um curto-circuito na solicitação ao processar o pipeline e impede outros middleware downstream de processar uma solicitação, ele é chamado de *middleware terminal*.</span><span class="sxs-lookup"><span data-stu-id="173a6-215">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="173a6-216">Para saber mais sobre curto-circuito, confira a seção [Criar um pipeline de middleware com o IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="173a6-216">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="173a6-217">Middleware</span><span class="sxs-lookup"><span data-stu-id="173a6-217">Middleware</span></span> | <span data-ttu-id="173a6-218">Descrição</span><span class="sxs-lookup"><span data-stu-id="173a6-218">Description</span></span> | <span data-ttu-id="173a6-219">Pedido</span><span class="sxs-lookup"><span data-stu-id="173a6-219">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="173a6-220">Autenticação</span><span class="sxs-lookup"><span data-stu-id="173a6-220">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="173a6-221">Fornece suporte à autenticação.</span><span class="sxs-lookup"><span data-stu-id="173a6-221">Provides authentication support.</span></span> | <span data-ttu-id="173a6-222">Antes de `HttpContext.User` ser necessário.</span><span class="sxs-lookup"><span data-stu-id="173a6-222">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="173a6-223">Terminal para retornos de chamada OAuth.</span><span class="sxs-lookup"><span data-stu-id="173a6-223">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="173a6-224">Política de cookies</span><span class="sxs-lookup"><span data-stu-id="173a6-224">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="173a6-225">Acompanha o consentimento dos usuários para o armazenamento de informações pessoais e impõe padrões mínimos para campos de cookie, tais como `secure` e `SameSite`.</span><span class="sxs-lookup"><span data-stu-id="173a6-225">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="173a6-226">Antes do middleware que emite cookies.</span><span class="sxs-lookup"><span data-stu-id="173a6-226">Before middleware that issues cookies.</span></span> <span data-ttu-id="173a6-227">Exemplos: autenticação, sessão e MVC (TempData).</span><span class="sxs-lookup"><span data-stu-id="173a6-227">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="173a6-228">CORS</span><span class="sxs-lookup"><span data-stu-id="173a6-228">CORS</span></span>](xref:security/cors) | <span data-ttu-id="173a6-229">Configura o Compartilhamento de Recursos entre Origens.</span><span class="sxs-lookup"><span data-stu-id="173a6-229">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="173a6-230">Antes de componentes que usam o CORS.</span><span class="sxs-lookup"><span data-stu-id="173a6-230">Before components that use CORS.</span></span> |
| [<span data-ttu-id="173a6-231">Tratamento de Exceção</span><span class="sxs-lookup"><span data-stu-id="173a6-231">Exception Handling</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="173a6-232">Trata exceções.</span><span class="sxs-lookup"><span data-stu-id="173a6-232">Handles exceptions.</span></span> | <span data-ttu-id="173a6-233">Antes dos componentes que geram erros.</span><span class="sxs-lookup"><span data-stu-id="173a6-233">Before components that generate errors.</span></span> |
| [<span data-ttu-id="173a6-234">Cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="173a6-234">Forwarded Headers</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="173a6-235">Encaminha cabeçalhos como proxy para a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="173a6-235">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="173a6-236">Antes dos componentes que consomem os campos atualizados.</span><span class="sxs-lookup"><span data-stu-id="173a6-236">Before components that consume the updated fields.</span></span> <span data-ttu-id="173a6-237">Exemplos: esquema, host, IP do cliente e método.</span><span class="sxs-lookup"><span data-stu-id="173a6-237">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="173a6-238">Verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="173a6-238">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="173a6-239">Verifica a integridade de um aplicativo ASP.NET Core e suas dependências, como a verificação da disponibilidade do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="173a6-239">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="173a6-240">Terminal, se uma solicitação corresponde a um ponto de extremidade da verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="173a6-240">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="173a6-241">Substituição do Método HTTP</span><span class="sxs-lookup"><span data-stu-id="173a6-241">HTTP Method Override</span></span>](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | <span data-ttu-id="173a6-242">Permite que uma solicitação de entrada POST substitua o método.</span><span class="sxs-lookup"><span data-stu-id="173a6-242">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="173a6-243">Antes dos componentes que consomem o método atualizado.</span><span class="sxs-lookup"><span data-stu-id="173a6-243">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="173a6-244">Redirecionamento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="173a6-244">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="173a6-245">Redirecione todas as solicitações HTTP para HTTPS (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="173a6-245">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="173a6-246">Antes dos componentes que consomem a URL.</span><span class="sxs-lookup"><span data-stu-id="173a6-246">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="173a6-247">Segurança de Transporte Estrita de HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="173a6-247">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="173a6-248">Middleware de aprimoramento de segurança que adiciona um cabeçalho de resposta especial (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="173a6-248">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="173a6-249">Antes das respostas serem enviadas e depois dos componentes que modificam solicitações.</span><span class="sxs-lookup"><span data-stu-id="173a6-249">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="173a6-250">Exemplos: cabeçalhos encaminhados, regravação de URL.</span><span class="sxs-lookup"><span data-stu-id="173a6-250">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="173a6-251">MVC</span><span class="sxs-lookup"><span data-stu-id="173a6-251">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="173a6-252">Processa as solicitações de Razor Pages/MVC (ASP.NET Core 2.0 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="173a6-252">Processes requests with MVC/Razor Pages (ASP.NET Core 2.0 or later).</span></span> | <span data-ttu-id="173a6-253">Terminal, se uma solicitação corresponder a uma rota.</span><span class="sxs-lookup"><span data-stu-id="173a6-253">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="173a6-254">OWIN</span><span class="sxs-lookup"><span data-stu-id="173a6-254">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="173a6-255">Interoperabilidade com aplicativos baseados em OWIN, em servidores e em middleware.</span><span class="sxs-lookup"><span data-stu-id="173a6-255">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="173a6-256">Terminal, se o middleware OWIN processa totalmente a solicitação.</span><span class="sxs-lookup"><span data-stu-id="173a6-256">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="173a6-257">Cache de resposta</span><span class="sxs-lookup"><span data-stu-id="173a6-257">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="173a6-258">Fornece suporte para as respostas em cache.</span><span class="sxs-lookup"><span data-stu-id="173a6-258">Provides support for caching responses.</span></span> | <span data-ttu-id="173a6-259">Antes dos componentes que exigem armazenamento em cache.</span><span class="sxs-lookup"><span data-stu-id="173a6-259">Before components that require caching.</span></span> |
| [<span data-ttu-id="173a6-260">Compactação de resposta</span><span class="sxs-lookup"><span data-stu-id="173a6-260">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="173a6-261">Fornece suporte para a compactação de respostas.</span><span class="sxs-lookup"><span data-stu-id="173a6-261">Provides support for compressing responses.</span></span> | <span data-ttu-id="173a6-262">Antes dos componentes que exigem compactação.</span><span class="sxs-lookup"><span data-stu-id="173a6-262">Before components that require compression.</span></span> |
| [<span data-ttu-id="173a6-263">Localização de Solicitação</span><span class="sxs-lookup"><span data-stu-id="173a6-263">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="173a6-264">Fornece suporte à localização.</span><span class="sxs-lookup"><span data-stu-id="173a6-264">Provides localization support.</span></span> | <span data-ttu-id="173a6-265">Antes dos componentes de localização importantes.</span><span class="sxs-lookup"><span data-stu-id="173a6-265">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="173a6-266">Roteamento</span><span class="sxs-lookup"><span data-stu-id="173a6-266">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="173a6-267">Define e restringe as rotas de solicitação.</span><span class="sxs-lookup"><span data-stu-id="173a6-267">Defines and constrains request routes.</span></span> | <span data-ttu-id="173a6-268">Terminal de rotas correspondentes.</span><span class="sxs-lookup"><span data-stu-id="173a6-268">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="173a6-269">Sessão</span><span class="sxs-lookup"><span data-stu-id="173a6-269">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="173a6-270">Fornece suporte para gerenciar sessões de usuário.</span><span class="sxs-lookup"><span data-stu-id="173a6-270">Provides support for managing user sessions.</span></span> | <span data-ttu-id="173a6-271">Antes de componentes que exigem a sessão.</span><span class="sxs-lookup"><span data-stu-id="173a6-271">Before components that require Session.</span></span> |
| [<span data-ttu-id="173a6-272">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="173a6-272">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="173a6-273">Fornece suporte para servir arquivos estáticos e pesquisa no diretório.</span><span class="sxs-lookup"><span data-stu-id="173a6-273">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="173a6-274">Terminal, se uma solicitação corresponde a um arquivo.</span><span class="sxs-lookup"><span data-stu-id="173a6-274">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="173a6-275">Regravação de URL</span><span class="sxs-lookup"><span data-stu-id="173a6-275">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="173a6-276">Fornece suporte para regravar URLs e redirecionar solicitações.</span><span class="sxs-lookup"><span data-stu-id="173a6-276">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="173a6-277">Antes dos componentes que consomem a URL.</span><span class="sxs-lookup"><span data-stu-id="173a6-277">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="173a6-278">WebSockets</span><span class="sxs-lookup"><span data-stu-id="173a6-278">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="173a6-279">Habilita o protocolo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="173a6-279">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="173a6-280">Antes dos componentes que são necessários para aceitar solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="173a6-280">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="173a6-281">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="173a6-281">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
