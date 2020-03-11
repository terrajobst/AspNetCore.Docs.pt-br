---
title: Ativação de middleware com um contêiner de terceiros no ASP.NET Core
author: rick-anderson
description: Saiba como usar o middleware fortemente tipado com a ativação baseada em alocador e um contêiner de terceiros no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a5c5bf6dff6ef795add075df932dd625129ef793
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663131"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="f239e-103">Ativação de middleware com um contêiner de terceiros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f239e-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f239e-104">Este artigo demonstra como usar o <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e o <xref:Microsoft.AspNetCore.Http.IMiddleware> como um ponto de extensibilidade para a ativação do [middleware](xref:fundamentals/middleware/index) com um contêiner de terceiros.</span><span class="sxs-lookup"><span data-stu-id="f239e-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="f239e-105">Para obter informações introdutórias sobre `IMiddlewareFactory` e `IMiddleware`, confira <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="f239e-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="f239e-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f239e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f239e-107">O aplicativo de exemplo demonstra a ativação do middleware por uma implementação de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="f239e-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="f239e-108">O exemplo usa o contêiner de DI (injeção de dependência) [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="f239e-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="f239e-109">A implementação do middleware do exemplo registra o valor fornecido por um parâmetro de cadeia de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="f239e-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="f239e-110">O middleware usa um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor da cadeia de consulta em um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="f239e-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="f239e-111">O aplicativo de exemplo usa o [Simple Injector](https://github.com/simpleinjector/SimpleInjector) simplesmente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="f239e-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="f239e-112">O uso do Simple Injector não é um endosso.</span><span class="sxs-lookup"><span data-stu-id="f239e-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="f239e-113">As abordagens de ativação do middleware descritas na documentação do Simple Injector e nos problemas do GitHub são recomendadas pelos mantenedores do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="f239e-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="f239e-114">Para obter mais informações, confira a [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e o [repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="f239e-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="f239e-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="f239e-115">IMiddlewareFactory</span></span>

<span data-ttu-id="f239e-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.</span><span class="sxs-lookup"><span data-stu-id="f239e-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="f239e-117">No aplicativo de amostra, um alocador de middleware é implementado para criar uma instância de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="f239e-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="f239e-118">O alocador de middleware usa o contêiner do Simple Injector para resolver o middleware:</span><span class="sxs-lookup"><span data-stu-id="f239e-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="f239e-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="f239e-119">IMiddleware</span></span>

<span data-ttu-id="f239e-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f239e-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="f239e-121">Middleware ativado por uma implementação de `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="f239e-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="f239e-122">Uma extensão é criada para o middleware (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f239e-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="f239e-123">O `Startup.ConfigureServices` precisa executar várias tarefas:</span><span class="sxs-lookup"><span data-stu-id="f239e-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="f239e-124">Configure o contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="f239e-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="f239e-125">Registre o alocador e o middleware.</span><span class="sxs-lookup"><span data-stu-id="f239e-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="f239e-126">Disponibilize o contexto do banco de dados do aplicativo por meio do contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="f239e-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f239e-127">O middleware é registrado no pipeline de processamento da solicitação em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f239e-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f239e-128">Este artigo demonstra como usar o <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e o <xref:Microsoft.AspNetCore.Http.IMiddleware> como um ponto de extensibilidade para a ativação do [middleware](xref:fundamentals/middleware/index) com um contêiner de terceiros.</span><span class="sxs-lookup"><span data-stu-id="f239e-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="f239e-129">Para obter informações introdutórias sobre `IMiddlewareFactory` e `IMiddleware`, confira <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="f239e-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="f239e-130">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f239e-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f239e-131">O aplicativo de exemplo demonstra a ativação do middleware por uma implementação de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="f239e-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="f239e-132">O exemplo usa o contêiner de DI (injeção de dependência) [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="f239e-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="f239e-133">A implementação do middleware do exemplo registra o valor fornecido por um parâmetro de cadeia de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="f239e-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="f239e-134">O middleware usa um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor da cadeia de consulta em um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="f239e-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="f239e-135">O aplicativo de exemplo usa o [Simple Injector](https://github.com/simpleinjector/SimpleInjector) simplesmente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="f239e-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="f239e-136">O uso do Simple Injector não é um endosso.</span><span class="sxs-lookup"><span data-stu-id="f239e-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="f239e-137">As abordagens de ativação do middleware descritas na documentação do Simple Injector e nos problemas do GitHub são recomendadas pelos mantenedores do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="f239e-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="f239e-138">Para obter mais informações, confira a [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e o [repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="f239e-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="f239e-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="f239e-139">IMiddlewareFactory</span></span>

<span data-ttu-id="f239e-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.</span><span class="sxs-lookup"><span data-stu-id="f239e-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="f239e-141">No aplicativo de amostra, um alocador de middleware é implementado para criar uma instância de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="f239e-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="f239e-142">O alocador de middleware usa o contêiner do Simple Injector para resolver o middleware:</span><span class="sxs-lookup"><span data-stu-id="f239e-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="f239e-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="f239e-143">IMiddleware</span></span>

<span data-ttu-id="f239e-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f239e-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="f239e-145">Middleware ativado por uma implementação de `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="f239e-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="f239e-146">Uma extensão é criada para o middleware (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="f239e-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="f239e-147">O `Startup.ConfigureServices` precisa executar várias tarefas:</span><span class="sxs-lookup"><span data-stu-id="f239e-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="f239e-148">Configure o contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="f239e-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="f239e-149">Registre o alocador e o middleware.</span><span class="sxs-lookup"><span data-stu-id="f239e-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="f239e-150">Disponibilize o contexto do banco de dados do aplicativo por meio do contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="f239e-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f239e-151">O middleware é registrado no pipeline de processamento da solicitação em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f239e-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f239e-152">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f239e-152">Additional resources</span></span>

* [<span data-ttu-id="f239e-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="f239e-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f239e-154">Ativação de middleware de fábrica</span><span class="sxs-lookup"><span data-stu-id="f239e-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="f239e-155">Repositório do GitHub do Simple Injector</span><span class="sxs-lookup"><span data-stu-id="f239e-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="f239e-156">Documentação do Simple Injector</span><span class="sxs-lookup"><span data-stu-id="f239e-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
