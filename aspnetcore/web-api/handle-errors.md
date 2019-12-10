---
title: Tratar erros em APIs da Web ASP.NET Core
author: pranavkm
description: Saiba mais sobre o tratamento de erros com as APIs da Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/10/2019
uid: web-api/handle-errors
ms.openlocfilehash: c2dbc47b4495b7187aefbc62eb6d2f0c9683c2da
ms.sourcegitcommit: 29ace642ca0e1f0b48a18d66de266d8811df2b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74987824"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="630b8-103">Tratar erros em APIs da Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="630b8-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="630b8-104">Este artigo descreve como lidar e personalizar o tratamento de erros com as APIs da Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="630b8-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="630b8-105">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="630b8-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="630b8-106">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="630b8-106">Developer Exception Page</span></span>

<span data-ttu-id="630b8-107">A [página de exceção do desenvolvedor](xref:fundamentals/error-handling) é uma ferramenta útil para obter rastreamentos de pilha detalhados para erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="630b8-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="630b8-108">Ele usa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> capturar exceções síncronas e assíncronas do pipeline HTTP e gerar respostas de erro.</span><span class="sxs-lookup"><span data-stu-id="630b8-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="630b8-109">Para ilustrar, considere a seguinte ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="630b8-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="630b8-110">Execute o seguinte comando `curl` para testar a ação anterior:</span><span class="sxs-lookup"><span data-stu-id="630b8-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="630b8-111">No ASP.NET Core 3,0 e posterior, a página de exceção do desenvolvedor exibirá uma resposta de texto sem formatação se o cliente não solicitar saída formatada em HTML.</span><span class="sxs-lookup"><span data-stu-id="630b8-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="630b8-112">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="630b8-112">The following output appears:</span></span>

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

<span data-ttu-id="630b8-113">Para exibir uma resposta formatada em HTML, defina o `Accept` cabeçalho de solicitação HTTP para o tipo de mídia `text/html`.</span><span class="sxs-lookup"><span data-stu-id="630b8-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="630b8-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="630b8-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="630b8-115">Considere o seguinte trecho da resposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="630b8-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="630b8-116">No ASP.NET Core 2,2 e anteriores, a página de exceção do desenvolvedor exibe uma resposta formatada em HTML.</span><span class="sxs-lookup"><span data-stu-id="630b8-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="630b8-117">Por exemplo, considere o seguinte trecho da resposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="630b8-117">For example, consider the following excerpt from the HTTP response:</span></span>

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

<span data-ttu-id="630b8-118">A resposta formatada em HTML se torna útil durante o teste por meio de ferramentas como o postmaster.</span><span class="sxs-lookup"><span data-stu-id="630b8-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="630b8-119">A captura de tela a seguir mostra o texto sem formatação e as respostas formatadas em HTML no postmaster:</span><span class="sxs-lookup"><span data-stu-id="630b8-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Teste de página de exceção do desenvolvedor no postmaster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="630b8-121">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="630b8-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="630b8-122">Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção.</span><span class="sxs-lookup"><span data-stu-id="630b8-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="630b8-123">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="630b8-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="630b8-124">Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="630b8-124">Exception handler</span></span>

<span data-ttu-id="630b8-125">Em ambientes que não são de desenvolvimento, o [middleware de manipulação de exceção](xref:fundamentals/error-handling) pode ser usado para produzir uma carga de erro:</span><span class="sxs-lookup"><span data-stu-id="630b8-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="630b8-126">Em `Startup.Configure`, invoque <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> para usar o middleware:</span><span class="sxs-lookup"><span data-stu-id="630b8-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="630b8-127">Configure uma ação do controlador para responder à `/error` rota:</span><span class="sxs-lookup"><span data-stu-id="630b8-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="630b8-128">A ação de `Error` anterior envia uma carga compatível com [RFC 7807](https://tools.ietf.org/html/rfc7807)para o cliente.</span><span class="sxs-lookup"><span data-stu-id="630b8-128">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="630b8-129">O middleware de manipulação de exceção também pode fornecer uma saída de negociação de conteúdo mais detalhada no ambiente de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="630b8-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="630b8-130">Use as etapas a seguir para produzir um formato de carga consistente entre ambientes de desenvolvimento e produção:</span><span class="sxs-lookup"><span data-stu-id="630b8-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="630b8-131">Em `Startup.Configure`, registre as instâncias de middleware de manipulação de exceção específicas do ambiente:</span><span class="sxs-lookup"><span data-stu-id="630b8-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

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

    <span data-ttu-id="630b8-132">No código anterior, o middleware é registrado com:</span><span class="sxs-lookup"><span data-stu-id="630b8-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="630b8-133">Uma rota de `/error-local-development` no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="630b8-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="630b8-134">Uma rota de `/error` em ambientes que não são de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="630b8-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="630b8-135">Aplicar roteamento de atributo a ações do controlador:</span><span class="sxs-lookup"><span data-stu-id="630b8-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="630b8-136">Usar exceções para modificar a resposta</span><span class="sxs-lookup"><span data-stu-id="630b8-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="630b8-137">O conteúdo da resposta pode ser modificado de fora do controlador.</span><span class="sxs-lookup"><span data-stu-id="630b8-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="630b8-138">Na API Web do ASP.NET 4. x, uma maneira de fazer isso era usando o tipo de <xref:System.Web.Http.HttpResponseException>.</span><span class="sxs-lookup"><span data-stu-id="630b8-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="630b8-139">ASP.NET Core não inclui um tipo equivalente.</span><span class="sxs-lookup"><span data-stu-id="630b8-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="630b8-140">O suporte para `HttpResponseException` pode ser adicionado com as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="630b8-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="630b8-141">Crie um tipo de exceção conhecido chamado `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="630b8-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="630b8-142">Crie um filtro de ação chamado `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="630b8-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="630b8-143">Em `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros:</span><span class="sxs-lookup"><span data-stu-id="630b8-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="630b8-144">Resposta de erro de falha na validação</span><span class="sxs-lookup"><span data-stu-id="630b8-144">Validation failure error response</span></span>

<span data-ttu-id="630b8-145">Para controladores de API Web, o MVC responde com um tipo de resposta <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> quando a validação do modelo falha.</span><span class="sxs-lookup"><span data-stu-id="630b8-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="630b8-146">O MVC usa os resultados de <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> para construir a resposta de erro para uma falha de validação.</span><span class="sxs-lookup"><span data-stu-id="630b8-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="630b8-147">O exemplo a seguir usa a fábrica para alterar o tipo de resposta padrão para <xref:Microsoft.AspNetCore.Mvc.SerializableError> em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="630b8-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="630b8-148">Resposta de erro do cliente</span><span class="sxs-lookup"><span data-stu-id="630b8-148">Client error response</span></span>

<span data-ttu-id="630b8-149">Um *resultado de erro* é definido como resultado com um código de status HTTP de 400 ou superior.</span><span class="sxs-lookup"><span data-stu-id="630b8-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="630b8-150">Para controladores de API Web, o MVC transforma um resultado de erro em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="630b8-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="630b8-151">ASP.NET Core 2,1 gera uma resposta de detalhes do problema que é quase compatível com RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="630b8-151">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="630b8-152">Se a conformidade com o percentual de 100 for importante, atualize o projeto para ASP.NET Core 2,2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="630b8-152">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="630b8-153">A resposta de erro pode ser configurada de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="630b8-153">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="630b8-154">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="630b8-154">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="630b8-155">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="630b8-155">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="630b8-156">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="630b8-156">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="630b8-157">O MVC usa `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` para produzir todas as instâncias de <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> e <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="630b8-157">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="630b8-158">Isso inclui respostas de erro de cliente, respostas de erro de falha de validação e os métodos auxiliares `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem>.</span><span class="sxs-lookup"><span data-stu-id="630b8-158">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="630b8-159">Para personalizar a resposta dos detalhes do problema, registre uma implementação personalizada do `ProblemDetailsFactory` no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="630b8-159">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="630b8-160">A resposta de erro pode ser configurada conforme descrito na seção [usar ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="630b8-160">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="630b8-161">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="630b8-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="630b8-162">Use a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> para configurar o conteúdo da resposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="630b8-162">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="630b8-163">Por exemplo, o código a seguir no `Startup.ConfigureServices` atualiza a propriedade `type` para respostas de 404:</span><span class="sxs-lookup"><span data-stu-id="630b8-163">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
