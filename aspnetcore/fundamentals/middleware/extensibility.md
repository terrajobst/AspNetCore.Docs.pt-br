---
title: Ativação de middleware baseada em alocador no ASP.NET Core
author: guardrex
description: Saiba como usar um middleware fortemente tipado com uma implementação de ativação baseada em alocador no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: b4d71c2c7f09acb58b73e84080e8574d77f8b326
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087009"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="d2a14-103">Ativação de middleware baseada em alocador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2a14-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="d2a14-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d2a14-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d2a14-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> é um ponto de extensibilidade para a ativação de [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="d2a14-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="d2a14-106">Os métodos de extensão <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> verificam se o tipo registrado de um middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="d2a14-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="d2a14-107">Se isso acontecer, a instância <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada no contêiner será usada para resolver a implementação <xref:Microsoft.AspNetCore.Http.IMiddleware> em vez de usar a lógica de ativação de middleware baseada em convenção.</span><span class="sxs-lookup"><span data-stu-id="d2a14-107">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="d2a14-108">O middleware é registrado como um [serviço com escopo ou transitório](xref:fundamentals/dependency-injection#service-lifetimes) no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2a14-108">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="d2a14-109">Benefícios:</span><span class="sxs-lookup"><span data-stu-id="d2a14-109">Benefits:</span></span>

* <span data-ttu-id="d2a14-110">Ativação por solicitação de cliente (injeção de serviços com escopo)</span><span class="sxs-lookup"><span data-stu-id="d2a14-110">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="d2a14-111">Tipagem forte de middleware</span><span class="sxs-lookup"><span data-stu-id="d2a14-111">Strong typing of middleware</span></span>

<span data-ttu-id="d2a14-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> é ativado por solicitação de cliente (conexão), de modo que os serviços com escopo possam ser injetados no construtor do middleware.</span><span class="sxs-lookup"><span data-stu-id="d2a14-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="d2a14-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2a14-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="d2a14-114">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="d2a14-114">IMiddleware</span></span>

<span data-ttu-id="d2a14-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2a14-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="d2a14-116">O método [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) manipula as solicitações e retorna uma <xref:System.Threading.Tasks.Task> que representa a execução do middleware.</span><span class="sxs-lookup"><span data-stu-id="d2a14-116">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="d2a14-117">Middleware ativado por convenção:</span><span class="sxs-lookup"><span data-stu-id="d2a14-117">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="d2a14-118">Middleware ativado por <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="d2a14-118">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="d2a14-119">As extensões são criadas para os middlewares:</span><span class="sxs-lookup"><span data-stu-id="d2a14-119">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="d2a14-120">Não é possível passar objetos para o middleware ativado por alocador com <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="d2a14-120">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="d2a14-121">O middleware ativado por alocador é adicionado ao contêiner interno em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d2a14-121">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="d2a14-122">Ambos os middlewares estão registrados no pipeline de processamento de solicitação em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d2a14-122">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="d2a14-123">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="d2a14-123">IMiddlewareFactory</span></span>

<span data-ttu-id="d2a14-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.</span><span class="sxs-lookup"><span data-stu-id="d2a14-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="d2a14-125">A implementação de alocador do middleware é registrada no contêiner como um serviço com escopo.</span><span class="sxs-lookup"><span data-stu-id="d2a14-125">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="d2a14-126">A implementação <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> padrão, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, é encontrada no pacote [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="d2a14-126">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d2a14-127">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d2a14-127">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
