---
title: Métodos de filtro para Páginas Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como criar métodos de filtro para as Páginas Razor no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: a60b17685c6f836de7c0afcc5b89a9894fb8b28f
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447225"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="871a4-103">Métodos de filtro para Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="871a4-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="871a4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="871a4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="871a4-105">Os filtros de página Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Páginas Razor executem código antes e após a execução de um manipulador de Página Razor.</span><span class="sxs-lookup"><span data-stu-id="871a4-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="871a4-106">Os filtros de página Razor são semelhantes aos [filtros de ação de MVC do ASP.NET Core](xref:mvc/controllers/filters#action-filters), exceto que eles não podem ser aplicados aos métodos do manipulador de cada página individual.</span><span class="sxs-lookup"><span data-stu-id="871a4-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="871a4-107">Filtros de página Razor:</span><span class="sxs-lookup"><span data-stu-id="871a4-107">Razor Page filters:</span></span>

* <span data-ttu-id="871a4-108">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="871a4-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="871a4-109">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="871a4-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="871a4-110">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="871a4-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="871a4-111">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="871a4-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="871a4-112">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="871a4-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="871a4-113">Pode haver dependências de Construtor preenchidas pela [injeção de dependência](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="871a4-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="871a4-114">Para obter mais informações, [consulte](/aspnet/core/mvc/controllers/filters#servicefilterattribute) [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)e.</span><span class="sxs-lookup"><span data-stu-id="871a4-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="871a4-115">Embora os construtores de página e o middleware habilitem a execução do código personalizado antes que um método de manipulador seja executado, somente filtros de página Razor habilitam o acesso a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> e à página.</span><span class="sxs-lookup"><span data-stu-id="871a4-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="871a4-116">O middleware tem acesso ao `HttpContext`, mas não ao "contexto da página".</span><span class="sxs-lookup"><span data-stu-id="871a4-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="871a4-117">Os filtros têm um <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parâmetro derivado, que fornece acesso ao `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="871a4-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="871a4-118">Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.</span><span class="sxs-lookup"><span data-stu-id="871a4-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="871a4-119">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="871a4-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="871a4-120">Os filtros de página Razor fornecem os métodos a seguir, que podem ser aplicados globalmente ou no nível da página:</span><span class="sxs-lookup"><span data-stu-id="871a4-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="871a4-121">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="871a4-121">Synchronous methods:</span></span>

  * <span data-ttu-id="871a4-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="871a4-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="871a4-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="871a4-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="871a4-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="871a4-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="871a4-125">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="871a4-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="871a4-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="871a4-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="871a4-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="871a4-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="871a4-128">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="871a4-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="871a4-129">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="871a4-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="871a4-130">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="871a4-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="871a4-131">Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados.</span><span class="sxs-lookup"><span data-stu-id="871a4-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="871a4-132">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="871a4-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="871a4-133">Implementar filtros de página Razor globalmente</span><span class="sxs-lookup"><span data-stu-id="871a4-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="871a4-134">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="871a4-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="871a4-135">No código anterior, `ProcessUserAgent.Write` é um código fornecido pelo usuário que funciona com a cadeia de caracteres do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="871a4-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="871a4-136">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="871a4-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="871a4-137">O código a seguir chama <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para aplicar o `SampleAsyncPageFilter` a apenas páginas no */Movies*:</span><span class="sxs-lookup"><span data-stu-id="871a4-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="871a4-138">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="871a4-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="871a4-139">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="871a4-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="871a4-140">Implementar filtros de página Razor substituindo os métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="871a4-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="871a4-141">O código a seguir substitui os filtros de página assíncronas do Razor:</span><span class="sxs-lookup"><span data-stu-id="871a4-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="871a4-142">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="871a4-142">Implement a filter attribute</span></span>

<span data-ttu-id="871a4-143">O filtro interno baseado em atributo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtro pode ser subclasse.</span><span class="sxs-lookup"><span data-stu-id="871a4-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="871a4-144">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="871a4-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="871a4-145">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="871a4-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="871a4-146">Use uma ferramenta como as ferramentas de desenvolvedor do navegador para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="871a4-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="871a4-147">Em **cabeçalhos de resposta**, `author: Rick` é exibido.</span><span class="sxs-lookup"><span data-stu-id="871a4-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="871a4-148">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="871a4-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="871a4-149">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="871a4-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="871a4-150">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="871a4-150">Authorize filter attribute</span></span>

<span data-ttu-id="871a4-151">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="871a4-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="871a4-152">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="871a4-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="871a4-153">Os filtros de página Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Páginas Razor executem código antes e após a execução de um manipulador de Página Razor.</span><span class="sxs-lookup"><span data-stu-id="871a4-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="871a4-154">Os filtros de página Razor são semelhantes aos [filtros de ação de MVC do ASP.NET Core](xref:mvc/controllers/filters#action-filters), exceto que eles não podem ser aplicados aos métodos do manipulador de cada página individual.</span><span class="sxs-lookup"><span data-stu-id="871a4-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="871a4-155">Filtros de página Razor:</span><span class="sxs-lookup"><span data-stu-id="871a4-155">Razor Page filters:</span></span>

* <span data-ttu-id="871a4-156">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="871a4-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="871a4-157">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="871a4-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="871a4-158">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="871a4-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="871a4-159">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="871a4-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="871a4-160">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="871a4-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="871a4-161">O código pode ser executado antes que um método do manipulador seja executado usando o construtor de página ou o middleware, mas somente os filtros de página Razor têm acesso a [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="871a4-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="871a4-162">Os filtros têm um parâmetro derivado [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), que fornece acesso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="871a4-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="871a4-163">Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.</span><span class="sxs-lookup"><span data-stu-id="871a4-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="871a4-164">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="871a4-164">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="871a4-165">Os filtros de página Razor fornecem os métodos a seguir, que podem ser aplicados globalmente ou no nível da página:</span><span class="sxs-lookup"><span data-stu-id="871a4-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="871a4-166">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="871a4-166">Synchronous methods:</span></span>

  * <span data-ttu-id="871a4-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="871a4-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="871a4-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="871a4-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="871a4-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="871a4-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="871a4-170">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="871a4-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="871a4-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="871a4-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="871a4-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="871a4-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="871a4-173">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, não ambas.</span><span class="sxs-lookup"><span data-stu-id="871a4-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="871a4-174">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="871a4-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="871a4-175">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="871a4-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="871a4-176">Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados.</span><span class="sxs-lookup"><span data-stu-id="871a4-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="871a4-177">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="871a4-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="871a4-178">Implementar filtros de página Razor globalmente</span><span class="sxs-lookup"><span data-stu-id="871a4-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="871a4-179">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="871a4-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="871a4-180">No código anterior, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) não é necessário.</span><span class="sxs-lookup"><span data-stu-id="871a4-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="871a4-181">Ele é usado na amostra para fornecer informações de rastreamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="871a4-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="871a4-182">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="871a4-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="871a4-183">O código a seguir mostra a classe `Startup` completa:</span><span class="sxs-lookup"><span data-stu-id="871a4-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="871a4-184">O código a seguir chama `AddFolderApplicationModelConvention` para aplicar o `SampleAsyncPageFilter` somente às páginas em */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="871a4-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="871a4-185">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="871a4-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="871a4-186">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="871a4-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="871a4-187">Implementar filtros de página Razor substituindo os métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="871a4-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="871a4-188">O código a seguir substitui os filtros de página Razor síncronos:</span><span class="sxs-lookup"><span data-stu-id="871a4-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="871a4-189">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="871a4-189">Implement a filter attribute</span></span>

<span data-ttu-id="871a4-190">O filtro baseado em atributo interno [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) pode tornar-se uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="871a4-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="871a4-191">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="871a4-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="871a4-192">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="871a4-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="871a4-193">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="871a4-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="871a4-194">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="871a4-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="871a4-195">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="871a4-195">Authorize filter attribute</span></span>

<span data-ttu-id="871a4-196">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="871a4-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
