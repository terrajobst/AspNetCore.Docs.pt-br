---
title: Ativação de middleware com um contêiner de terceiros no ASP.NET Core
author: guardrex
description: Saiba como usar o middleware fortemente tipado com a ativação baseada em alocador e um contêiner de terceiros no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/03/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 4bc99b4c336aba611287c9fbe03d4252f8abee5b
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561642"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="e0ad4-103">Ativação de middleware com um contêiner de terceiros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0ad4-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="e0ad4-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0ad4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e0ad4-105">Este artigo demonstra como usar o <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e o <xref:Microsoft.AspNetCore.Http.IMiddleware> como um ponto de extensibilidade para a ativação do [middleware](xref:fundamentals/middleware/index) com um contêiner de terceiros.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-105">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="e0ad4-106">Para obter informações introdutórias sobre `IMiddlewareFactory` e `IMiddleware`, confira <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="e0ad4-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e0ad4-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e0ad4-108">O aplicativo de exemplo demonstra a ativação do middleware por uma implementação de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="e0ad4-109">O exemplo usa o contêiner de DI (injeção de dependência) [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="e0ad4-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="e0ad4-110">A implementação do middleware do exemplo registra o valor fornecido por um parâmetro de cadeia de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="e0ad4-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="e0ad4-111">O middleware usa um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor da cadeia de consulta em um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="e0ad4-112">O aplicativo de exemplo usa o [Simple Injector](https://github.com/simpleinjector/SimpleInjector) simplesmente para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="e0ad4-113">O uso do Simple Injector não é um endosso.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="e0ad4-114">As abordagens de ativação do middleware descritas na documentação do Simple Injector e nos problemas do GitHub são recomendadas pelos mantenedores do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="e0ad4-115">Para obter mais informações, confira a [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e o [repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="e0ad4-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="e0ad4-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="e0ad4-116">IMiddlewareFactory</span></span>

<span data-ttu-id="e0ad4-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="e0ad4-118">No aplicativo de amostra, um alocador de middleware é implementado para criar uma instância de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="e0ad4-119">O alocador de middleware usa o contêiner do Simple Injector para resolver o middleware:</span><span class="sxs-lookup"><span data-stu-id="e0ad4-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="e0ad4-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="e0ad4-120">IMiddleware</span></span>

<span data-ttu-id="e0ad4-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="e0ad4-122">Middleware ativado por uma implementação de `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="e0ad4-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="e0ad4-123">Uma extensão é criada para o middleware (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="e0ad4-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="e0ad4-124">O `Startup.ConfigureServices` precisa executar várias tarefas:</span><span class="sxs-lookup"><span data-stu-id="e0ad4-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="e0ad4-125">Configure o contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="e0ad4-126">Registre o alocador e o middleware.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="e0ad4-127">Disponibilize o contexto do banco de dados do aplicativo por meio do contêiner do Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="e0ad4-127">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="e0ad4-128">O middleware é registrado no pipeline de processamento da solicitação em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e0ad4-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="e0ad4-129">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e0ad4-129">Additional resources</span></span>

* [<span data-ttu-id="e0ad4-130">Middleware</span><span class="sxs-lookup"><span data-stu-id="e0ad4-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="e0ad4-131">Ativação de middleware de fábrica</span><span class="sxs-lookup"><span data-stu-id="e0ad4-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="e0ad4-132">Repositório do GitHub do Simple Injector</span><span class="sxs-lookup"><span data-stu-id="e0ad4-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="e0ad4-133">Documentação do Simple Injector</span><span class="sxs-lookup"><span data-stu-id="e0ad4-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
