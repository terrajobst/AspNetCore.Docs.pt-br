---
title: Tratar erros no ASP.NET Core
author: tdykstra
description: Descubra como tratar erros em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/01/2019
uid: fundamentals/error-handling
ms.openlocfilehash: a2ae2cb25c8cc5048b189b4035abbfc32a29aaff
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345479"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="61a86-103">Tratar erros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61a86-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="61a86-104">Por [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="61a86-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="61a86-105">Este artigo apresenta abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="61a86-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="61a86-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61a86-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="61a86-107">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="61a86-107">Developer Exception Page</span></span>

<span data-ttu-id="61a86-108">Para configurar um aplicativo para exibir uma página que mostra informações detalhadas sobre exceções, use a *página de exceção do desenvolvedor*.</span><span class="sxs-lookup"><span data-stu-id="61a86-108">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="61a86-109">A página é disponibilizada pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está disponível no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="61a86-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="61a86-110">Adicione uma linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="61a86-110">Add a line to the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=5)]

<span data-ttu-id="61a86-111">Coloque a chamada para <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> na frente de qualquer middleware no qual você deseja capturar exceções.</span><span class="sxs-lookup"><span data-stu-id="61a86-111">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> in front of any middleware where you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="61a86-112">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="61a86-112">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="61a86-113">Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção.</span><span class="sxs-lookup"><span data-stu-id="61a86-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="61a86-114">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="61a86-114">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="61a86-115">Para ver a página de exceção do desenvolvedor, execute o aplicativo de exemplo com o ambiente definido como `Development` e adicione `?throw=true` à URL base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="61a86-115">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="61a86-116">A página inclui as seguintes informações sobre a exceção e a solicitação:</span><span class="sxs-lookup"><span data-stu-id="61a86-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="61a86-117">Rastreamento de pilha</span><span class="sxs-lookup"><span data-stu-id="61a86-117">Stack trace</span></span>
* <span data-ttu-id="61a86-118">Parâmetros de cadeia de caracteres de consulta (se houver algum)</span><span class="sxs-lookup"><span data-stu-id="61a86-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="61a86-119">Cookies (se houver algum)</span><span class="sxs-lookup"><span data-stu-id="61a86-119">Cookies (if any)</span></span>
* <span data-ttu-id="61a86-120">Cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="61a86-120">Headers</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="61a86-121">Configurar uma página de tratamento de exceção personalizada</span><span class="sxs-lookup"><span data-stu-id="61a86-121">Configure a custom exception handling page</span></span>

<span data-ttu-id="61a86-122">Quando o aplicativo não estiver em execução no ambiente de desenvolvimento, chame o método de extensão <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> para adicionar o Middleware de manipulação de exceção.</span><span class="sxs-lookup"><span data-stu-id="61a86-122">When the app isn't running in the Development environment, call the <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> extension method to add Exception Handling Middleware.</span></span> <span data-ttu-id="61a86-123">O middleware:</span><span class="sxs-lookup"><span data-stu-id="61a86-123">The middleware:</span></span>

* <span data-ttu-id="61a86-124">Captura exceções.</span><span class="sxs-lookup"><span data-stu-id="61a86-124">Catches exceptions.</span></span>
* <span data-ttu-id="61a86-125">Registra exceções em log.</span><span class="sxs-lookup"><span data-stu-id="61a86-125">Logs exceptions.</span></span>
* <span data-ttu-id="61a86-126">Executa novamente a solicitação em um pipeline alternativo para a página ou controlador indicado.</span><span class="sxs-lookup"><span data-stu-id="61a86-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="61a86-127">A solicitação não será executada novamente se a resposta tiver sido iniciada.</span><span class="sxs-lookup"><span data-stu-id="61a86-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="61a86-128">No exemplo de aplicativo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> inclui o Middleware de Manipulação de Exceções em ambientes que não são de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="61a86-128">In the following example from the sample app, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments.</span></span> <span data-ttu-id="61a86-129">O método de extensão especifica uma página de erro ou um controlador no ponto de extremidade `/Error` para solicitações reexecutadas depois que as exceções são capturadas e registradas:</span><span class="sxs-lookup"><span data-stu-id="61a86-129">The extension method specifies an error page or controller at the `/Error` endpoint for re-executed requests after exceptions are caught and logged:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=9)]

<span data-ttu-id="61a86-130">O modelo de aplicativo Razor Pages fornece uma Página de erro (*.cshtml*) e uma classe <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) na pasta Páginas.</span><span class="sxs-lookup"><span data-stu-id="61a86-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the Pages folder.</span></span>

<span data-ttu-id="61a86-131">Em um aplicativo MVC, o método do manipulador de erro a seguir está incluído no modelo do aplicativo MVC e é exibido no controlador Home:</span><span class="sxs-lookup"><span data-stu-id="61a86-131">In an MVC app, the following error handler method is included in the MVC app template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="61a86-132">Não decore o método de ação do manipulador de erro com atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="61a86-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="61a86-133">Verbos explícitos impedem algumas solicitações de chegar ao método.</span><span class="sxs-lookup"><span data-stu-id="61a86-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="61a86-134">Permita acesso anônimo ao método para que os usuários não autenticados possam capazes receber a exibição de erro.</span><span class="sxs-lookup"><span data-stu-id="61a86-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

## <a name="configure-status-code-pages"></a><span data-ttu-id="61a86-135">Configurar páginas de código de status</span><span class="sxs-lookup"><span data-stu-id="61a86-135">Configure status code pages</span></span>

<span data-ttu-id="61a86-136">Por padrão, o aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status HTTP, como *404 - Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="61a86-136">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="61a86-137">O aplicativo retornar um código de status e um corpo de resposta vazio.</span><span class="sxs-lookup"><span data-stu-id="61a86-137">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="61a86-138">Para fornecer páginas de código de status, use o middleware das páginas de código de status.</span><span class="sxs-lookup"><span data-stu-id="61a86-138">To provide status code pages, use Status Code Pages Middleware.</span></span>

<span data-ttu-id="61a86-139">O middleware é disponibilizado pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está disponível no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="61a86-139">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="61a86-140">Adicione uma linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="61a86-140">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="61a86-141">Chame o método <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> antes do middleware de tratamento da solicitação (por exemplo, o middleware de arquivos estáticos e o middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="61a86-141">Call the <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> method before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="61a86-142">Por padrão, o middleware de páginas de código de status adiciona manipuladores, somente de texto, para os códigos de status comuns, como *404 - Não Encontrado*:</span><span class="sxs-lookup"><span data-stu-id="61a86-142">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as *404 - Not Found*:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="61a86-143">O middleware é compatível com vários métodos de extensão que permitem personalizar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="61a86-143">The middleware supports several extension methods that allow you to customize its behavior.</span></span>

<span data-ttu-id="61a86-144">Uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> usa uma expressão lambda, que você pode usar para processar a lógica personalizada de tratamento de erros e gravar manualmente a resposta:</span><span class="sxs-lookup"><span data-stu-id="61a86-144">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a lambda expression, which you can use to process custom error-handling logic and manually write the response:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="61a86-145">Uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> usa um tipo de conteúdo e uma cadeia de formato, que você pode usar para personalizar o texto da resposta e o tipo de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="61a86-145">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a content type and format string, which you can use to customize the content type and response text:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a><span data-ttu-id="61a86-146">Redirecionar e executar métodos de extensão novamente</span><span class="sxs-lookup"><span data-stu-id="61a86-146">Redirect and re-execute extension methods</span></span>

<span data-ttu-id="61a86-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="61a86-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="61a86-148">Envia um código de status *302 – Encontrado* ao cliente.</span><span class="sxs-lookup"><span data-stu-id="61a86-148">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="61a86-149">Redireciona o cliente para o local fornecido no modelo de URL.</span><span class="sxs-lookup"><span data-stu-id="61a86-149">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="61a86-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> normalmente é usado quando o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="61a86-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> is commonly used when the app:</span></span>

* <span data-ttu-id="61a86-151">Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro.</span><span class="sxs-lookup"><span data-stu-id="61a86-151">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="61a86-152">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.</span><span class="sxs-lookup"><span data-stu-id="61a86-152">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="61a86-153">Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.</span><span class="sxs-lookup"><span data-stu-id="61a86-153">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="61a86-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="61a86-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="61a86-155">Retorna o código de status original ao cliente.</span><span class="sxs-lookup"><span data-stu-id="61a86-155">Returns the original status code to the client.</span></span>
* <span data-ttu-id="61a86-156">Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.</span><span class="sxs-lookup"><span data-stu-id="61a86-156">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<span data-ttu-id="61a86-157"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> normalmente é usado quando o aplicativo tem por obrigação:</span><span class="sxs-lookup"><span data-stu-id="61a86-157"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> is commonly used when the app should:</span></span>

* <span data-ttu-id="61a86-158">Processar a solicitação sem redirecionar para um ponto de extremidade diferente.</span><span class="sxs-lookup"><span data-stu-id="61a86-158">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="61a86-159">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.</span><span class="sxs-lookup"><span data-stu-id="61a86-159">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="61a86-160">Preservar e retornar o código de status original com a resposta.</span><span class="sxs-lookup"><span data-stu-id="61a86-160">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="61a86-161">O modelo pode incluir um espaço reservado (`{0}`) para o código de status.</span><span class="sxs-lookup"><span data-stu-id="61a86-161">Templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="61a86-162">O modelo deve começar com uma barra (`/`).</span><span class="sxs-lookup"><span data-stu-id="61a86-162">The template must start with a forward slash (`/`).</span></span> <span data-ttu-id="61a86-163">Ao usar um espaço reservado, verifique se o ponto de extremidade (página ou controlador) possa processar o segmento de linha.</span><span class="sxs-lookup"><span data-stu-id="61a86-163">When using a placeholder, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="61a86-164">Por exemplo, uma Página do Razor para erros deve aceitar o valor de segmento de linha opcional com a diretiva `@page`:</span><span class="sxs-lookup"><span data-stu-id="61a86-164">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="61a86-165">As páginas de código de status podem ser desabilitadas para solicitações específicas em um método de manipulador de Páginas Razor ou em um controlador do MVC.</span><span class="sxs-lookup"><span data-stu-id="61a86-165">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="61a86-166">Para desabilitar as páginas de código de status, tente recuperar o <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> da coleção [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) da solicitação e desabilitar o recurso se ele estiver disponível:</span><span class="sxs-lookup"><span data-stu-id="61a86-166">To disable status code pages, attempt to retrieve the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> from the request's [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="61a86-167">Para usar uma sobrecarga <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que aponta para um ponto de extremidade dentro do aplicativo, crie um modo de exibição do MVC ou Razor Page para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="61a86-167">To use a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="61a86-168">Por exemplo, o modelo de aplicativo Razor Pages produz a seguinte página e classe de modelo de página:</span><span class="sxs-lookup"><span data-stu-id="61a86-168">For example, the Razor Pages app template produces the following page and page model class:</span></span>

<span data-ttu-id="61a86-169">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="61a86-169">*Error.cshtml*:</span></span>

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

<span data-ttu-id="61a86-170">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="61a86-170">*Error.cshtml.cs*:</span></span>

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

<span data-ttu-id="61a86-171">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="61a86-171">*Error.cshtml.cs*:</span></span>

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

## <a name="exception-handling-code"></a><span data-ttu-id="61a86-172">Código de tratamento de exceção</span><span class="sxs-lookup"><span data-stu-id="61a86-172">Exception-handling code</span></span>

<span data-ttu-id="61a86-173">Código em páginas de tratamento de exceção pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="61a86-173">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="61a86-174">Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.</span><span class="sxs-lookup"><span data-stu-id="61a86-174">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="61a86-175">Além disso, lembre-se que, depois que os cabeçalhos de resposta são enviados:</span><span class="sxs-lookup"><span data-stu-id="61a86-175">Also, be aware that once the headers for a response are sent:</span></span>

* <span data-ttu-id="61a86-176">O aplicativo já não consegue alterar o código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="61a86-176">The app can't change the response's status code.</span></span>
* <span data-ttu-id="61a86-177">As páginas de exceção ou manipuladores não podem ser executados.</span><span class="sxs-lookup"><span data-stu-id="61a86-177">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="61a86-178">A resposta deve ser concluída ou a conexão será anulada.</span><span class="sxs-lookup"><span data-stu-id="61a86-178">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="61a86-179">Tratamento de exceções do servidor</span><span class="sxs-lookup"><span data-stu-id="61a86-179">Server exception handling</span></span>

<span data-ttu-id="61a86-180">Além da lógica de tratamento de exceção no aplicativo, a [implementação do servidor](xref:fundamentals/servers/index) pode lidar com algumas exceções.</span><span class="sxs-lookup"><span data-stu-id="61a86-180">In addition to the exception handling logic in your app, the [server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="61a86-181">Se o servidor capturar uma exceção antes que os cabeçalhos de resposta sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="61a86-181">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="61a86-182">Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão.</span><span class="sxs-lookup"><span data-stu-id="61a86-182">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="61a86-183">As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="61a86-183">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="61a86-184">Qualquer exceção ocorrida é tratada pelo tratamento de exceção do servidor.</span><span class="sxs-lookup"><span data-stu-id="61a86-184">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="61a86-185">As páginas de erro personalizadas, o middleware de tratamento de exceção ou os filtros configurados não afetam esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="61a86-185">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="61a86-186">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="61a86-186">Startup exception handling</span></span>

<span data-ttu-id="61a86-187">Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="61a86-187">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="61a86-188">Usando o [Host da Web](xref:fundamentals/host/web-host), você pode [configurar como o host se comporta em resposta a erros durante a inicialização](xref:fundamentals/host/web-host#detailed-errors) usando as chaves `captureStartupErrors` e `detailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="61a86-188">Using [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="61a86-189">A hospedagem apenas poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta.</span><span class="sxs-lookup"><span data-stu-id="61a86-189">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="61a86-190">Se alguma associação falhar por qualquer motivo:</span><span class="sxs-lookup"><span data-stu-id="61a86-190">If any binding fails for any reason:</span></span>

* <span data-ttu-id="61a86-191">A camada de hospedagem registrará uma exceção crítica.</span><span class="sxs-lookup"><span data-stu-id="61a86-191">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="61a86-192">O processo dotnet falhará.</span><span class="sxs-lookup"><span data-stu-id="61a86-192">The dotnet process crashes.</span></span>
* <span data-ttu-id="61a86-193">Nenhuma página de erro será exibida quando o aplicativo estiver sendo executado no servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="61a86-193">No error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="61a86-194">Quando executado no [IIS](/iis) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="61a86-194">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="61a86-195">Para obter mais informações, consulte <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="61a86-195">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="61a86-196">Para obter informações sobre como solucionar problemas de inicialização com o Serviço de Aplicativo do Azure, consulte <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="61a86-196">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="61a86-197">Tratamento de erro do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="61a86-197">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="61a86-198">Os aplicativos [MVC](xref:mvc/overview) contêm algumas opções adicionais para o tratamento de erros, como configurar filtros de exceção e executar a validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="61a86-198">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="61a86-199">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="61a86-199">Exception filters</span></span>

<span data-ttu-id="61a86-200">Os filtros de exceção podem ser configurados globalmente ou por controlador ou por ação em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="61a86-200">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="61a86-201">Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro.</span><span class="sxs-lookup"><span data-stu-id="61a86-201">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="61a86-202">Esses filtros não são chamados de outra forma.</span><span class="sxs-lookup"><span data-stu-id="61a86-202">These filters aren't called otherwise.</span></span> <span data-ttu-id="61a86-203">Para saber mais, confira <xref:mvc/controllers/filters>.</span><span class="sxs-lookup"><span data-stu-id="61a86-203">To learn more, see <xref:mvc/controllers/filters>.</span></span>

> [!TIP]
> <span data-ttu-id="61a86-204">Os filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="61a86-204">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="61a86-205">Recomendamos o uso de middleware.</span><span class="sxs-lookup"><span data-stu-id="61a86-205">We recommend the use of middleware.</span></span> <span data-ttu-id="61a86-206">Use filtros somente quando você precisar fazer o tratamento de erro *de forma diferente* com base na ação de MVC escolhida.</span><span class="sxs-lookup"><span data-stu-id="61a86-206">Use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handle-model-state-errors"></a><span data-ttu-id="61a86-207">Lidar com erros de estado do modelo</span><span class="sxs-lookup"><span data-stu-id="61a86-207">Handle model state errors</span></span>

<span data-ttu-id="61a86-208">A [validação do modelo](xref:mvc/models/validation) ocorre antes da invocação de cada ação do controlador e é responsabilidade do método de ação inspecionar [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) e responder de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="61a86-208">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) and react appropriately.</span></span>

<span data-ttu-id="61a86-209">Alguns aplicativos optam por seguir uma convenção padrão para lidar com erros de [validação do modelo](xref:mvc/models/validation), caso em que um [filtro](xref:mvc/controllers/filters) pode ser um local adequado para implementar uma política como essa.</span><span class="sxs-lookup"><span data-stu-id="61a86-209">Some apps choose to follow a standard convention for dealing with [model validation](xref:mvc/models/validation) errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="61a86-210">É necessário testar o comportamento das ações com estados de modelo inválidos.</span><span class="sxs-lookup"><span data-stu-id="61a86-210">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="61a86-211">Para obter mais informações, consulte <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="61a86-211">For more information, see <xref:mvc/controllers/testing>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61a86-212">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="61a86-212">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
