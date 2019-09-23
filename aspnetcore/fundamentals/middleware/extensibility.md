---
title: Ativação de middleware baseada em alocador no ASP.NET Core
author: guardrex
description: Saiba como usar um middleware fortemente tipado com uma implementação de ativação baseada em alocador no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 17018d2dd20ed7b26bd0aa1095fa720a73f77261
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186951"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Ativação de middleware baseada em alocador no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> é um ponto de extensibilidade para a ativação de [middleware](xref:fundamentals/middleware/index).

Os métodos de extensão <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> verificam se o tipo registrado de um middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>. Se isso acontecer, a instância <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada no contêiner será usada para resolver a implementação <xref:Microsoft.AspNetCore.Http.IMiddleware> em vez de usar a lógica de ativação de middleware baseada em convenção. O middleware é registrado como um [serviço com escopo ou transitório](xref:fundamentals/dependency-injection#service-lifetimes) no contêiner de serviço do aplicativo.

Benefícios:

* Ativação por solicitação de cliente (injeção de serviços com escopo)
* Tipagem forte de middleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> é ativado por solicitação de cliente (conexão), de modo que os serviços com escopo possam ser injetados no construtor do middleware.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo. O método [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) manipula as solicitações e retorna uma <xref:System.Threading.Tasks.Task> que representa a execução do middleware.

Middleware ativado por convenção:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware ativado por <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

As extensões são criadas para os middlewares:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Não é possível passar objetos para o middleware ativado por alocador com <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

O middleware ativado por alocador é adicionado ao contêiner interno em `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Ambos os middlewares estão registrados no pipeline de processamento de solicitação em `Startup.Configure`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware. A implementação de alocador do middleware é registrada no contêiner como um serviço com escopo.

A implementação <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> padrão, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, é encontrada no pacote [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> é um ponto de extensibilidade para a ativação de [middleware](xref:fundamentals/middleware/index).

Os métodos de extensão <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> verificam se o tipo registrado de um middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>. Se isso acontecer, a instância <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada no contêiner será usada para resolver a implementação <xref:Microsoft.AspNetCore.Http.IMiddleware> em vez de usar a lógica de ativação de middleware baseada em convenção. O middleware é registrado como um [serviço com escopo ou transitório](xref:fundamentals/dependency-injection#service-lifetimes) no contêiner de serviço do aplicativo.

Benefícios:

* Ativação por solicitação de cliente (injeção de serviços com escopo)
* Tipagem forte de middleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> é ativado por solicitação de cliente (conexão), de modo que os serviços com escopo possam ser injetados no construtor do middleware.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo. O método [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) manipula as solicitações e retorna uma <xref:System.Threading.Tasks.Task> que representa a execução do middleware.

Middleware ativado por convenção:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware ativado por <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

As extensões são criadas para os middlewares:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Não é possível passar objetos para o middleware ativado por alocador com <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

O middleware ativado por alocador é adicionado ao contêiner interno em `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Ambos os middlewares estão registrados no pipeline de processamento de solicitação em `Startup.Configure`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware. A implementação de alocador do middleware é registrada no contêiner como um serviço com escopo.

A implementação <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> padrão, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, é encontrada no pacote [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
