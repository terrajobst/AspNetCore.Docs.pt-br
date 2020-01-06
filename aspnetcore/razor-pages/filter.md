---
title: Métodos de filtro para Páginas Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como criar métodos de filtro para as Páginas Razor no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 12/28/2019
uid: razor-pages/filter
ms.openlocfilehash: 02771219454556b236080c2668243f788693b2c1
ms.sourcegitcommit: 077b45eceae044475f04c1d7ef2d153d7c0515a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2019
ms.locfileid: "75542710"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="68367-103">Métodos de filtro para Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68367-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68367-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68367-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="68367-105">Os filtros de página Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Páginas Razor executem código antes e após a execução de um manipulador de Página Razor.</span><span class="sxs-lookup"><span data-stu-id="68367-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="68367-106">Os filtros de página Razor são semelhantes aos [filtros de ação de MVC do ASP.NET Core](xref:mvc/controllers/filters#action-filters), exceto que eles não podem ser aplicados aos métodos do manipulador de cada página individual.</span><span class="sxs-lookup"><span data-stu-id="68367-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="68367-107">Filtros de página Razor:</span><span class="sxs-lookup"><span data-stu-id="68367-107">Razor Page filters:</span></span>

* <span data-ttu-id="68367-108">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="68367-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="68367-109">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="68367-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="68367-110">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="68367-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="68367-111">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="68367-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="68367-112">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="68367-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="68367-113">Pode haver dependências de Construtor preenchidas pela [injeção de dependência](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="68367-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="68367-114">Para obter mais informações, [consulte](/aspnet/core/mvc/controllers/filters#servicefilterattribute) [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)e.</span><span class="sxs-lookup"><span data-stu-id="68367-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="68367-115">O código pode ser executado antes que um método de manipulador seja executado usando o construtor de página ou middleware, mas somente filtros de página Razor têm acesso a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="68367-115">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>.</span></span> <span data-ttu-id="68367-116">Os filtros têm um <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parâmetro derivado, que fornece acesso ao `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="68367-116">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="68367-117">Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.</span><span class="sxs-lookup"><span data-stu-id="68367-117">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="68367-118">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68367-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="68367-119">Os filtros de página Razor fornecem os métodos a seguir, que podem ser aplicados globalmente ou no nível da página:</span><span class="sxs-lookup"><span data-stu-id="68367-119">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="68367-120">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="68367-120">Synchronous methods:</span></span>

  * <span data-ttu-id="68367-121">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="68367-121">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="68367-122">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="68367-122">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="68367-123">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="68367-123">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="68367-124">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="68367-124">Asynchronous methods:</span></span>

  * <span data-ttu-id="68367-125">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="68367-125">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="68367-126">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="68367-126">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="68367-127">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="68367-127">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="68367-128">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="68367-128">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="68367-129">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="68367-129">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="68367-130">Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados.</span><span class="sxs-lookup"><span data-stu-id="68367-130">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="68367-131">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="68367-131">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="68367-132">Implementar filtros de página Razor globalmente</span><span class="sxs-lookup"><span data-stu-id="68367-132">Implement Razor Page filters globally</span></span>

<span data-ttu-id="68367-133">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="68367-133">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="68367-134">No código anterior, `ProcessUserAgent.Write` é um código fornecido pelo usuário que funciona com a cadeia de caracteres do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="68367-134">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="68367-135">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="68367-135">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="68367-136">O código a seguir chama <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para aplicar o `SampleAsyncPageFilter` a apenas páginas no */Movies*:</span><span class="sxs-lookup"><span data-stu-id="68367-136">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="68367-137">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="68367-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="68367-138">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="68367-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="68367-139">Implementar filtros de página Razor substituindo os métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="68367-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="68367-140">O código a seguir substitui os filtros de página assíncronas do Razor:</span><span class="sxs-lookup"><span data-stu-id="68367-140">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="68367-141">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="68367-141">Implement a filter attribute</span></span>

<span data-ttu-id="68367-142">O filtro interno baseado em atributo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtro pode ser subclasse.</span><span class="sxs-lookup"><span data-stu-id="68367-142">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="68367-143">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="68367-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="68367-144">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="68367-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="68367-145">Use uma ferramenta como as ferramentas de desenvolvedor do navegador para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="68367-145">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="68367-146">Em **cabeçalhos de resposta**, `author: Rick` é exibido.</span><span class="sxs-lookup"><span data-stu-id="68367-146">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="68367-147">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="68367-147">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="68367-148">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="68367-148">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="68367-149">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="68367-149">Authorize filter attribute</span></span>

<span data-ttu-id="68367-150">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="68367-150">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68367-151">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68367-151">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="68367-152">Os filtros de página Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Páginas Razor executem código antes e após a execução de um manipulador de Página Razor.</span><span class="sxs-lookup"><span data-stu-id="68367-152">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="68367-153">Os filtros de página Razor são semelhantes aos [filtros de ação de MVC do ASP.NET Core](xref:mvc/controllers/filters#action-filters), exceto que eles não podem ser aplicados aos métodos do manipulador de cada página individual.</span><span class="sxs-lookup"><span data-stu-id="68367-153">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="68367-154">Filtros de página Razor:</span><span class="sxs-lookup"><span data-stu-id="68367-154">Razor Page filters:</span></span>

* <span data-ttu-id="68367-155">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="68367-155">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="68367-156">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="68367-156">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="68367-157">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="68367-157">Run code after the handler method executes.</span></span>
* <span data-ttu-id="68367-158">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="68367-158">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="68367-159">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="68367-159">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="68367-160">O código pode ser executado antes que um método do manipulador seja executado usando o construtor de página ou o middleware, mas somente os filtros de página Razor têm acesso a [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="68367-160">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="68367-161">Os filtros têm um parâmetro derivado [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), que fornece acesso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="68367-161">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="68367-162">Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.</span><span class="sxs-lookup"><span data-stu-id="68367-162">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="68367-163">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68367-163">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="68367-164">Os filtros de página Razor fornecem os métodos a seguir, que podem ser aplicados globalmente ou no nível da página:</span><span class="sxs-lookup"><span data-stu-id="68367-164">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="68367-165">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="68367-165">Synchronous methods:</span></span>

  * <span data-ttu-id="68367-166">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="68367-166">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="68367-167">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="68367-167">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="68367-168">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="68367-168">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="68367-169">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="68367-169">Asynchronous methods:</span></span>

  * <span data-ttu-id="68367-170">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="68367-170">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="68367-171">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="68367-171">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="68367-172">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, não ambas.</span><span class="sxs-lookup"><span data-stu-id="68367-172">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="68367-173">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="68367-173">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="68367-174">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="68367-174">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="68367-175">Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados.</span><span class="sxs-lookup"><span data-stu-id="68367-175">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="68367-176">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="68367-176">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="68367-177">Implementar filtros de página Razor globalmente</span><span class="sxs-lookup"><span data-stu-id="68367-177">Implement Razor Page filters globally</span></span>

<span data-ttu-id="68367-178">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="68367-178">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="68367-179">No código anterior, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) não é necessário.</span><span class="sxs-lookup"><span data-stu-id="68367-179">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="68367-180">Ele é usado na amostra para fornecer informações de rastreamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68367-180">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="68367-181">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="68367-181">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="68367-182">O código a seguir mostra a classe `Startup` completa:</span><span class="sxs-lookup"><span data-stu-id="68367-182">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="68367-183">O código a seguir chama `AddFolderApplicationModelConvention` para aplicar o `SampleAsyncPageFilter` somente às páginas em */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="68367-183">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="68367-184">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="68367-184">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="68367-185">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="68367-185">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="68367-186">Implementar filtros de página Razor substituindo os métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="68367-186">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="68367-187">O código a seguir substitui os filtros de página Razor síncronos:</span><span class="sxs-lookup"><span data-stu-id="68367-187">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="68367-188">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="68367-188">Implement a filter attribute</span></span>

<span data-ttu-id="68367-189">O filtro baseado em atributo interno [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) pode tornar-se uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="68367-189">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="68367-190">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="68367-190">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="68367-191">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="68367-191">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="68367-192">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="68367-192">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="68367-193">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="68367-193">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="68367-194">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="68367-194">Authorize filter attribute</span></span>

<span data-ttu-id="68367-195">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="68367-195">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
