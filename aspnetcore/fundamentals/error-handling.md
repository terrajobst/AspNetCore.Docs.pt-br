---
title: Tratar erros no ASP.NET Core
author: tdykstra
description: Descubra como tratar erros em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: d809c70b3fae6b2d21d5ec0871298d905b873d5d
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665357"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="3d8b2-103">Tratar erros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d8b2-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="3d8b2-104">Por [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3d8b2-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3d8b2-105">Este artigo apresenta abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="3d8b2-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3d8b2-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="3d8b2-107">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="3d8b2-107">Developer Exception Page</span></span>

<span data-ttu-id="3d8b2-108">Para configurar um aplicativo para exibir uma página que mostra informações detalhadas sobre exceções de solicitação, use a *página de exceção do desenvolvedor*.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-108">To configure an app to display a page that shows detailed information about request exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="3d8b2-109">A página é disponibilizada pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está disponível no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="3d8b2-110">Adicione uma linha ao método `Startup.Configure` quando o aplicativo estiver sendo executado no [ambiente](xref:fundamentals/environments) de desenvolvimento :</span><span class="sxs-lookup"><span data-stu-id="3d8b2-110">Add a line to the `Startup.Configure` method when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseDeveloperExceptionPage)]

<span data-ttu-id="3d8b2-111">Coloque a chamada para <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> na frente de qualquer middleware no qual você deseja capturar exceções.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-111">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> in front of any middleware where you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="3d8b2-112">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-112">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="3d8b2-113">Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="3d8b2-114">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-114">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3d8b2-115">Para ver a página de exceção do desenvolvedor, execute o aplicativo de exemplo com o ambiente definido como `Development` e adicione `?throw=true` à URL base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-115">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="3d8b2-116">A página inclui as seguintes informações sobre a exceção e a solicitação:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="3d8b2-117">Rastreamento de pilha</span><span class="sxs-lookup"><span data-stu-id="3d8b2-117">Stack trace</span></span>
* <span data-ttu-id="3d8b2-118">Parâmetros de cadeia de caracteres de consulta (se houver algum)</span><span class="sxs-lookup"><span data-stu-id="3d8b2-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="3d8b2-119">Cookies (se houver algum)</span><span class="sxs-lookup"><span data-stu-id="3d8b2-119">Cookies (if any)</span></span>
* <span data-ttu-id="3d8b2-120">Cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="3d8b2-120">Headers</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="3d8b2-121">Configurar uma página de tratamento de exceção personalizada</span><span class="sxs-lookup"><span data-stu-id="3d8b2-121">Configure a custom exception handling page</span></span>

<span data-ttu-id="3d8b2-122">Quando o aplicativo não estiver em execução no ambiente de desenvolvimento, chame o método de extensão <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> para adicionar o Middleware de manipulação de exceção.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-122">When the app isn't running in the Development environment, call the <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> extension method to add Exception Handling Middleware.</span></span> <span data-ttu-id="3d8b2-123">O middleware:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-123">The middleware:</span></span>

* <span data-ttu-id="3d8b2-124">Captura exceções.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-124">Catches exceptions.</span></span>
* <span data-ttu-id="3d8b2-125">Registra exceções em log.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-125">Logs exceptions.</span></span>
* <span data-ttu-id="3d8b2-126">Executa novamente a solicitação em um pipeline alternativo para a página ou controlador indicado.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="3d8b2-127">A solicitação não será executada novamente se a resposta tiver sido iniciada.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="3d8b2-128">No exemplo de aplicativo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> inclui o Middleware de Manipulação de Exceções em ambientes que não são de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-128">In the following example from the sample app, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments.</span></span> <span data-ttu-id="3d8b2-129">O método de extensão especifica uma página de erro ou um controlador no ponto de extremidade `/Error` para solicitações reexecutadas depois que as exceções são capturadas e registradas:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-129">The extension method specifies an error page or controller at the `/Error` endpoint for re-executed requests after exceptions are caught and logged:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler1)]

<span data-ttu-id="3d8b2-130">O modelo de aplicativo Razor Pages fornece uma Página de erro (*.cshtml*) e uma classe <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) na pasta Páginas.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the Pages folder.</span></span>

<span data-ttu-id="3d8b2-131">Em um aplicativo MVC, o método do manipulador de erro a seguir está incluído no modelo do aplicativo MVC e é exibido no controlador Home:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-131">In an MVC app, the following error handler method is included in the MVC app template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="3d8b2-132">Não decore o método de ação do manipulador de erro com atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="3d8b2-133">Verbos explícitos impedem algumas solicitações de chegar ao método.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="3d8b2-134">Permita acesso anônimo ao método para que os usuários não autenticados possam capazes receber a exibição de erro.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

## <a name="access-the-exception"></a><span data-ttu-id="3d8b2-135">Acessar a exceção</span><span class="sxs-lookup"><span data-stu-id="3d8b2-135">Access the exception</span></span>

<span data-ttu-id="3d8b2-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção ou o caminho de solicitação original em um controlador ou uma página:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception or the original request path in a controller or page:</span></span>

* <span data-ttu-id="3d8b2-137">O caminho está disponível por meio da propriedade <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-137">The path is available from the <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path> property.</span></span>
* <span data-ttu-id="3d8b2-138">Leia <xref:System.Exception?displayProperty=fullName> da propriedade herdada [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-138">Read the <xref:System.Exception?displayProperty=fullName> from the inherited [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error) property.</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics;

var exceptionHandlerPathFeature = 
    HttpContext.Features.Get<IExceptionHandlerPathFeature>();
var path = exceptionHandlerPathFeature?.Path;
var error = exceptionHandlerPathFeature?.Error;
```

> [!WARNING]
> <span data-ttu-id="3d8b2-139">**Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-139">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="3d8b2-140">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-140">Serving errors is a security risk.</span></span>

## <a name="configure-custom-exception-handling-code"></a><span data-ttu-id="3d8b2-141">Configurar código de tratamento de exceção personalizada</span><span class="sxs-lookup"><span data-stu-id="3d8b2-141">Configure custom exception handling code</span></span>

<span data-ttu-id="3d8b2-142">Uma alternativa para atender a um ponto de extremidade de erros com uma [página de tratamento de exceção personalizada](#configure-a-custom-exception-handling-page) é fornecer um lambda para <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-142">An alternative to serving an endpoint for errors with a [custom exception handling page](#configure-a-custom-exception-handling-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="3d8b2-143">Usar um lambda com <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> permite acessar o erro antes de retornar a resposta.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-143">Using a lambda with <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> allows access to the error before returning the response.</span></span>

<span data-ttu-id="3d8b2-144">O aplicativo de exemplo demonstra o código de tratamento de exceção personalizado em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-144">The sample app demonstrates custom exception handling code in `Startup.Configure`.</span></span> <span data-ttu-id="3d8b2-145">Dispare uma exceção com o link **Gerar Exceção** na página de índice.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-145">Trigger an exception with the **Throw Exception** link on the Index page.</span></span> <span data-ttu-id="3d8b2-146">O lambda a seguir executa:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-146">The following lambda runs:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler2)]

> [!WARNING]
> <span data-ttu-id="3d8b2-147">**Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="3d8b2-148">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-148">Serving errors is a security risk.</span></span>

## <a name="configure-status-code-pages"></a><span data-ttu-id="3d8b2-149">Configurar páginas de código de status</span><span class="sxs-lookup"><span data-stu-id="3d8b2-149">Configure status code pages</span></span>

<span data-ttu-id="3d8b2-150">Por padrão, o aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status HTTP, como *404 - Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-150">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="3d8b2-151">O aplicativo retornar um código de status e um corpo de resposta vazio.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-151">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="3d8b2-152">Para fornecer páginas de código de status, use o middleware das páginas de código de status.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-152">To provide status code pages, use Status Code Pages Middleware.</span></span>

<span data-ttu-id="3d8b2-153">O middleware é disponibilizado pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está disponível no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-153">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3d8b2-154">Adicione uma linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-154">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="3d8b2-155">Chame o método <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> antes do middleware de tratamento da solicitação (por exemplo, o middleware de arquivos estáticos e o middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-155">Call the <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> method before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="3d8b2-156">Por padrão, o middleware de páginas de código de status adiciona manipuladores, somente de texto, para os códigos de status comuns, como *404 - Não Encontrado*:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-156">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as *404 - Not Found*:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="3d8b2-157">O middleware é compatível com vários métodos de extensão que permitem personalizar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-157">The middleware supports several extension methods that allow you to customize its behavior.</span></span>

<span data-ttu-id="3d8b2-158">Uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> usa uma expressão lambda, que você pode usar para processar a lógica personalizada de tratamento de erros e gravar manualmente a resposta:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-158">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a lambda expression, which you can use to process custom error-handling logic and manually write the response:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="3d8b2-159">Uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> usa um tipo de conteúdo e uma cadeia de formato, que você pode usar para personalizar o texto da resposta e o tipo de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-159">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a content type and format string, which you can use to customize the content type and response text:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a><span data-ttu-id="3d8b2-160">Redirecionar e executar métodos de extensão novamente</span><span class="sxs-lookup"><span data-stu-id="3d8b2-160">Redirect and re-execute extension methods</span></span>

<span data-ttu-id="3d8b2-161"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-161"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="3d8b2-162">Envia um código de status *302 – Encontrado* ao cliente.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-162">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="3d8b2-163">Redireciona o cliente para o local fornecido no modelo de URL.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-163">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="3d8b2-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> normalmente é usado quando o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> is commonly used when the app:</span></span>

* <span data-ttu-id="3d8b2-165">Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-165">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="3d8b2-166">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-166">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="3d8b2-167">Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-167">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="3d8b2-168"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-168"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="3d8b2-169">Retorna o código de status original ao cliente.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-169">Returns the original status code to the client.</span></span>
* <span data-ttu-id="3d8b2-170">Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-170">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<span data-ttu-id="3d8b2-171"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> normalmente é usado quando o aplicativo tem por obrigação:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-171"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> is commonly used when the app should:</span></span>

* <span data-ttu-id="3d8b2-172">Processar a solicitação sem redirecionar para um ponto de extremidade diferente.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-172">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="3d8b2-173">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-173">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="3d8b2-174">Preservar e retornar o código de status original com a resposta.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-174">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="3d8b2-175">O modelo pode incluir um espaço reservado (`{0}`) para o código de status.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-175">Templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="3d8b2-176">O modelo deve começar com uma barra (`/`).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-176">The template must start with a forward slash (`/`).</span></span> <span data-ttu-id="3d8b2-177">Ao usar um espaço reservado, verifique se o ponto de extremidade (página ou controlador) possa processar o segmento de linha.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-177">When using a placeholder, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="3d8b2-178">Por exemplo, uma Página do Razor para erros deve aceitar o valor de segmento de linha opcional com a diretiva `@page`:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-178">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="3d8b2-179">As páginas de código de status podem ser desabilitadas para solicitações específicas em um método de manipulador de Páginas Razor ou em um controlador do MVC.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-179">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="3d8b2-180">Para desabilitar as páginas de código de status, tente recuperar o <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> da coleção [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) da solicitação e desabilitar o recurso se ele estiver disponível:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-180">To disable status code pages, attempt to retrieve the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> from the request's [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="3d8b2-181">Para usar uma sobrecarga <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que aponta para um ponto de extremidade dentro do aplicativo, crie um modo de exibição do MVC ou Razor Page para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-181">To use a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="3d8b2-182">Por exemplo, o modelo de aplicativo Razor Pages produz a seguinte página e classe de modelo de página:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-182">For example, the Razor Pages app template produces the following page and page model class:</span></span>

<span data-ttu-id="3d8b2-183">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-183">*Error.cshtml*:</span></span>

::: moniker range=">= aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to the <strong>Development</strong> environment displays 
    detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed 
    applications.</strong> It can result in displaying sensitive information 
    from exceptions to end users. For local debugging, enable the 
    <strong>Development</strong> environment by setting the 
    <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to 
    <strong>Development</strong> and restarting the app.
</p>
```

<span data-ttu-id="3d8b2-184">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-184">*Error.cshtml.cs*:</span></span>

```csharp
[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

<span data-ttu-id="3d8b2-185">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-185">*Error.cshtml.cs*:</span></span>

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

## <a name="exception-handling-code"></a><span data-ttu-id="3d8b2-186">Código de tratamento de exceção</span><span class="sxs-lookup"><span data-stu-id="3d8b2-186">Exception-handling code</span></span>

<span data-ttu-id="3d8b2-187">Código em páginas de tratamento de exceção pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-187">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="3d8b2-188">Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-188">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="3d8b2-189">Além disso, lembre-se que, depois que os cabeçalhos de resposta são enviados:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-189">Also, be aware that once the headers for a response are sent:</span></span>

* <span data-ttu-id="3d8b2-190">O aplicativo já não consegue alterar o código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-190">The app can't change the response's status code.</span></span>
* <span data-ttu-id="3d8b2-191">As páginas de exceção ou manipuladores não podem ser executados.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-191">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="3d8b2-192">A resposta deve ser concluída ou a conexão será anulada.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-192">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="3d8b2-193">Tratamento de exceções do servidor</span><span class="sxs-lookup"><span data-stu-id="3d8b2-193">Server exception handling</span></span>

<span data-ttu-id="3d8b2-194">Além da lógica de tratamento de exceção no aplicativo, a [implementação do servidor](xref:fundamentals/servers/index) pode lidar com algumas exceções.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-194">In addition to the exception handling logic in your app, the [server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="3d8b2-195">Se o servidor capturar uma exceção antes que os cabeçalhos de resposta sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-195">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="3d8b2-196">Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-196">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="3d8b2-197">As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-197">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="3d8b2-198">Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-198">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="3d8b2-199">As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-199">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="3d8b2-200">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="3d8b2-200">Startup exception handling</span></span>

<span data-ttu-id="3d8b2-201">Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-201">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="3d8b2-202">Usando o [Host da Web](xref:fundamentals/host/web-host), você pode [configurar como o host se comporta em resposta a erros durante a inicialização](xref:fundamentals/host/web-host#detailed-errors) usando as chaves `captureStartupErrors` e `detailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-202">Using [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="3d8b2-203">A hospedagem apenas poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-203">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="3d8b2-204">Se alguma associação falhar por qualquer motivo:</span><span class="sxs-lookup"><span data-stu-id="3d8b2-204">If any binding fails for any reason:</span></span>

* <span data-ttu-id="3d8b2-205">A camada de hospedagem registrará uma exceção crítica.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-205">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="3d8b2-206">O processo dotnet falhará.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-206">The dotnet process crashes.</span></span>
* <span data-ttu-id="3d8b2-207">Nenhuma página de erro será exibida quando o aplicativo estiver sendo executado no servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="3d8b2-207">No error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="3d8b2-208">Quando executado no [IIS](/iis) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-208">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="3d8b2-209">Para obter mais informações, consulte <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-209">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="3d8b2-210">Para obter informações sobre como solucionar problemas de inicialização com o Serviço de Aplicativo do Azure, consulte <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-210">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="3d8b2-211">Tratamento de erro do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3d8b2-211">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="3d8b2-212">Os aplicativos [MVC](xref:mvc/overview) contêm algumas opções adicionais para o tratamento de erros, como configurar filtros de exceção e executar a validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-212">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="3d8b2-213">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="3d8b2-213">Exception filters</span></span>

<span data-ttu-id="3d8b2-214">Os filtros de exceção podem ser configurados globalmente ou por controlador ou por ação em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-214">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="3d8b2-215">Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-215">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="3d8b2-216">Esses filtros não são chamados de outra forma.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-216">These filters aren't called otherwise.</span></span> <span data-ttu-id="3d8b2-217">Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-217">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="3d8b2-218">Os filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-218">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="3d8b2-219">É recomendável usar o middleware.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-219">We recommend using the middleware.</span></span> <span data-ttu-id="3d8b2-220">Use filtros somente quando você precisar fazer o tratamento de erro *de forma diferente* com base na ação de MVC escolhida.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-220">Use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handle-model-state-errors"></a><span data-ttu-id="3d8b2-221">Lidar com erros de estado do modelo</span><span class="sxs-lookup"><span data-stu-id="3d8b2-221">Handle model state errors</span></span>

<span data-ttu-id="3d8b2-222">A [validação do modelo](xref:mvc/models/validation) ocorre antes da invocação de cada ação do controlador e é responsabilidade do método de ação inspecionar [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) e responder de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-222">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) and react appropriately.</span></span>

<span data-ttu-id="3d8b2-223">Alguns aplicativos optam por seguir uma convenção padrão para lidar com erros de [validação do modelo](xref:mvc/models/validation), caso em que um [filtro](xref:mvc/controllers/filters) pode ser um local adequado para implementar uma política como essa.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-223">Some apps choose to follow a standard convention for dealing with [model validation](xref:mvc/models/validation) errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="3d8b2-224">É necessário testar o comportamento das ações com estados de modelo inválidos.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-224">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="3d8b2-225">Para obter mais informações, consulte <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="3d8b2-225">For more information, see <xref:mvc/controllers/testing>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d8b2-226">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3d8b2-226">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
