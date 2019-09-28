---
title: Tratar erros em APIs da Web ASP.NET Core
author: pranavkm
description: Saiba mais sobre o tratamento de erros com as APIs da Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/27/2019
uid: web-api/handle-errors
ms.openlocfilehash: dc21d4b2cf096b8d38b0a24d739e6874186004e7
ms.sourcegitcommit: 5d25a7f22c50ca6fdd0f8ecd8e525822e1b35b7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2019
ms.locfileid: "71551738"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="e108f-103">Tratar erros em APIs da Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e108f-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="e108f-104">Este artigo descreve como lidar e personalizar o tratamento de erros com as APIs da Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e108f-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="e108f-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e108f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="e108f-106">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="e108f-106">Developer Exception Page</span></span>

<span data-ttu-id="e108f-107">A [página de exceção do desenvolvedor](xref:fundamentals/error-handling) é uma ferramenta útil para obter rastreamentos de pilha detalhados para erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="e108f-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="e108f-108">Ele usa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> para capturar exceções síncronas e assíncronas do pipeline HTTP e gerar respostas de erro.</span><span class="sxs-lookup"><span data-stu-id="e108f-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="e108f-109">Para ilustrar, considere a seguinte ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="e108f-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="e108f-110">Execute o seguinte comando `curl` para testar a ação anterior:</span><span class="sxs-lookup"><span data-stu-id="e108f-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e108f-111">No ASP.NET Core 3,0 e posterior, a página de exceção do desenvolvedor exibirá uma resposta de texto sem formatação se o cliente não solicitar saída formatada em HTML.</span><span class="sxs-lookup"><span data-stu-id="e108f-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="e108f-112">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="e108f-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="e108f-113">Para exibir uma resposta formatada em HTML, defina o cabeçalho de solicitação HTTP `Accept` para o tipo de mídia `text/html`.</span><span class="sxs-lookup"><span data-stu-id="e108f-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="e108f-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e108f-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="e108f-115">Considere o seguinte trecho da resposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="e108f-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e108f-116">No ASP.NET Core 2,2 e anteriores, a página de exceção do desenvolvedor exibe uma resposta formatada em HTML.</span><span class="sxs-lookup"><span data-stu-id="e108f-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="e108f-117">Por exemplo, considere o seguinte trecho da resposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="e108f-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e108f-118">A resposta formatada em HTML se torna útil durante o teste por meio de ferramentas como o postmaster.</span><span class="sxs-lookup"><span data-stu-id="e108f-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="e108f-119">A captura de tela a seguir mostra o texto sem formatação e as respostas formatadas em HTML no postmaster:</span><span class="sxs-lookup"><span data-stu-id="e108f-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Teste de página de exceção do desenvolvedor no postmaster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="e108f-121">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="e108f-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="e108f-122">Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção.</span><span class="sxs-lookup"><span data-stu-id="e108f-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="e108f-123">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e108f-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="e108f-124">Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="e108f-124">Exception handler</span></span>

<span data-ttu-id="e108f-125">Em ambientes que não são de desenvolvimento, o [middleware de manipulação de exceção](xref:fundamentals/error-handling) pode ser usado para produzir uma carga de erro:</span><span class="sxs-lookup"><span data-stu-id="e108f-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="e108f-126">No `Startup.Configure`, invoque <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> para usar o middleware:</span><span class="sxs-lookup"><span data-stu-id="e108f-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="e108f-127">Configure uma ação do `/error` controlador para responder à rota:</span><span class="sxs-lookup"><span data-stu-id="e108f-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="e108f-128">A ação `Error` anterior envia uma carga compatível com [RFC7807](https://tools.ietf.org/html/rfc7807)para o cliente.</span><span class="sxs-lookup"><span data-stu-id="e108f-128">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="e108f-129">O middleware de manipulação de exceção também pode fornecer uma saída de negociação de conteúdo mais detalhada no ambiente de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="e108f-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="e108f-130">Use as etapas a seguir para produzir um formato de carga consistente entre ambientes de desenvolvimento e produção:</span><span class="sxs-lookup"><span data-stu-id="e108f-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="e108f-131">No `Startup.Configure`, registre as instâncias de middleware de manipulação de exceção específicas do ambiente:</span><span class="sxs-lookup"><span data-stu-id="e108f-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="e108f-132">No código anterior, o middleware é registrado com:</span><span class="sxs-lookup"><span data-stu-id="e108f-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="e108f-133">Uma rota do `/error-local-development` no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e108f-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="e108f-134">Uma rota do `/error` em ambientes que não são de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e108f-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="e108f-135">Aplicar roteamento de atributo a ações do controlador:</span><span class="sxs-lookup"><span data-stu-id="e108f-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="e108f-136">Usar exceções para modificar a resposta</span><span class="sxs-lookup"><span data-stu-id="e108f-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="e108f-137">O conteúdo da resposta pode ser modificado de fora do controlador.</span><span class="sxs-lookup"><span data-stu-id="e108f-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="e108f-138">Na API Web do ASP.NET 4. x, uma maneira de fazer isso era usando <xref:System.Web.Http.HttpResponseException> o tipo.</span><span class="sxs-lookup"><span data-stu-id="e108f-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="e108f-139">ASP.NET Core não inclui um tipo equivalente.</span><span class="sxs-lookup"><span data-stu-id="e108f-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="e108f-140">O suporte `HttpResponseException` para pode ser adicionado com as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="e108f-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="e108f-141">Crie um tipo de exceção conhecido chamado `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="e108f-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="e108f-142">Crie um filtro de ação `HttpResponseExceptionFilter`chamado:</span><span class="sxs-lookup"><span data-stu-id="e108f-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="e108f-143">No `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros:</span><span class="sxs-lookup"><span data-stu-id="e108f-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="e108f-144">Resposta de erro de falha na validação</span><span class="sxs-lookup"><span data-stu-id="e108f-144">Validation failure error response</span></span>

<span data-ttu-id="e108f-145">Para controladores de API Web, o MVC responde <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> com um tipo de resposta quando a validação do modelo falha.</span><span class="sxs-lookup"><span data-stu-id="e108f-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="e108f-146">O MVC usa os resultados <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> de para construir a resposta de erro para uma falha de validação.</span><span class="sxs-lookup"><span data-stu-id="e108f-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="e108f-147">O exemplo a seguir usa a fábrica para alterar o tipo de resposta <xref:Microsoft.AspNetCore.Mvc.SerializableError> padrão `Startup.ConfigureServices`para em:</span><span class="sxs-lookup"><span data-stu-id="e108f-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="e108f-148">Resposta de erro do cliente</span><span class="sxs-lookup"><span data-stu-id="e108f-148">Client error response</span></span>

<span data-ttu-id="e108f-149">Um *resultado de erro* é definido como resultado com um código de status HTTP de 400 ou superior.</span><span class="sxs-lookup"><span data-stu-id="e108f-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="e108f-150">Para controladores de API Web, o MVC transforma um resultado de erro em um resultado <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>com.</span><span class="sxs-lookup"><span data-stu-id="e108f-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e108f-151">A resposta de erro pode ser configurada de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="e108f-151">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="e108f-152">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="e108f-152">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="e108f-153">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="e108f-153">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="e108f-154">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="e108f-154">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="e108f-155">O MVC `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` usa para produzir todas as <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> instâncias <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>do e do.</span><span class="sxs-lookup"><span data-stu-id="e108f-155">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="e108f-156">Isso inclui respostas de erro de cliente, respostas de erro de falha `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` de <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> validação e os métodos auxiliares and.</span><span class="sxs-lookup"><span data-stu-id="e108f-156">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="e108f-157">Para personalizar a resposta dos detalhes do problema, registre uma implementação `ProblemDetailsFactory` personalizada `Startup.ConfigureServices`do no:</span><span class="sxs-lookup"><span data-stu-id="e108f-157">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e108f-158">A resposta de erro pode ser configurada conforme descrito na seção [usar ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="e108f-158">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="e108f-159">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="e108f-159">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="e108f-160">Use a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> para configurar o conteúdo da resposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="e108f-160">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="e108f-161">Por exemplo, o código a seguir `Startup.ConfigureServices` em atualiza `type` a propriedade para 404 respostas:</span><span class="sxs-lookup"><span data-stu-id="e108f-161">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
