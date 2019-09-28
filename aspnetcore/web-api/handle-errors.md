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
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Tratar erros em APIs da Web ASP.NET Core

Este artigo descreve como lidar e personalizar o tratamento de erros com as APIs da Web do ASP.NET Core.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Como baixar](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Página de exceção do desenvolvedor

A [página de exceção do desenvolvedor](xref:fundamentals/error-handling) é uma ferramenta útil para obter rastreamentos de pilha detalhados para erros do servidor. Ele usa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> para capturar exceções síncronas e assíncronas do pipeline HTTP e gerar respostas de erro. Para ilustrar, considere a seguinte ação do controlador:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

Execute o seguinte comando `curl` para testar a ação anterior:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

No ASP.NET Core 3,0 e posterior, a página de exceção do desenvolvedor exibirá uma resposta de texto sem formatação se o cliente não solicitar saída formatada em HTML. A saída a seguir é exibida:

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

Para exibir uma resposta formatada em HTML, defina o cabeçalho de solicitação HTTP `Accept` para o tipo de mídia `text/html`. Por exemplo:

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

Considere o seguinte trecho da resposta HTTP:

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

No ASP.NET Core 2,2 e anteriores, a página de exceção do desenvolvedor exibe uma resposta formatada em HTML. Por exemplo, considere o seguinte trecho da resposta HTTP:

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

A resposta formatada em HTML se torna útil durante o teste por meio de ferramentas como o postmaster. A captura de tela a seguir mostra o texto sem formatação e as respostas formatadas em HTML no postmaster:

![Teste de página de exceção do desenvolvedor no postmaster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**. Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção. Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.

## <a name="exception-handler"></a>Manipulador de exceção

Em ambientes que não são de desenvolvimento, o [middleware de manipulação de exceção](xref:fundamentals/error-handling) pode ser usado para produzir uma carga de erro:

1. No `Startup.Configure`, invoque <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> para usar o middleware:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Configure uma ação do `/error` controlador para responder à rota:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

A ação `Error` anterior envia uma carga compatível com [RFC7807](https://tools.ietf.org/html/rfc7807)para o cliente.

O middleware de manipulação de exceção também pode fornecer uma saída de negociação de conteúdo mais detalhada no ambiente de desenvolvimento local. Use as etapas a seguir para produzir um formato de carga consistente entre ambientes de desenvolvimento e produção:

1. No `Startup.Configure`, registre as instâncias de middleware de manipulação de exceção específicas do ambiente:

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

    No código anterior, o middleware é registrado com:

    * Uma rota do `/error-local-development` no ambiente de desenvolvimento.
    * Uma rota do `/error` em ambientes que não são de desenvolvimento.
    
1. Aplicar roteamento de atributo a ações do controlador:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>Usar exceções para modificar a resposta

O conteúdo da resposta pode ser modificado de fora do controlador. Na API Web do ASP.NET 4. x, uma maneira de fazer isso era usando <xref:System.Web.Http.HttpResponseException> o tipo. ASP.NET Core não inclui um tipo equivalente. O suporte `HttpResponseException` para pode ser adicionado com as seguintes etapas:

1. Crie um tipo de exceção conhecido chamado `HttpResponseException`:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Crie um filtro de ação `HttpResponseExceptionFilter`chamado:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. No `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Resposta de erro de falha na validação

Para controladores de API Web, o MVC responde <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> com um tipo de resposta quando a validação do modelo falha. O MVC usa os resultados <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> de para construir a resposta de erro para uma falha de validação. O exemplo a seguir usa a fábrica para alterar o tipo de resposta <xref:Microsoft.AspNetCore.Mvc.SerializableError> padrão `Startup.ConfigureServices`para em:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>Resposta de erro do cliente

Um *resultado de erro* é definido como resultado com um código de status HTTP de 400 ou superior. Para controladores de API Web, o MVC transforma um resultado de erro em um resultado <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>com.

::: moniker range=">= aspnetcore-3.0"

A resposta de erro pode ser configurada de uma das seguintes maneiras:

1. [Implementar ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [Usar ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Implementar ProblemDetailsFactory

O MVC `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` usa para produzir todas as <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> instâncias <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>do e do. Isso inclui respostas de erro de cliente, respostas de erro de falha `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` de <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> validação e os métodos auxiliares and.

Para personalizar a resposta dos detalhes do problema, registre uma implementação `ProblemDetailsFactory` personalizada `Startup.ConfigureServices`do no:

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

A resposta de erro pode ser configurada conforme descrito na seção [usar ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>Usar ApiBehaviorOptions. ClientErrorMapping

Use a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> para configurar o conteúdo da resposta `ProblemDetails`. Por exemplo, o código a seguir `Startup.ConfigureServices` em atualiza `type` a propriedade para 404 respostas:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
