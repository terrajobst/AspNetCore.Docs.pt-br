---
title: Tratar erros em APIs da Web ASP.NET Core
author: pranavkm
description: Saiba mais sobre o tratamento de erros com as APIs da Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/18/2019
uid: web-api/handle-errors
ms.openlocfilehash: bf8229d37ef15c42b5d7bb4a12737ac65d7b753c
ms.sourcegitcommit: a11f09c10ef3d4eeab7ae9ce993e7f30427741c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150904"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="a127c-103">Tratar erros em APIs da Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a127c-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="a127c-104">Este artigo descreve como lidar e personalizar o tratamento de erros com as APIs da Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a127c-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a127c-105">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="a127c-105">Developer Exception Page</span></span>

<span data-ttu-id="a127c-106">A [página de exceção do desenvolvedor](xref:fundamentals/error-handling) é uma ferramenta útil para obter rastreamentos de pilha detalhados para erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="a127c-106">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a127c-107">A página de exceção do desenvolvedor exibirá uma resposta de texto sem formatação se o cliente não aceitar a saída formatada em HTML:</span><span class="sxs-lookup"><span data-stu-id="a127c-107">The Developer Exception Page displays a plain-text response if the client doesn't accept HTML-formatted output:</span></span>

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="a127c-108">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="a127c-108">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a127c-109">Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção.</span><span class="sxs-lookup"><span data-stu-id="a127c-109">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="a127c-110">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a127c-110">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="a127c-111">Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="a127c-111">Exception handler</span></span>

<span data-ttu-id="a127c-112">Em ambientes que não são de desenvolvimento, o [middleware de manipulação de exceção](xref:fundamentals/error-handling) pode ser usado para produzir uma carga de erro:</span><span class="sxs-lookup"><span data-stu-id="a127c-112">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="a127c-113">No `Startup.Configure`, invoque <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> para usar o middleware:</span><span class="sxs-lookup"><span data-stu-id="a127c-113">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

1. <span data-ttu-id="a127c-114">Configure uma ação do `/error` controlador para responder à rota:</span><span class="sxs-lookup"><span data-stu-id="a127c-114">Configure a controller action to respond to the `/error` route:</span></span>

    ```csharp
    [ApiController]
    public class ErrorController : ControllerBase
    {
        [Route("/error")]
        public IActionResult Error() => Problem();
    }
    ```

<span data-ttu-id="a127c-115">A ação `Error` anterior envia uma carga compatível com [RFC7807](https://tools.ietf.org/html/rfc7807)para o cliente.</span><span class="sxs-lookup"><span data-stu-id="a127c-115">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="a127c-116">O middleware de manipulação de exceção também pode fornecer uma saída de negociação de conteúdo mais detalhada no ambiente de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="a127c-116">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="a127c-117">Use as etapas a seguir para produzir um formato de carga consistente entre ambientes de desenvolvimento e produção:</span><span class="sxs-lookup"><span data-stu-id="a127c-117">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="a127c-118">No `Startup.Configure`, registre as instâncias de middleware de manipulação de exceção específicas do ambiente:</span><span class="sxs-lookup"><span data-stu-id="a127c-118">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

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

    <span data-ttu-id="a127c-119">No código anterior, o middleware é registrado com:</span><span class="sxs-lookup"><span data-stu-id="a127c-119">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="a127c-120">Uma rota do `/error-local-development` no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a127c-120">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="a127c-121">Uma rota do `/error` em ambientes que não são de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a127c-121">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="a127c-122">Aplicar roteamento de atributo a ações do controlador:</span><span class="sxs-lookup"><span data-stu-id="a127c-122">Apply attribute routing to controller actions:</span></span>

    ```csharp
    [ApiController]
    public class ErrorController : ControllerBase
    {
        [Route("/error-local-development")]
        public IActionResult ErrorLocalDevelopment(
            [FromServices] IWebHostEnvironment webHostEnvironment)
        {
            if (!webHostEnvironment.IsDevelopment())
            {
                throw new InvalidOperationException(
                    "This shouldn't be invoked in non-development environments.");
            }
    
            var context = HttpContext.Features.Get<IExceptionHandlerFeature>();

            return Problem(
                detail: context.Error.StackTrace,
                title: context.Error.Message);
        }
    
        [Route("/error")]
        public IActionResult Error() => Problem();
    }
    ```

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="a127c-123">Usar exceções para modificar a resposta</span><span class="sxs-lookup"><span data-stu-id="a127c-123">Use exceptions to modify the response</span></span>

<span data-ttu-id="a127c-124">O conteúdo da resposta pode ser modificado de fora do controlador.</span><span class="sxs-lookup"><span data-stu-id="a127c-124">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="a127c-125">Na API Web do ASP.NET 4. x, uma maneira de fazer isso era usando <xref:System.Web.Http.HttpResponseException> o tipo.</span><span class="sxs-lookup"><span data-stu-id="a127c-125">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="a127c-126">ASP.NET Core não inclui um tipo equivalente.</span><span class="sxs-lookup"><span data-stu-id="a127c-126">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="a127c-127">O suporte `HttpResponseException` para pode ser adicionado com as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="a127c-127">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="a127c-128">Crie um tipo de exceção conhecido chamado `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="a127c-128">Create a well-known exception type named `HttpResponseException`:</span></span>

    ```csharp
    public class HttpResponseException : Exception
    {
        public int Status { get; set; } = 500;
    
        public object Value { get; set; }
    }
    ```

1. <span data-ttu-id="a127c-129">Crie um filtro de ação `HttpResponseExceptionFilter`chamado:</span><span class="sxs-lookup"><span data-stu-id="a127c-129">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    ```csharp
    public class HttpResponseExceptionFilter : IActionFilter, IOrderedFilter
    {
        public int Order { get; set; } = int.MaxValue - 10;
    
        public void OnActionExecuting(ActionExecutingContext context) {}
    
        public void OnActionExecuted(ActionExecutedContext context)
        {
            if (context.Exception is HttpResponseException exception)
            {
                context.Result = new ObjectResult(exception.Value)
                {
                    Status = exception.Status,
                };
                context.ExceptionHandled = true;
            }
        }
    }
    ```

1. <span data-ttu-id="a127c-130">No `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros:</span><span class="sxs-lookup"><span data-stu-id="a127c-130">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers(options => 
            options.Filters.Add(new HttpResponseExceptionFilter()));
    }
    ```

## <a name="validation-failure-error-response"></a><span data-ttu-id="a127c-131">Resposta de erro de falha na validação</span><span class="sxs-lookup"><span data-stu-id="a127c-131">Validation failure error response</span></span>

<span data-ttu-id="a127c-132">Para controladores de API Web, o MVC responde <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> com um tipo de resposta quando a validação do modelo falha.</span><span class="sxs-lookup"><span data-stu-id="a127c-132">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="a127c-133">O MVC usa os resultados <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> de para construir a resposta de erro para uma falha de validação.</span><span class="sxs-lookup"><span data-stu-id="a127c-133">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="a127c-134">O exemplo a seguir usa a fábrica para alterar o tipo de resposta <xref:Microsoft.AspNetCore.Mvc.SerializableError> padrão `Startup.ConfigureServices`para em:</span><span class="sxs-lookup"><span data-stu-id="a127c-134">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

services.Configure<ApiBehaviorOptions>(options =>
{
    options.InvalidModelStateResponseFactory = context =>
    {
        var result = new BadRequestObjectResult(context.ModelState);

        // TODO: add `using using System.Net.Mime;` to resolve MediaTypeNames
        result.ContentTypes.Add(MediaTypeNames.Application.Json);
        result.ContentTypes.Add(MediaTypeNames.Application.Xml);

        return result;
    };
});
```

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="a127c-135">Resposta de erro do cliente</span><span class="sxs-lookup"><span data-stu-id="a127c-135">Client error response</span></span>

<span data-ttu-id="a127c-136">Um *resultado de erro* é definido como resultado com um código de status HTTP de 400 ou superior.</span><span class="sxs-lookup"><span data-stu-id="a127c-136">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="a127c-137">Para controladores de API Web, o MVC transforma um resultado de erro em um resultado <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>com.</span><span class="sxs-lookup"><span data-stu-id="a127c-137">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a127c-138">A resposta de erro pode ser configurada de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="a127c-138">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="a127c-139">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="a127c-139">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="a127c-140">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="a127c-140">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="a127c-141">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="a127c-141">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="a127c-142">O MVC `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` usa para produzir todas as <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> instâncias <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>do e do.</span><span class="sxs-lookup"><span data-stu-id="a127c-142">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="a127c-143">Isso inclui respostas de erro de cliente, respostas de erro de falha `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` de <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> validação e os métodos auxiliares and.</span><span class="sxs-lookup"><span data-stu-id="a127c-143">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="a127c-144">Para personalizar a resposta dos detalhes do problema, registre uma implementação `ProblemDetailsFactory` personalizada `Startup.ConfigureServices`do no:</span><span class="sxs-lookup"><span data-stu-id="a127c-144">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a127c-145">A resposta de erro pode ser configurada conforme descrito na seção [usar ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="a127c-145">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="a127c-146">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="a127c-146">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="a127c-147">Use a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> para configurar o conteúdo da resposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="a127c-147">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="a127c-148">Por exemplo, o código a seguir atualiza a propriedade `type` para respostas 404:</span><span class="sxs-lookup"><span data-stu-id="a127c-148">For example, the following code updates the `type` property for 404 responses:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
