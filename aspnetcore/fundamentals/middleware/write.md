---
title: Escrever middleware do ASP.NET Core personalizado
author: rick-anderson
description: Saiba como escrever middleware do ASP.NET Core personalizado.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889161"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="67a23-103">Escrever middleware do ASP.NET Core personalizado</span><span class="sxs-lookup"><span data-stu-id="67a23-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="67a23-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="67a23-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="67a23-105">O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas.</span><span class="sxs-lookup"><span data-stu-id="67a23-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="67a23-106">O ASP.NET Core fornece um rico conjunto de componentes de middleware internos, mas em alguns cenários, talvez seja conveniente que você escreva um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="67a23-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="67a23-107">Classe de middleware</span><span class="sxs-lookup"><span data-stu-id="67a23-107">Middleware class</span></span>

<span data-ttu-id="67a23-108">O middleware geralmente é encapsulado em uma classe e exposto com um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="67a23-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="67a23-109">Considere o middleware a seguir, que define a cultura para a solicitação atual de uma cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="67a23-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="67a23-110">O código de exemplo anterior é usado para demonstrar a criação de um componente de middleware.</span><span class="sxs-lookup"><span data-stu-id="67a23-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="67a23-111">Para suporte de localização interna do ASP.NET Core, veja <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="67a23-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="67a23-112">Você pode testar o middleware ao transmitir a cultura.</span><span class="sxs-lookup"><span data-stu-id="67a23-112">You can test the middleware by passing in the culture.</span></span> <span data-ttu-id="67a23-113">Por exemplo, `http://localhost:7997/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="67a23-113">For example, `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="67a23-114">O código a seguir move o delegado de middleware para uma classe:</span><span class="sxs-lookup"><span data-stu-id="67a23-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="67a23-115">O nome do método `Task` de middleware precisa ser `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="67a23-115">The middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="67a23-116">No ASP.NET Core 2.0 ou posterior, o nome pode ser `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="67a23-116">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

::: moniker-end

## <a name="middleware-extension-method"></a><span data-ttu-id="67a23-117">Método de extensão de middleware</span><span class="sxs-lookup"><span data-stu-id="67a23-117">Middleware extension method</span></span>

<span data-ttu-id="67a23-118">O seguinte método de extensão expõe o middleware por meio do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="67a23-118">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="67a23-119">O código a seguir chama o middleware de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="67a23-119">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="67a23-120">O middleware deve seguir o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) ao expor suas dependências em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="67a23-120">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="67a23-121">O middleware é construído uma vez por *tempo de vida do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="67a23-121">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="67a23-122">Consulte a seção [Dependências por solicitação](#per-request-dependencies) se você precisar compartilhar serviços com middleware dentro de uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="67a23-122">See the [Per-request dependencies](#per-request-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="67a23-123">Os componentes de middleware podem resolver suas dependências, utilizando a [DI (injeção de dependência)](xref:fundamentals/dependency-injection) por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="67a23-123">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="67a23-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) também pode aceitar parâmetros adicionais diretamente.</span><span class="sxs-lookup"><span data-stu-id="67a23-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-dependencies"></a><span data-ttu-id="67a23-125">Dependências de pré-solicitação</span><span class="sxs-lookup"><span data-stu-id="67a23-125">Per-request dependencies</span></span>

<span data-ttu-id="67a23-126">Uma vez que o middleware é construído durante a inicialização do aplicativo, e não por solicitação, os serviços de tempo de vida *com escopo* usados pelos construtores do middleware não são compartilhados com outros tipos de dependência inseridos durante cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="67a23-126">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="67a23-127">Se você tiver que compartilhar um serviço *com escopo* entre seu serviço de middleware e serviços de outros tipos, adicione esses serviços à assinatura do método `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="67a23-127">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="67a23-128">O método `Invoke` pode aceitar parâmetros adicionais que são preenchidos pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="67a23-128">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="67a23-129">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="67a23-129">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
